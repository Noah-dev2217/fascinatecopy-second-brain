# USING_IT.md — How to actually use your second brain

You have a working vault and one ingested source. Now what?

This file covers the habits that make the difference between a vault that sits unused for six months and one that compounds into something genuinely useful. None of this is mandatory — pick what works for you and ignore the rest.

---

## The mental model

There's one shift that has to happen before any of this works:

**You're the curator. Claude Code is the maintainer.**

Your job is to source things, ask good questions, and decide what matters. Claude Code's job is everything else — summarizing, cross-linking, filing, formatting, keeping the wiki consistent.

If you find yourself doing Claude Code's job (manually writing wiki pages, hand-editing links, organizing folders), you're holding it wrong. The agent doesn't get bored doing bookkeeping. You will.

---

## Daily habits

### 1. Capture on friction, not on everything

The trap people fall into: treating their second brain like a journal. Dump everything in. That ruins the wiki — too much low-signal content drowns the useful stuff.

Better rule: **capture only when you hit friction.** Friction means *"this will be relevant again and I'd rather not re-figure it out."*

Examples that pass the test:

- You spent 40 minutes solving an n8n quirk → capture (you'll hit it again)
- A client said something that reframes a project → capture
- You wrote a prompt that worked unusually well → capture
- A meeting produced a real decision → capture the decision

Examples that fail the test:

- A passing thought ("I should learn Rust someday")
- A meeting recap with no decisions or insights
- Tasks (those belong in a real tracker, not the brain)
- Notes you'll never re-read

The friction filter keeps the vault dense instead of bloated.

### 2. The 60-second capture rule

When something passes the friction filter, drop a one-paragraph note into `raw/inbox/` and move on. Don't format. Don't tag. Don't link. **Just dump.**

Set up the inbox folder once:

```powershell
mkdir C:\YourBrain\raw\inbox
```

Capture an idea:

```powershell
notepad C:\YourBrain\raw\inbox\2026-06-12_n8n_webhook_quirk.md
```

Write whatever you remember, save, done. The agent will turn it into proper wiki pages later when you tell it to ingest the inbox.

### 3. Query before you Google or ChatGPT

This is the habit shift that pays off most.

When a work question hits you — *"what did we decide about X?"*, *"how did I structure that brief?"*, *"what's the right way to handle Y?"* — your default is now: **ask Claude Code in your vault.**

Open the vault in Claude Code and ask:

```
Look in the wiki and tell me what we know about <topic>. If there are connections to <related thing> worth flagging, mention them.
```

The "connections to <related thing>" framing is the magic. It surfaces stuff you forgot was related — which is the actual value of having a linked wiki instead of a search engine.

If the wiki doesn't have the answer, *then* go to Google or ChatGPT. But check the wiki first. The wiki contains *your* prior thinking; the internet contains *everyone else's*.

---

## When starting a new project — the kickoff query

Before you start a complex project (a new automation, a client deliverable, a research task), spend 2 minutes asking your wiki what's relevant:

```
I'm about to start <X>. Look at the wiki — what prior work, patterns, decisions, or sources are relevant? Surface 3-5 things I should reread or reuse before I start fresh.
```

This catches two failure modes that cost real time:

1. **You already solved this and forgot.** You'll rebuild from scratch unless something reminds you.
2. **A past project established a constraint you should respect this time.** Naming conventions, architectural choices, lessons learned — they should carry forward.

The wiki remembers so you don't have to.

---

## When finishing a project — the end-of-project ingest

When a project finishes (or hits a major milestone), this is when the wiki earns its keep.

Drop the right artifacts into `raw/<project-name>/` and ingest. The "right artifacts" are:

- The final spec, brief, or design doc
- The runbook or setup guide
- A short **lessons-learned doc** — 5 bullets of "what worked / what didn't" written by you
- The handoff docs (if any)

Don't ingest the entire codebase — that's bloat. Ingest *the artifacts that capture the thinking*.

The lessons-learned bullets are the highest-value part. **Five minutes of writing those before ingest compounds for years.** Future you will query "what did I learn from past projects like X?" and the wiki will actually have an answer.

Then ingest with the one-line trigger:

```
ingest raw/<project-name> — <one-line description>
```

---

## The biggest unlock — point other Claude Code projects at this vault

This is the move most people miss, and it's worth more than every other habit combined.

Every time you start a new Claude Code project (a new automation, a new client task, anything), you currently re-explain yourself: who you are, what stack you use, what conventions you follow, what you've built before. That's tokens and time you waste in every new session.

Instead: **add a snippet to that project's `CLAUDE.md` pointing at your vault.** Now any new Claude Code session knows about *you* before it does any work.

### The snippet to add

Open the new project's `CLAUDE.md` (create one if it doesn't exist) and add this section, replacing `C:\YourBrain\` with your actual vault path:

````markdown
## Second brain — how to read me

I maintain a personal Karpathy-style LLM Wiki at `C:\YourBrain\` (Obsidian + Claude Code).
It contains my work, projects, methodologies, decisions, and preferences. It is the
source of truth for context about ME — my style, my stack, what I've built, how I think.

### When to read the wiki

Read the wiki when you need context about ME or MY WORK that you don't already have:
- Before starting a new project for me, to find prior work I'd want to reuse
- When I reference a project, person, tool, or pattern by name without explaining it
- When you need to know my preferences before making a stylistic or architectural call
- When I ask a question that depends on prior context ("how did we handle X before?")

Do NOT read the wiki for:
- Generic technical questions (Python syntax, API docs, library behavior)
- Anything that doesn't depend on knowing me specifically
- Routine task execution where you already have enough context

### How to read the wiki (in order)

1. Read `C:\YourBrain\wiki\hot.md` first. This is a ~500-word cache of what's currently
   top of mind. Often this is all you need.
2. If hot.md doesn't have what you need, read `C:\YourBrain\wiki\index.md` — the catalog
   of every wiki page, organized by section.
3. Read the specific pages you found via the index.
4. Only search the full vault if steps 1-3 didn't surface what you need.

### How to USE what you read

- Don't say "I looked at your wiki and found..." — just act on what you found.
- Don't quote the wiki at length — synthesize it into your response.
- Don't ask me to re-explain things that are in the wiki — read first, ask second.
- If the wiki shows me making a decision on a similar past problem, default to that
  decision's reasoning unless I explicitly ask otherwise.
````

That's the snippet. Paste it into any project's `CLAUDE.md` and that project's Claude Code sessions will know about you without you re-explaining.

### Important — don't deploy this snippet too early

The snippet only works if the wiki has enough content to be worth reading. Deploying it when the wiki has 1-2 sources means Claude Code reads thin content and makes up the rest, which is worse than no wiki at all.

**Wait until you have at least 5-8 ingested sources before deploying this snippet to other projects.** Until then, the wiki is still calibrating.

---

## A weekly rhythm worth trying

You don't have to do this — but if you want a routine, this one works:

- **Monday morning (5 min):** open your vault, read `wiki/hot.md`, ask Claude Code "what's currently top of mind? what should I be working on this week?" Orients you fast.
- **End of each work session (2 min):** ask Claude Code "anything from today worth capturing into `raw/inbox/`?" Turns the agent into a capture prompter so you don't forget.
- **Friday afternoon (15 min):** tell Claude Code "ingest everything in `raw/inbox/`" and let it process the week's captures. Then ask it to run a lint pass to keep the wiki healthy.
- **End of every project:** the end-of-project ingest described above.

You'll fall off this rhythm. That's fine. Just restart whenever — the wiki doesn't penalize you for skipping a week.

---

## Reusable prompts

A small library of prompts that come up often. Save them somewhere reachable (Obsidian itself works — make a note called `_prompts` in your vault), or just memorize the patterns.

### Ingest a new source

```
ingest raw/<folder-name> — <one-line description of the source>
```

### Ingest a deprecated project (project you've stopped using)

```
ingest raw/<folder-name> — deprecated. <description>. Deprecation reason: <why you stopped>. Replaced by: <surviving alternative or "none">.
```

### Update the hot cache

```
Update hot.md based on what I've been working on recently. Surface the current active projects, recent decisions worth carrying forward, and anything that should be top of mind.
```

### Query the wiki

```
Look in the wiki and tell me what we know about <topic>. If there are connections to <related thing> worth flagging, mention them.
```

### Project kickoff query

```
I'm about to start <project>. Look at the wiki — what prior work, patterns, decisions, or sources are relevant? Surface 3-5 things I should reread or reuse before I start fresh.
```

### Cross-project pattern check

```
Look across the wiki for patterns I've used in multiple projects. Are there any concepts I've used more than once that don't have a dedicated concept page yet? Suggest 3-5 candidates for consolidation.
```

### Run a lint pass

```
Run a lint pass on the wiki. Look for: contradictions between pages, stale claims superseded by newer sources, orphan pages with no inbound links, important concepts mentioned but lacking their own page, missing cross-references, data gaps that could be filled by web search. Report findings — don't auto-fix.
```

### Add a new convention

```
I want to add a new convention to CLAUDE.md: <describe the new rule>.

