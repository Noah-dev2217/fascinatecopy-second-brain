# CLAUDE.md — Vault Schema

This file is the **schema** for your personal second brain. It tells Claude Code how this vault is organized, what conventions to follow, and what operations to support.

You should read this file. So should any Claude Code session you open in this vault. The conventions here are intentionally explicit — they're what keeps a wiki useful over months and years instead of rotting into a junk drawer.

---

## What this vault is

A personal knowledge base built on the **Karpathy LLM Wiki pattern**. Two files in this vault explain the pattern in full:

- `karpathy-llm-wiki-idea.md` — the canonical spec (the source of truth)
- `karpathy-llm-wiki-second-brain.md` — a walkthrough with concrete examples

Read those first if you haven't. Everything below assumes you understand the basic shape.

---

## Architecture (three layers)

| Layer | Folder | Who owns it |
|---|---|---|
| **Raw sources** | `raw/` | The user. Immutable. Claude Code reads but never modifies. |
| **The wiki** | `wiki/` | Claude Code. Creates, links, and maintains pages. |
| **The schema** | `CLAUDE.md` (this file) | Co-evolved by user + Claude Code over time. |

### Vault root structure

```
your-vault/
├── CLAUDE.md                                ← This file
├── karpathy-llm-wiki-idea.md                ← Canonical spec
├── karpathy-llm-wiki-second-brain.md        ← Walkthrough
├── raw/                                     ← Source documents (immutable)
│   └── assets/                              ← Images, attachments
└── wiki/                                    ← LLM-maintained knowledge
    ├── index.md                             ← Catalog of all pages
    ├── log.md                               ← Operation history
    ├── hot.md                               ← Recent-context cache
    ├── concepts/                            ← Transferable patterns
    ├── entities/                            ← People, organizations, tools
    ├── projects/                            ← Specific projects
    ├── sources/                             ← Manifest pages (one per source)
    └── workflows/                           ← Commands, scripts, processes
```

---

## Operations

There are exactly three operations Claude Code performs on this vault.

### 1. Ingest

User drops a new source into `raw/<folder>/` and triggers ingest. Claude Code:

1. Reads every file in the folder (recursively). Skips unreadable binaries but notes them in the source manifest.
2. Treats the entire folder as ONE source, ONE ingest, ONE log entry.
3. Asks clarifying questions in ONE batch BEFORE writing anything (or says explicitly if none are needed).
4. Decides page granularity per the Conventions section below.
5. Writes wiki pages — both new ones AND updates to existing pages this source touches.
6. Looks for emergent cross-project patterns. Creates or updates concept pages that abstract shared patterns across multiple projects.
7. Updates `wiki/index.md` with new entries.
8. Appends entry to `wiki/log.md` in the format: `## [YYYY-MM-DD] ingest | <project name>`
9. Updates `wiki/hot.md` to reflect recent context.
10. Commits with message: `ingest: <project name>`.
11. Reports back: pages CREATED, pages UPDATED, new concept pages, granularity decisions, anything noticed but not acted on.

### 2. Query

User asks a question against the wiki. Claude Code:

1. Reads `wiki/hot.md` first — often enough on its own.
2. If not, reads `wiki/index.md` to find relevant pages.
3. Reads those specific pages — does not grep the whole vault unnecessarily.
4. Only searches `raw/` if the wiki doesn't have the answer (raw is verbose; the wiki is distilled).
5. Synthesizes an answer with citations.
6. **Optional but valuable**: high-quality query results get filed back into the wiki as new pages, so explorations compound the same way ingests do.

### 3. Lint

Periodically (weekly or on demand), Claude Code health-checks the wiki:

- Contradictions between pages
- Stale claims superseded by newer sources
- Orphan pages with no inbound links
- Important concepts mentioned but lacking their own page
- Missing cross-references
- Data gaps that could be filled by a web search

Reports findings and asks user which to act on. Never silently rewrites.

---

## Ingest trigger (shorthand)

When user says `ingest raw/<folder>` (optionally with a one-line description), run the full ingest protocol above. No long prompt needed — the protocol lives here.

If the user gives a description, use it as context. If not, infer purpose from the files and state the inference in the report so the user can correct it.

---

## Conventions

These are the rules that govern wiki pages. They were learned through real ingests. **Each one solves a real problem. Don't drop them without understanding why.**

### Granularity & structure

