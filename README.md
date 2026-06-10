# FascinateCopy Second Brain

A personal knowledge base that grows with you — built on the **Karpathy LLM Wiki pattern** (Obsidian + Claude Code, markdown only, no vector database, no embeddings, no infrastructure).

Built for the FascinateCopy team. Each person runs their own independent vault.

---

## What you'll have at the end

A folder on your computer that:

- Holds every project, brief, workflow, research note, and document you want to remember
- Cross-links them automatically as you add new sources
- Lets you query your own work conversationally instead of digging through files
- Gets smarter the more you feed it
- Can be pointed at by any Claude Code project so it knows about *you* — your preferences, your past decisions, your patterns

The pattern was created by Andrej Karpathy. We adapted it for our team's workflow after testing it on a real vault (5 projects, 100+ wiki pages, 14+ conventions, one real security incident handled). What you're getting here is the working version — not the version that took us a day of trial and error to learn.

---

## Who this is for

- **Jon** — you're going to follow `INSTALL.md` step by step, then `FIRST_INGEST.md`. About 30 minutes total. The terminal stuff might look intimidating but every command is copy-paste ready. If you get stuck, the troubleshooting section in `INSTALL.md` covers the common errors.
- **Julia** — you can probably skim `INSTALL.md` and run the commands quickly, then read `template/CLAUDE.md` carefully because that's where the actual interesting design lives. The conventions in there were learned through real ingests, not designed in the abstract.

After you're set up, customize your vault to fit how you work. The conventions are a starting seed, not a cage.

---

## The 30-minute happy path

Open these three files in order. Don't skip ahead.

1. **`INSTALL.md`** — install prerequisites and create your vault (~15 minutes)
2. **`FIRST_INGEST.md`** — drop in your first source and watch the wiki form (~15 minutes)
3. **`template/CLAUDE.md`** — read this once you have a working vault, so you understand the conventions

---

## What's in this repo

```
fascinatecopy-second-brain/
├── README.md           ← you are here
├── INSTALL.md          ← step-by-step install
├── FIRST_INGEST.md     ← hand-held first ingest
├── template/           ← files that get copied into your vault
│   ├── CLAUDE.md
│   ├── karpathy-llm-wiki-idea.md
│   └── karpathy-llm-wiki-second-brain.md
└── LICENSE
```

---

## Why this isn't just RAG / NotebookLM / ChatGPT projects

Most AI-with-documents systems re-derive knowledge from scratch on every query. They retrieve chunks of raw text and synthesize from those chunks. Nothing accumulates between queries.

This pattern is different. The wiki is **persistent** — Claude Code reads your sources once, builds linked summary pages, and maintains them over time. When you add a new source, it doesn't just index it — it integrates it into the existing wiki, updating entity pages, revising topic summaries, flagging where new data contradicts old claims.

After a few months of use:
- The cross-references already exist
- The contradictions are already flagged
- The synthesis already reflects everything you've read
- Other Claude Code projects can read this vault and get personalized context about you without you re-explaining yourself every chat

No vector database. No embeddings. No infrastructure. Just markdown files and a disciplined Claude Code session.

---

## Questions

If you get stuck during install, check `INSTALL.md`'s troubleshooting section first. If you're still stuck, ask Rinoah.

If you have questions about *how to use* the vault after install (what to ingest, how to query, how to customize), ask Rinoah and we'll start building out the docs based on real questions.