After adding it, run the convention-retrofit self-audit per the meta-rule — check whether existing pages conform to the new rule and retrofit any that don't, in a separate commit titled "retrofit: <description>".

Commit the new convention as: "convention: <short name>"
```

### Customize an existing convention

```
I want to change the <X> convention in CLAUDE.md from <current> to <new>. Apply the retrofit meta-rule — audit existing pages, fix any that no longer conform, in a separate commit.
```

---

## What NOT to do

Five traps that ruin second brains:

### Don't make it your task tracker

Active to-dos belong in ClickUp, Trello, Asana, paper — wherever your real task system is. The wiki holds **context**, not **state**. Future plans with `status: planning` frontmatter are fine; daily to-dos are not.

### Don't ingest things just to ingest things

Wiki bloat kills retrieval quality. If a source doesn't pass the friction filter, leave it out. Less content with high signal beats more content with low signal.

### Don't manually edit wiki pages

The wiki is Claude Code's territory. If you hand-edit pages, you create drift between what the agent thinks is in the wiki and what's actually there. If a page is wrong, *ask the agent to fix it*. Same outcome, no drift.

Exception: typos and small wording fixes in your own notes are fine. Just don't restructure pages or add new sections by hand.

### Don't share vaults

Each person on the team should have their own independent vault. Don't try to merge yours with someone else's, don't share via Dropbox/OneDrive, don't put the vault in a team-shared folder. The conventions in your `CLAUDE.md` are yours; they'll diverge from your teammate's over time. That's correct — they should diverge, because each of you works differently.

If you want to share specific *knowledge*, export the relevant wiki pages as plain markdown and share those. The vault itself stays personal.

### Don't deploy the second-brain context prompt prematurely

The "point other projects at your vault" snippet only pays off once the wiki has real content. If you paste it into a project's `CLAUDE.md` after one or two ingests, Claude Code reads thin content and produces low-quality personalization. Wait until you have 5-8 sources ingested. Then deploy it widely.

### Don't skip git commits

Every ingest = one commit. The scope-discipline convention in `CLAUDE.md` enforces this. If you ever need to roll back a bad ingest, git is your safety net. If you skip commits, you lose that net.

---

## When in doubt

Ask your team's vault expert (for FascinateCopy: Rinoah). The conventions in `CLAUDE.md` will evolve as you find what works for your specific work. Don't be afraid to add, change, or remove conventions — the retrofit meta-rule keeps the wiki consistent as the schema evolves.

The biggest mistake isn't doing it wrong. It's not doing it at all.
