# Custom GPT Builder — Claude Code Project

Turn a raw idea into a production-grade Custom GPT package, ready to paste into ChatGPT's GPT Builder UI. One command, four steps, fully automated.

This project replaces the manual "Master Custom GPTs Challenge" 4-day workflow (which required clicking through three separate ChatGPT GPTs: Operationalizer, KB Master Builder, Metaprompt) with a local Claude Code pipeline that runs end-to-end in one shot.

## What you get out

Every build produces a `builds/<your-gpt-name>/` folder containing:

| File | Purpose |
|------|---------|
| `knowledge-base.txt` | **Upload this** to ChatGPT's GPT Builder under "Knowledge" |
| `system-prompt.md` | **Paste this** into the GPT Builder's "Instructions" field |
| `gpt-config.md` | Recommended name, description, conversation starters, and capability toggles |
| `worksheet.md` | Full audit log of every step (the "GPT Building Blocks Worksheet") |
| `purpose.md`, `knowledge-report.md`, `process.md`, `variables.md` | Intermediate artifacts from each day |

## Prerequisites

- **Claude Code** installed. If you installed it but have not used it yet, run `claude` in any terminal to log in. See the official install guide at https://docs.claude.com if the command is not on your PATH.
- A terminal (macOS Terminal, iTerm, Windows Terminal, or your editor's integrated terminal).

That is it. No API keys, no n8n instance, no OpenAI subscription beyond the ChatGPT Plus you need anyway to *deploy* the Custom GPT at the end.

## First-time setup

From the folder containing this README:

```bash
cd custom-gpt-builder
claude
```

When Claude Code launches, it automatically reads `CLAUDE.md` and discovers the `/build-gpt` slash command from `.claude/commands/`.

That is the entire setup. There is no npm install, no configuration file, nothing else.

## How to use it

Inside the Claude Code session, type:

```
/build-gpt I want a GPT that reviews resumes and gives ATS-friendly, role-targeted feedback with before/after bullet rewrites.
```

Claude Code will:

1. Create `builds/resume-reviewer-gpt/` (slug auto-generated from your idea)
2. Run Day 1 → purpose + knowledge report
3. Run Day 2 → operationalized process
4. Run Day 3 → structured knowledge base + set of variables
5. Run Day 4 → final system prompt
6. Assemble `knowledge-base.txt`, `system-prompt.md`, and `gpt-config.md`
7. Print a summary with everything you need to finish in ChatGPT

It runs all 4 days in one pass. No pauses. No confirmations.

### Then: deploy it in ChatGPT

Open `gpt-config.md` — it tells you, step by step, what to paste into which field of ChatGPT's GPT Builder UI. Short version:

1. Go to https://chatgpt.com/gpts and click **+ Create**
2. Click the **Configure** tab (not Create — never use the conversational Create mode; it produces weak GPTs)
3. Paste the name and description from `gpt-config.md` into the Name and Description fields
4. Paste the full contents of `system-prompt.md` into **Instructions**
5. Paste the 3 conversation starters
6. Under **Knowledge**, upload `knowledge-base.txt`
7. Toggle capabilities as recommended in `gpt-config.md`
8. Click **Create** (top right)

Test with the preview pane on the right before publishing.

## Project structure

```
custom-gpt-builder/
├── CLAUDE.md                         ← project instructions (auto-loaded)
├── README.md                         ← this file
├── .claude/
│   └── commands/
│       └── build-gpt.md              ← the /build-gpt slash command
├── prompts/
│   ├── 01-purpose-knowledge.md       ← Day 1 (replaces manual Perplexity research)
│   ├── 02-operationalizer.md         ← Day 2 (replaces Operationalizer GPT)
│   ├── 03-kb-master.md               ← Day 3 (replaces KB Master Builder GPT)
│   └── 04-metaprompt.md              ← Day 4 (replaces Metaprompt GPT)
├── templates/
│   └── worksheet-template.md         ← skeleton for the audit log
└── builds/                           ← output lands here, one folder per GPT
```

## What changed from the 2024 original playbook

The original challenge was designed in 2024 around three manual ChatGPT GPTs. This project faithfully replicates their exact methodologies while upgrading for 2026:

- **Faithful replication of Daron Vener's three GPTs.** The prompts are built from the actual system prompts of Operationalizer GPT, KB Master Builder GPT, and Metaprompt GPT. The Operationalizer's 8-step framework, KB Master Builder's input-gated methodology, and Metaprompt's PROMPT_TEMPLATE format are all preserved exactly.
- **PROMPT_TEMPLATE format.** Day 4 produces system prompts in the exact format: CONTEXT → GOAL → INSTRUCTIONS (numbered step-by-step) → STYLE → TONE → FORMAT → INPUT VARIABLES → CONSTRAINTS → CORE AIM. Opens with "Hey ChatGPT, you are [NAME]..." as the original does.
- **Task-based process format.** Day 2 produces processes where each task includes Instructions, Expert Insights, and Quality Criteria — matching the Operationalizer's output structure.
- **Deep knowledge reports.** Day 1 now produces 3,000-6,000 word knowledge reports with worked examples for every framework, pass/fail examples for every criterion, and concrete examples for every forbidden behavior. This matches the depth Daron expects from production-grade knowledge reports.
- **Retrieval-optimized knowledge base.** KB Master Builder's design standards are preserved: chunking, semantic search optimization, single-idea paragraphs, hierarchical headings, AI-execution focus.

## Why this uses Claude Code instead of n8n or the OpenAI API

- The three ChatGPT GPTs at the `chatgpt.com/g/g-...` URLs have no API. They cannot be called from n8n or any automated pipeline. This project replicates their behavior as prompts instead.
- Claude Code reads and writes local files natively — the worksheet audit log is just a Markdown file that gets appended to, no database needed.
- You can version-control the whole project with git and diff your iterations.
- Running the pipeline costs nothing beyond your existing Claude Code subscription.

## Troubleshooting

**"Claude Code doesn't see the slash command."**
Make sure you launched `claude` from *inside* the `custom-gpt-builder/` folder. Slash commands live in `.claude/commands/` relative to the current working directory.

**"The generated GPT doesn't behave like I wanted."**
Open `builds/<your-gpt>/worksheet.md` and find the day where it went off-track. The fastest fix is usually editing the Knowledge Report or Purpose Statement and re-running from that day forward. You can do this by just asking Claude Code: "Re-run Day 2 onward using the updated knowledge report."

**"I want to edit one of the prompts."**
The prompt files in `prompts/` are yours to modify. They are the "system prompts" of this pipeline itself — tune them to your taste. For example, you might want Day 4 to produce shorter system prompts, or Day 3 to generate more forbidden behaviors.

**"The idea I typed was too vague."**
Claude Code will make assumptions and list them under `## Assumptions` in the worksheet. Read that section and decide if the assumptions are wrong. If they are, re-run with a more specific idea.

## Running the pipeline against your own existing GPT ideas

If you already have a partial Purpose Statement or Knowledge Report from elsewhere (e.g. you already ran Day 1 manually), just tell Claude Code in plain English: "Skip Day 1, use this existing purpose and knowledge report, and run Day 2 onward." The project instructions in `CLAUDE.md` make it flexible enough to handle partial starts.
