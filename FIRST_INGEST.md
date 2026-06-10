# FIRST_INGEST.md — Your first source

By now you have a working vault: `CLAUDE.md` at the root, `raw/` and `wiki/` folders scaffolded, git initialized, Claude Code running.

This guide walks you through your first ingest. **About 15 minutes.**

The goal: feed Claude Code your first real source and watch the wiki form. Don't worry about choosing the perfect first source — you'll iterate.

---

## Step 1 — Pick a starter source

The first source you ingest becomes the template for how Claude Code handles every future ingest. Pick well.

### Good first sources

- A project you've worked on recently and remember clearly (so you can spot if the agent gets it wrong)
- Something with **multiple entities, concepts, and tools** so the graph forms real hubs (a project that touches at least one other team member, at least one tool, and at least one methodology)
- 3–10 files total. Not a single file. Not a 500-file codebase.
- Pure markdown if possible; `.docx` and `.pdf` work but extract less cleanly

### Bad first sources

- A single short note (graph won't form)
- A huge codebase (overwhelms the first ingest with noise)
- Something you barely remember (you can't verify the agent's read)
- Something you might want to keep private later (start with non-sensitive work)

### Recommendations by role

**Jon**: a client brief or strategy doc you've recently written. It'll have client info, methodology references, deliverables — good cross-linking potential.

**Julia**: a technical project you led — codebase plus its design docs. Mix of code and prose, plenty of tools and concepts.

---

## Step 2 — Drop the source files into `raw/`

In PowerShell:

```powershell
cd C:\YourBrain
mkdir raw\<your-project-folder-name>
```

Use a clean folder name — lowercase, hyphens for spaces, no special characters. Examples: `client-x-q3-brief`, `internal-tooling-refactor`.

Then either drag the source files into `raw\<your-project-folder-name>\` using File Explorer, or copy from PowerShell:

```powershell
copy C:\path\to\original\files\* raw\<your-project-folder-name>\
```

Verify they landed:

```powershell
dir raw\<your-project-folder-name>
```

---

## Step 3 — Open Obsidian's graph view

Before triggering the ingest, open Obsidian and switch to the graph view (left sidebar → graph icon, or `Ctrl+G`).

Right now the graph should be nearly empty — just the three template files and the empty wiki stubs. **This is your baseline.**

Keep the graph view visible during the ingest. You'll watch nodes appear and connect in real time. It's not just satisfying — it's how you'll know the ingest is working.

### Filter raw sources out of the graph (optional, recommended)

By default, Obsidian shows all markdown files including the raw sources. To see only your wiki:

1. Graph view → settings icon (top-right or in a sliders icon)
2. Find the **Filters** section → **Search** field
3. Type: `-path:raw`
4. Click the small save icon at the bottom to make this the default

Now the graph only shows wiki content, which is what you actually care about.

---

## Step 4 — Trigger the ingest

In your Claude Code session, paste this:

```
ingest raw/<your-project-folder-name> — <one-line description of what this project is>
```

Examples:

```
ingest raw/client-x-q3-brief — Q3 marketing strategy brief for Client X, includes audience research, positioning, channel mix, and KPI plan.
```

```
ingest raw/internal-tooling-refactor — Refactor of our internal report generation tool from Python scripts to a proper CLI. Includes new architecture, migration guide, and the deprecated v1 code for reference.
```

That's the whole prompt. **The full ingest protocol lives in `CLAUDE.md`** — Claude Code reads it and runs the protocol without you needing to specify steps.

---

## Step 5 — Answer clarifying questions (if any)

Claude Code will likely ask a few clarifying questions before writing anything. Common ones:

- "How granular should the pages be?" → Default to "balanced" unless you have a strong opinion.
- "Should I create per-project subfolders inside wiki/?" → Yes for projects with multiple phases or components; no for flat projects.
- "Do you want to include [secondary content like meeting notes]?" → Your call.

**If you don't know, say so.** Tell Claude Code "I'm not sure — give me your best default and we'll refine if needed."

Whatever you say here gets baked into `CLAUDE.md` as precedent for future ingests, so future you doesn't have to re-decide every time.

---

## Step 6 — Watch the ingest happen

While Claude Code reads and writes, keep the graph view visible. You should see:

- New nodes appearing in the wiki cluster
- Edges forming between nodes
- Hub nodes (frequently referenced entities, concepts, tools) growing larger as more pages link to them

Time estimate: 5–15 minutes depending on source size. Don't sit and stare — go grab coffee, come back.

---

## Step 7 — Read the report

When Claude Code finishes, it'll print a report. Look for:

- **Pages CREATED** (with file paths) — should be roughly 10–25 for a typical first source
- **Pages UPDATED** — for your first ingest, this will be near-zero (no existing pages to update). For subsequent ingests, this list is the cross-project compounding signal.
- **Decisions made about granularity/linking** that aren't in `CLAUDE.md` yet — these are candidates to promote to convention.
- **Anything noticed but not acted on** — files that look ambiguous, possible secrets, missing references, etc.

Open 2–3 of the created pages in Obsidian and skim them:
- Do they read like useful reference notes, or are they bloated/empty?
- Are the links actually connecting the right things?
- Does anything feel wrong?

If something feels wrong, **tell Claude Code now, before you ingest a second source.** Whatever pattern got established here becomes the template.

---

## Step 8 — Commit

If the ingest looks good, Claude Code will commit automatically with message `ingest: <project name>`. Verify:

```powershell
git log --oneline
```

You should see at least two commits: `scaffold: vault skeleton` and `ingest: <project name>`.

---

## Step 9 — Open `wiki/hot.md` and `wiki/index.md`

These are your two most important files going forward.

- **`hot.md`** is the recent-context cache. Other Claude Code projects will read this first when they need to know what's currently top-of-mind for you. After your first ingest, it should mention the project you just ingested.

- **`index.md`** is the catalog of every wiki page. As you ingest more sources, this becomes how you (and any Claude Code session) navigate the wiki. Skim it now — note the structure.

---

## What's next

**You have a working second brain with one source ingested.** Three things to do over the next few weeks:

### 1. Ingest more sources

The wiki compounds with each source. The cross-project linking only kicks in after the second source. To see it work, your next ingest should touch at least one tool or concept already in the wiki.

Use the same one-line trigger: `ingest raw/<folder> — <description>`.

### 2. Query your wiki

When you're working on something and a question comes up — *"how did I handle X last time?"*, *"what were the deliverables for the Y project?"* — open Claude Code in the vault and ask:

```
Look in the wiki and tell me what we know about <X>. If there are connections to <Y> worth flagging, mention them.
```

This habit is the actual payoff. Stop re-searching the same things. Ask the wiki.

### 3. Customize `CLAUDE.md` to fit how you work

The conventions in `CLAUDE.md` are a starting seed. As you ingest more sources and find patterns you want handled differently, tell Claude Code, then lock the change into `CLAUDE.md` as a new convention.

The convention-retrofit meta-rule means your existing pages stay consistent as the schema evolves.

---

## Common issues

### "The agent created way too many pages / too few pages"

Granularity is dialed in via the Conventions section of `CLAUDE.md`. Tell Claude Code your preference and ask it to update the Granularity convention.

### "It didn't update existing pages — only created new ones"

For the first ingest this is expected (no existing pages to update). For later ingests, if Pages UPDATED is empty when it shouldn't be, the cross-project linking failed. Tell Claude Code: "You didn't update existing pages this source should touch. Re-check the index for related entities and update them."

### "The agent did extra things I didn't ask for (rewrote git history, edited files outside the ingest)"

This shouldn't happen — `CLAUDE.md` has a "Scope discipline" convention that limits ingests to a single commit. If it happens anyway, tell Claude Code: "You went beyond scope. Roll back any changes outside the ingest and only do what I asked." The backup branch (if it created one) is your safety net.

### "Claude Code asked me a question and I gave the wrong answer"

You can change any convention at any time. Tell Claude Code: "Update the [granularity / linking / whatever] convention in CLAUDE.md to [new value]." It'll update and retrofit existing pages automatically per the meta-rule.

---

That's your first ingest. Welcome to your second brain.
