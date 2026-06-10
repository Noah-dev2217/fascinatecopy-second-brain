# The Karpathy LLM Wiki Method: Claude Code + Obsidian as a Second Brain

> A walkthrough of Andrej Karpathy's "LLM-maintained wiki" pattern — a markdown-only knowledge base that Claude Code ingests, organizes, links, and queries. No vector DB, no embeddings, no chunking pipeline. ~5 minute setup.

---

## What this actually is

Karpathy posted about using LLMs to build personal knowledge bases for research topics. The idea got serious traction on X because it sidesteps the entire RAG stack — you give Claude Code raw documents, it builds a wiki of cross-linked markdown files, and you query it conversationally.

Two real examples to anchor it:

- **YouTube knowledge system** — 36 video transcripts ingested in one batch. Claude Code built nodes for every tool mentioned (Perplexity, Claude Code, n8n, etc.), every technique (the WAT framework, bypass permissions mode, human review checkpoints), and every concept. Hover any node and you can follow backlinks to every related video. Zero manual relationship building.
- **Personal second brain** — meeting recordings, business initiatives, employees, Q2 priorities, etc. Same structure, different content. Same Claude Code project can be pointed at either.

You can keep these separate or combine them. Either way, the vault becomes a context source that other Claude Code projects (e.g. an executive assistant) can read from.

---

## Why people are calling this a game changer

- **Normal AI chats are ephemeral.** Knowledge dies with the conversation. The wiki makes it compound.
- **No infrastructure.** No vector DB, no embedding model, no chunking pipeline. Literally a folder of markdown files.
- **Token efficiency.** One X user reported a 95% drop in token usage by replacing 383 scattered files + 100 meeting transcripts with a compact wiki.
- **No repo to clone.** You paste Karpathy's idea into Claude Code and tell it to build the thing. Karpathy explicitly left the prompt vague so it customizes per project. People are calling this the 2026 software pattern — give a high-level idea, let the agent build it.

---

## The architecture

It's just two top-level folders inside your vault:

```
my-wiki/
├── raw/          ← you drop source documents here
└── wiki/         ← Claude Code builds and maintains this
    ├── index.md
    ├── log.md
    ├── hot.md    ← optional cache (see below)
    └── [generated pages]
```

- **`raw/`** — every source document goes here (articles, PDFs, transcripts, meeting notes).
- **`wiki/`** — Claude Code reads `raw/`, decides what pages to create, how to link them, and writes everything in `wiki/`.
- **`index.md`** — the master list. Auto-organized into sections like Tools, Techniques, Concepts, Sources, People, Comparisons.
- **`log.md`** — operation history. Every ingest gets logged here.
- **`CLAUDE.md`** — at the root. Explains the schema, search behavior, update rules.

### Optional subfolders inside `wiki/`

By default Claude Code creates folders like `analysis/`, `concepts/`, `entities/`, `sources/`. Karpathy actually prefers flat (no subfolders, less over-organizing). For some projects flat works; for the YouTube vault, subfolders made more sense. Tell Claude Code what you want once it asks.

---

## Setup (5 minutes)

### 1. Install Obsidian

