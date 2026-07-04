# Java Notes

A personal, hands-on set of notes on **Java** — the language, the JVM, the
standard library, and the ecosystem around them.

!!! tip "Think of it as"
    A lab notebook, not a textbook. Each topic explains one idea in plain
    language, with small runnable snippets and diagrams where they help.

## Where to start

New here? Work through the topics in order — they build on each other.
Notes will appear here as they're written.

## Topics

### [Garbage Collection](garbage-collection/index.md)

Generational GC concepts, the two collectors you'll meet first in production,
and the tuning flags that actually matter.

- [Parallel GC](garbage-collection/parallel-gc.md) — throughput collector,
  default in **Java 5 – 8**.
- [G1GC](garbage-collection/g1gc.md) — pause-time collector, default since
  **Java 9**.