- **Granularity: Balanced.** Page when substantial or recurring. Split when unskimmable. Aim for 10–30 pages per source as a sanity range; outside that range, stop and discuss with the user before writing.
- **Linking: Moderate.** Link the first meaningful mention of any entity/concept/tool. Dangling links (links to pages that don't exist yet) are allowed as signals for future creation.
- **Folder structure: subfoldered.** Use the standard subfolders (`concepts/`, `entities/`, `projects/`, `sources/`, `workflows/`). Per-project subfolders allowed when a project has substantial internal structure (e.g. pipeline stages, agent roles). Don't force a per-project subfolder on every project.

### Frontmatter

Every wiki page gets YAML frontmatter with at minimum:
- `status` (e.g. `in-progress`, `done`, `archived`, `deprecated`, `planning`)
- `source_file` (path to the raw file that produced this page)

Additional fields encouraged: `tags`, `date`, `source_count` for query convenience via Dataview plugin.

### Source pages

- **One source page per ingested folder**, named after the project. Acts as a manifest pointing back to the raw files.
- **Name collision rule**: when a project page and its source page share a name, suffix the source page with `-source`. The project page is canonical (synthesized knowledge); the source page is the manifest.

### Logging

Format: `## [YYYY-MM-DD] <operation> | <title>`

Example: `## [2026-06-10] ingest | LinkedIn Lead Capture v1.1`

This makes the log grep-able with `grep "^## \[" wiki/log.md`.

### Source contradictions vs upstream corrections

- **Source contradictions** — when a raw source contradicts itself internally — capture under `## Known inconsistencies in the source` on the source page. Raw is immutable; the contradiction is a fact about the artifact. Flag as a candidate `lint` item.
- **Upstream corrections** — when source notes correct an EXTERNAL (non-ingested) document — capture under `## Corrections to the upstream blueprint` on the project page. Distinct from internal contradictions.

### Watch items

Operational gotchas (silent-failure risks, hidden coupling, things that break if changed) get flagged under `## Watch items` on BOTH the project page AND the source page, so they surface from either entry point.

A watch item is specifically a *gotcha*. Feature gaps and known limitations belong under `## Known limitations` instead.

### Convention retrofits (meta-rule)

Whenever a Convention is added or changed, audit all existing pages that fall under it. If any pages don't conform, retrofit them in a separate commit titled `retrofit: <description>` within the same session. **Never leave convention debt.**

### Scope discipline on git operations

Ingest operations produce exactly **one commit** (the ingest commit). Do not rebase, squash, split commits, rewrite history, or perform any git operation beyond `git add` and `git commit` during an ingest. If repo hygiene issues are noticed (history that could be cleaner, files that shouldn't be committed, etc.), surface them as observations in the report — DO NOT act on them.

### Secret handling

When ingesting code projects containing credentials:
- Surface them in the report under `## Secrets detected` with file:line references.
- Do NOT remove them from the raw source (raw is immutable).
- Do NOT include the secret VALUES in any wiki page — only that a secret exists at `<file>:<line>`.
- The user handles rotation outside the vault.

### Command grouping

When a project has multiple related slash commands or scripts that share lifecycle (e.g. `build`/`resume`/`status` acting on the same artifact), group them into lifecycle pages. When commands are unrelated, keep them separate.

### Supersession (versioned sources)

When a newer version of a project is ingested (e.g. v1.2 of a project where v1.1 already exists):
- Do NOT delete or modify the old source page.
- Create the new source page.
- Update the project page to: (a) point at the new source as canonical, (b) preserve the old source as "previous version", (c) note what changed between versions.
- Both source pages remain in the wiki — supersession, not replacement.

### Deprecation

When a source is marked deprecated:
- Project page frontmatter: `status: deprecated`.
- Project page opens with `## Deprecation` section near the top, containing: deprecation date, stated reason (verbatim), and "Replaced by:" field if applicable.
- Project page ends with `## Patterns worth salvaging` listing reusable architectural ideas, prompt patterns, or workflow structures from the deprecated project.
- Concept/entity/tool pages this source touches still get updated normally — deprecation of the project doesn't deprecate the concepts it used.
- `hot.md` mentions the ingest but does NOT elevate the deprecated project to active context.

### Source gaps

When a source file references another file that is NOT present in the raw folder, capture under `## Source gaps` on the source page (not the project page).

Format: `` - `<filename>` — referenced by <citing-file>, absent from folder. Notes: <what partial coverage exists, if any>. ``

Do NOT create stub wiki pages for missing files. Do NOT fabricate their content. The gap itself is the record.

### Verify before flagging

When the user claims something appears in the raw source (errors, inconsistencies, contradictions, references), verify against the actual raw file before flagging it on a wiki page. If the claim doesn't match the source, say so and ask — do not invent a flag to satisfy the request.

---

## Co-evolution

These conventions are a **seed**, not a finished product. They were learned through ingesting real projects and will need to evolve to match how you actually work.

When you find yourself wishing the wiki worked differently:

1. Tell Claude Code what you want changed.
2. Lock it into this `CLAUDE.md` under Conventions.
3. The convention-retrofit meta-rule will keep your wiki consistent as it evolves.

Add conventions, modify them, remove ones that don't fit your work. This file is yours.