[obsidian.md](https://obsidian.md) — download for your OS. Free. Not strictly required (the wiki is just markdown), but the graph view makes the relationships visible.

### 2. Create a vault

Open Obsidian → Manage Vaults → Create new → name it (e.g. `demo-vault`) → pick a location.

### 3. Open Claude Code in that vault

Open the vault folder in your terminal or VS Code, run `claude`.

### 4. Paste Karpathy's prompt

Grab the LLM wiki idea from Karpathy's gist (the followup post with the architecture explanation). Paste it into Claude Code, then add a wrapper instruction:

> You are now my LLM wiki agent. Implement this exact idea file as my complete second brain. Guide me step by step. Create the `CLAUDE.md`, schema, index, log, and folder structure.

Claude Code will scaffold `raw/`, `wiki/`, `CLAUDE.md`, `index.md`, `log.md`, and a few starting subfolders. It'll then prompt you to drop your first source into `raw/` and tell it to ingest.

### 5. Set the project's intent up front

Before ingesting anything, tell Claude Code what this vault is *for*:

> This project is my personal AI research vault. I'll be dropping articles, papers, and notes here. Help me keep it organized so I can query it later.

Or:

> This is my executive-assistant context vault. I'll drop in meeting recordings, ClickUp summaries, and business notes. Optimize the structure for querying business context.

The intent shapes how Claude Code names folders, what it considers a "page worth creating," and how aggressively it links.

---

## Ingesting your first source

### Method A — Obsidian Web Clipper (for articles)

Install the **Obsidian Web Clipper** Chrome extension. In the extension options, set the default save location to `raw/` (it defaults to `clippings/`). On any article, click the extension → save to your vault. Done — it lands in `raw/` as clean markdown.

### Method B — Drop a file in `raw/` directly

PDFs, transcripts, anything markdown-able. Then tell Claude Code:

> I just dropped `[filename]` into raw. Please ingest it.

### What happens during ingest

Claude Code reads the source, decides how many wiki pages to create (one article might generate 5–25 pages), and writes them with cross-links. It usually asks clarifying questions first: what to emphasize, how granular to go, what your focus is. Answer those — the project gets sharper the more context you give.

Time estimate: ~10 minutes for one rich article (Karpathy's AI 2027), ~14 minutes for 36 YouTube transcripts in batch.

---

## The graph view (worth watching live)

Switch Obsidian to graph view during an ingest. You'll see hubs form in real time — people, organizations, concepts, sources — and individual nodes wire themselves together. After one article you can already see structure (a "main source" node with everything radiating out). After several articles, cross-article hubs start emerging (e.g. "compute scaling" referenced by three different sources).

Every page is clickable. Backlinks just work. No setup.

---

## The `hot.md` cache (for assistant-style projects)

For vaults that get queried constantly (like an executive assistant context source), add a `hot.md` file inside `wiki/`. It's a small cache (~500 words) of the most recent things you mentioned or talked about — what's currently top of mind.

The assistant project reads `hot.md` first. Often that's enough; it doesn't need to crawl the wiki at all.

For static reference vaults (like the YouTube knowledge system), skip this — there's no "recent context" to cache.

---

## Linting (LLM health checks on the wiki)

Karpathy runs periodic LLM passes over the wiki to:

- Find inconsistent data
- Fill missing data via web search
- Suggest interesting connections worth turning into new pages
- Flag confusion ("I don't fully understand X, can you give me more sources?")

Run this daily, weekly, or on demand. Keeps the wiki scalable and structurally clean.

---

## Pointing other projects at the vault

Once a vault exists, any other Claude Code project can read from it. In the consumer project's `CLAUDE.md`:

```
## Wiki path
~/Documents/HerkBrain/

When you need context about me or my business that you don't already have:
1. Read the hot cache first
2. Read the index
3. Read the relevant domain subindex
4. Then search through the wiki itself

Don't read from the wiki unless you actually need it. For [list routine tasks], skip the wiki entirely.
```

Switching from "stuff context files into every project" to "point projects at one wiki" measurably drops token usage in the consumer project. The wiki only gets read when it's needed.

---

## Does this kill RAG?

No — but kind of yes for personal-scale knowledge.

| | LLM Wiki | Semantic Search RAG |
|---|---|---|
| **Retrieval** | Indexes + link-following | Similarity search on embeddings |
| **Understanding** | Real relationships (explicit links) | "These chunks seem similar" |
| **Infrastructure** | Markdown files | Embedding model + vector DB + chunking pipeline |
| **Cost** | Free (just tokens at query time) | Ongoing compute + storage |
| **Maintenance** | Run a lint, add articles | Re-embed when things change |
| **Scale ceiling** | Hundreds of pages with good indexes | Millions of documents |

**Verdict:** for personal knowledge, research vaults, business context — wiki wins. Cheaper, smarter, easier to maintain. For enterprise-scale (millions of docs), traditional RAG or knowledge graphs (LightRAG, etc.) still wins on cost and retrieval speed.

This is the state as of April 2026 — current models and current pricing. The break-even keeps shifting.

---

## TL;DR

1. Install Obsidian, create a vault
2. Open Claude Code in it
3. Paste Karpathy's LLM wiki idea + a wrapper prompt
4. Tell Claude Code what the vault is *for*
5. Drop sources in `raw/`, tell it to ingest
6. Query conversationally; lint periodically; point other projects at the vault

No vector DB. No embeddings. No chunking. Just markdown files Claude Code maintains for you.
