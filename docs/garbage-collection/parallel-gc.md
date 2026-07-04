# Parallel GC

The classic **throughput collector**. Simple, aggressive, and effective when
raw work-per-CPU matters more than tail latency.

!!! info "Default in Java 5 – Java 8"
    On server-class JVMs, Parallel GC (a.k.a. the *Throughput Collector*) was
    the default from **Java 5 through Java 8**. From **Java 9 onward, G1GC is
    the default** ([JEP 248](https://openjdk.org/jeps/248)) — on modern JDKs
    you now opt into Parallel explicitly with `-XX:+UseParallelGC`.

## How it works

Parallel GC uses the [standard generational heap](index.md#heap-memory-architecture)
— young and old — laid out as **two contiguous regions** (the distinguishing
structural difference from G1's regionised heap). Both are collected
stop-the-world:

- **Young collection:** all application threads stop. Multiple GC threads
  (a count derived from the machine's core count — see
  `-XX:ParallelGCThreads` below) copy live objects from Eden to a Survivor
  space in parallel. Fast, but stop-the-world.
- **Old collection ("Full GC"):** all application threads stop. Multiple GC
  threads compact the entire old generation **in place**. This can take
  **seconds** on a multi-GB heap — the main downside.

There is no concurrent phase. The collector's whole strategy is "do lots of
GC work in parallel while the app is paused, then get out of the way."

## Requests under Parallel GC

Two pause profiles show up in production. **Minor GC** is short and (mostly)
painless. **Full GC** compacts the entire old generation stop-the-world —
on a large heap this can freeze every in-flight request for seconds.

```mermaid
sequenceDiagram
    autonumber
    participant C1 as Client A
    participant C2 as Client B
    participant App as App threads
    participant Eden
    participant Old
    participant GC as GC threads

    C1->>App: GET /orders/123
    App->>Eden: allocate
    App-->>C1: 200 OK (12 ms)
    C2->>App: GET /users/42
    App->>Eden: allocate
    App-->>C2: 200 OK (14 ms)

    Note over Eden: Eden full
    rect rgb(255, 235, 220)
        Note over App,GC: STW: minor GC (~50 ms)<br/>any in-flight request pauses here
        GC->>Eden: copy live to Survivor
        GC->>Old: promote aged survivors
    end

    C1->>App: GET /orders/456
    App-->>C1: 200 OK (13 ms)

    Note over Old: Old fills after many minor GCs
    rect rgb(240, 120, 120)
        Note over App,GC: STW: FULL GC (~3 s) — every request stalls
        C2->>App: GET /users/42
        GC->>Old: mark + compact<br/>entire old generation
    end

    App-->>C2: 200 OK (3.1 s — timeout risk)
```

Notice the two red bands: the small one is a minor GC — bounded and
predictable — but the darker one is the full GC, and it stops **every**
thread until the entire old generation has been compacted.

## When to use it

- Batch jobs, data pipelines, offline processing — anywhere throughput
  matters more than tail latency.
- Small heaps (< 4 GB) where a full GC is still fast enough.
- Not for interactive services with tight p99 SLAs.

## Fundamental tuning parameters

| Flag | What it controls | Typical use |
|------|------------------|-------------|
| `-XX:+UseParallelGC` | Selects the collector. | Explicit opt-in on Java 9+. |
| `-Xms<size>` | Initial heap size. | Set equal to `-Xmx` on servers to skip resize pauses. |
| `-Xmx<size>` | Maximum heap size. | The single most impactful flag — start here. |
| `-XX:ParallelGCThreads=<n>` | GC worker thread count. | Defaults to `ncores` on small machines, `≈ 5/8 × ncores` on machines with more than 8 hardware threads. Cap it in shared containers. |
| `-XX:MaxGCPauseMillis=<ms>` | Target pause time (soft goal). | Parallel shrinks young gen to try to hit this — often at the cost of throughput. |
| `-XX:GCTimeRatio=<n>` | Target ratio of app time to GC time as `n:1`. | `19` → aim for ≤ 5 % GC time. |
| `-XX:NewRatio=<n>` | Old-to-young ratio as `n:1`. | `2` → young is 1/3 of the heap. |

!!! tip "Think of it as"
    A big garbage truck that comes on a schedule and blocks the whole street
    while it works. Efficient per bag collected, disruptive to traffic.

## Key Takeaways

- Parallel GC prioritises **throughput** — total useful work per CPU — over
  individual pause length.
- Every collection is stop-the-world; a **full GC** on a large heap can pause
  the app for seconds.
- Was the **default from Java 5 through Java 8**; still a fine choice for
  batch and background workloads on Java 9+ if you set `-XX:+UseParallelGC`.
- Tune `-Xmx` and `-Xms` first; reach for `MaxGCPauseMillis` or
  `GCTimeRatio` only after that.
