# Java Notes

A personal, hands-on set of notes on **Java** — the language, the JVM, the
standard library, and the ecosystem around them. Written as a documentation site
(MkDocs Material) with Mermaid diagrams.

📖 **Read the notes:** live at **<https://vatsan127.github.io/java_notes/>** —
source under [`docs/`](docs/).

## Project structure

```
java_notes/
├── README.md          # This file
├── CLAUDE.md          # Guidance for Claude Code
├── mkdocs.yml         # Site config (theme, nav, Mermaid)
├── docs/              # All notes (single source of truth)
│   ├── index.md              # Landing page
│   └── stylesheets/extra.css # Full-width layout override
├── .venv/             # Python virtualenv (gitignored)
└── site/              # mkdocs build output (gitignored)
```

## Setup

Create the virtualenv and install MkDocs Material (one-time):

```bash
py -m venv .venv
.venv/Scripts/python.exe -m pip install --upgrade pip mkdocs-material
```

## Run

```bash
# Live dev server with hot reload → http://127.0.0.1:8000/java_notes/
.venv/Scripts/python.exe -m mkdocs serve

# Static build to site/
.venv/Scripts/python.exe -m mkdocs build
```
