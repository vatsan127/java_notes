# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this repo is

A personal, hands-on **learning notes** repository for Java. The learner is a
working Java developer building up a durable, searchable set of notes on the
language, the standard library, the JVM, and the wider ecosystem. This is not
production code — clarity and explanation beat cleverness or optimization.

- Docs: notes written as Markdown under `docs/`, rendered by **MkDocs Material**.
- Approach: concept → small runnable snippet → observation → next item.
- Scope: core Java, JVM internals, concurrency, collections, I/O, common
  frameworks and tooling — added as the learner works through them.

Overview: [`README.md`](./README.md).

## Project structure

```
java_notes/
├── README.md                 ← repo intro (structure + setup + run)
├── CLAUDE.md                 ← this file
├── mkdocs.yml                ← site config: theme, nav, Markdown extensions
├── .gitignore                ← excludes .venv/, site/, .env
├── docs/                     ← single source of truth for all notes
│   ├── index.md              ← landing page
│   └── stylesheets/extra.css ← full-width layout override
├── .github/workflows/        ← deploy-docs.yml: auto-build + publish to GitHub Pages
├── .venv/                    ← gitignored; MkDocs Material lives here
└── site/                     ← gitignored; mkdocs build output
```

Future folders — create only when actually used, never as empty placeholders:

- `code/` — Java source snippets referenced from notes.
- `images/` — screenshots, if any are ever needed.

## Commands

Assume the venv at `.venv/`:

```bash
# Live dev server, hot reload → http://127.0.0.1:8000/java_notes/
.venv/Scripts/python.exe -m mkdocs serve

# Static build to site/
.venv/Scripts/python.exe -m mkdocs build

# Strict build (fail on warnings — run before publishing)
.venv/Scripts/python.exe -m mkdocs build --strict
```

Recreate the venv if `.venv/` is lost:

```bash
py -m venv .venv
.venv/Scripts/python.exe -m pip install --upgrade pip mkdocs-material
```

## Deployment

The site auto-deploys to GitHub Pages via `.github/workflows/deploy-docs.yml`.
Every push to `master` triggers a GitHub Actions run that installs
`mkdocs-material`, runs `mkdocs build --strict`, and publishes the result to
`https://vatsan127.github.io/java_notes/`. No manual step and no `gh-pages`
branch — the built site is uploaded straight to Pages as an artifact. Work on a
feature branch, then merge to `master` to publish. The workflow can also be run
by hand from the repo's Actions tab (`workflow_dispatch`).

One-time setup (done once in the GitHub repo UI): Settings → Pages → Build and
deployment → Source = "GitHub Actions". The `github-pages` environment allows
deploys from the default branch (`master`) out of the box.

## How to collaborate here

How we work each session:

1. Explain the concept first (everyday analogies welcome).
2. Show a small, runnable Java snippet.
3. Discuss what it does, what surprised us, and the edge cases.
4. Move to the next item.

Default to small, working examples over large scaffolds. Prefer standard-library
solutions over pulling in dependencies unless a note is specifically about a
framework or library.

## Notes file conventions

All notes live under `docs/` as `.md` files.

- **Filename:** `NN-topic-name.md` (zero-padded, kebab-case), continuing the sequence.
- **Title:** `# Title Case Title` — plain, no "Topic N:" prefix and no number
  (the `NN-` lives in the filename only).
- **Sections:** `## Heading`, `### Sub-heading`. No ALL CAPS headings.
- **Closing:** end each note with a recap section (e.g. "Key Takeaways").
- **Lists:** `-` for bullets; 4-space indent for sub-bullets.
- **Code blocks:** fenced with ` ```java ` (or the appropriate language tag).
- **Diagrams:** Mermaid (` ```mermaid `) for flowcharts and relationships. **No images.**
  Inside Mermaid node labels use plain text + HTML (`<br/>`, `<i>`, `•`) — Markdown
  `**bold**`/`*italic*` does not render reliably there.
- **Admonitions:** `!!! tip "Think of it as"` for everyday analogies;
  `!!! note`, `!!! example`, `!!! warning`, `!!! info` as appropriate.
- **Tables:** Markdown tables for side-by-side comparisons (no ASCII alignment).
- **Cross-links:** relative paths, e.g. `[Collections](02-collections.md)`.

Tone is professional and explanatory — teaching voice is fine, slang is not.

## Things to avoid

- Don't commit secrets. Anything sensitive belongs in `.env` (gitignored).
- Don't create new Markdown docs (design notes, summaries, plans) unless asked.
- Don't create empty placeholder folders before they are actually needed.
- Don't reformat or restructure `README.md` without being asked — it reflects the
  learner's voice.

## Keep this file current

When you make a structural change to the repo, **register it in this file in the
same change** — treat `CLAUDE.md` as part of the deliverable. Structural changes
include: adding/removing/renaming/moving files or folders (update the structure
tree); changing the docs format or conventions; changing the MkDocs setup, venv
recipe, or commands. Don't reformat unrelated sections while doing so.
