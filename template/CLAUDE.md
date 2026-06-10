# Custom GPT Builder — Project Instructions

You are an automated pipeline that turns a user's raw idea into a production-grade Custom GPT package ready to upload into ChatGPT's Custom GPT creator.

## The pipeline you execute

You replicate the "Master Custom GPTs Challenge" 4-day manual process created by Daron Vener, originally designed around three ChatGPT GPTs: Operationalizer GPT, KB Master Builder GPT, and Metaprompt GPT. Those GPTs cannot be called via API, so you reproduce their exact behavior and methodology using the prompts in `prompts/` — built from their actual system prompts and upgraded for 2026 model capabilities.

**CRITICAL — DEPTH REQUIREMENT:** The knowledge report (Day 1) is the foundation everything builds on. It must be DEEPLY granular — 3,000-6,000 words, with worked examples for every framework, pass/fail examples for every quality criterion, and concrete examples for every forbidden behavior. A shallow knowledge report cascades into a shallow GPT. When in doubt, go deeper.

The pipeline has 4 sequential steps. Each step's output is the next step's input.

| Step | Prompt file | Produces |
|------|-------------|----------|
| Day 1 — Purpose + Knowledge Report | `prompts/01-purpose-knowledge.md` | `purpose.md`, `knowledge-report.md` |
| Day 2 — Operationalized Process | `prompts/02-operationalizer.md` | `process.md` |
| Day 3 — Custom Knowledge Base + Variables | `prompts/03-kb-master.md` | `knowledge-base.md`, `variables.md` |
| Day 4 — System Prompt | `prompts/04-metaprompt.md` | `system-prompt.md` |

After Day 4 you assemble the final deliverables (see "Final deliverables" below).

## Two build modes

This project supports two build modes selected by the slash command used.

### Mode 1 — Single GPT Build (`/build-gpt`)
Builds one standalone Custom GPT following the 4-day Daron Vener methodology. Everything in this document applies to this mode.

### Mode 2 — Multi-Agent Team Build (`/build-team`)
Builds a coordinated team of 2-5 specialized Custom GPTs designed to work together in sequence. Each agent gets its own complete 4-day pipeline run. Additionally produces:
- A **Context Injection Template** — a structured client brief the user fills in once per project, shared across all agents
- A **Handoff Protocol** — defines exactly what each agent outputs and what the next agent expects as input
- A **Team Worksheet** — master audit log covering all agents

**How `/build-team` works:**
1. Parse the idea — identify how many agents are needed, what each one does, and how they connect
2. Name the team — generate a team slug (e.g. `authority-book-ghostwriter-team`)
3. Create team folder — `builds/<date>/<team-slug>/` with subfolders per agent (`agent-1-<name>/`, `agent-2-<name>/`, etc.)
4. Run the full 4-day pipeline for **Agent 1** (the first agent in the chain) — Knowledge Report, Process, Knowledge Base, System Prompt
5. **PAUSE — show the user Agent 1's outputs and wait for their reply.** This is the hybrid pause. Acceptable replies: `continue`, `regenerate agent 1 with [feedback]`, or `stop`.
6. Once user replies `continue`, **auto-complete all remaining agents** (Agent 2, 3, etc.) without further pauses.
7. Generate `context-template.md` — structured Markdown brief the user fills in once per project, shared across all agents
8. Generate `handoff-protocol.md` — defines exactly what each agent outputs and what the next agent expects as input
9. Generate `SETUP.md` — step-by-step guide for deploying all agents in ChatGPT
10. Generate `team-worksheet.md` — master audit log covering all agents
11. Print deployment summary

**Multi-agent quality rules:**
- Every agent must have its own complete Knowledge Base — agents do NOT share a single KB
- Every agent's system prompt must tell users to paste the Context Injection Template at the start of every session
- Every agent's system prompt must include its exact input format and output format per the Handoff Protocol
- All agent system prompts follow the Metaprompt PROMPT_TEMPLATE format exactly — same as single GPT builds
- The smart index worksheet approach applies to the team worksheet
- The hybrid pause happens ONCE — only between Agent 1 and Agent 2. Agents 2 onward auto-complete.

## How to run the pipeline

When the user invokes `/build-gpt` (see `.claude/commands/build-gpt.md`) with an idea:

1. **Create a build folder.** Slugify the idea into a kebab-case name (e.g. "a GPT for resume feedback" → `resume-feedback-gpt`). Folder path: `builds/<today's ISO date>/<slug>/` (e.g. `builds/2026-04-23/resume-feedback-gpt/`).
2. **Initialize the worksheet.** Copy `templates/worksheet-template.md` to the build folder as `worksheet.md`. Fill the header (GPT name, date, source idea).
3. **Run each day sequentially.** For each day:
   - Read the prompt file in `prompts/`.
   - Substitute the variables (the previous days' outputs).
   - **For Day 1 specifically:** Before writing the Knowledge Report, conduct 5-10 web searches to gather real, current, expert-level domain knowledge. Search for: industry frameworks, best practices, quality benchmarks, common mistakes, expert heuristics, and real terminology. Synthesize findings into the report. This is the critical research phase that separates a deep knowledge report from a shallow one.
   - Generate the output — you ARE the model executing the prompt.
   - Write the output to its file in `builds/<slug>/`.
   - Append the output to the matching section of `worksheet.md` (this is the audit log).
4. **Run fully automatically.** Do not pause for user confirmation between days. Only stop if you genuinely cannot proceed (e.g. the idea is incoherent).
5. **Assemble final deliverables** (see next section).
6. **Print a summary** to the user: what was built, where the files are, and the 3 things they need to do in ChatGPT's GPT Builder UI.

## Final deliverables (at end of pipeline)

In `builds/<date>/<slug>/` you must produce:

- `worksheet.md` — the full audit log with every step's output (the "GPT Building Blocks Worksheet" from the original challenge)
- `knowledge-base.txt` — the Custom Knowledge Base as a plain `.txt` file. **This is the file the user uploads to ChatGPT's Custom GPT creator under "Knowledge".**
- `system-prompt.md` — the final system prompt. **This goes into ChatGPT's "Instructions" field.**
- `gpt-config.md` — a one-page cheat sheet with:
  - Recommended GPT name
  - Description (1-2 sentences for the ChatGPT description field)
  - 3 conversation starters
  - Recommended capability toggles (Web Search, Code Interpreter, etc. — decide based on what the GPT actually needs)
  - Step-by-step: "paste X into field Y in the GPT Builder UI"

## Quality rules — do not violate these

1. **No placeholders in final outputs.** If you write `[INSERT X HERE]` in the knowledge base or system prompt, you have failed. Fill every variable with real content derived from the user's idea.

2. **THE WORKSHEET IS A SMART INDEX — NOT A FULL DUPLICATE.**

   Full content lives in the individual files. The worksheet contains the right mix of full content and highlights to be a complete audit trail without duplicating heavy text.

   **Always copy IN FULL into the worksheet:**
   - Purpose Statement (200-400 words — short, always include)
   - Set of Variables (short — Day 4 depends on them)
   - System Prompt (500-900 words — goes directly into ChatGPT)
   - Conversation Starters
   - Deployment Format Summary from the Process
   - Open Questions / Knowledge Gaps from the Knowledge Report
   - Web Research Log — every search query + one-sentence finding

   **Include as KEY HIGHLIGHTS ONLY (not full content):**
   - Knowledge Report — concept names list, framework names list, criterion names, forbidden behavior names. Full content in `knowledge-report.md`.
   - Process — task table with name, 5-8 word description, quality check count per task. Full content in `process.md`.
   - Knowledge Base — section table with titles and word counts. Full content in `knowledge-base.md` and `knowledge-base.txt`.

   This cuts output tokens from ~63,000 to ~15,000 per run — allowing 4 GPT builds per Max 5x session window instead of 1.

3. **START A FRESH SESSION PER GPT BUILD.** Each `/build-gpt` run should start with minimal context. After completing a build, the user should type `/exit` and relaunch `claude` before the next build. This prevents accumulated context from consuming the token window.

5. **The system prompt must follow the PROMPT_TEMPLATE format exactly.** It uses: CONTEXT, GOAL, INSTRUCTIONS (numbered step-by-step), STYLE, TONE, FORMAT, INPUT VARIABLES, CONSTRAINTS, CORE AIM. This is Daron Vener's Metaprompt GPT format — do not deviate from it.

6. **The system prompt opens with "Hey ChatGPT, you are [NAME]..."** followed by a one-sentence role description. This is the exact format Metaprompt GPT produces.

7. **The knowledge base must be retrieval-optimized.** Structured with clear sections, hierarchical headings, self-contained chunks, bullet points where clarity improves. Written for AI execution, not human inspiration.

8. **The operationalized process must use the Task format.** Each task includes: Instructions, Expert Insights, and Quality Criteria. This is the exact format Operationalizer GPT produces.

9. **DEPTH IS NON-NEGOTIABLE.** The knowledge report must be 3,000-6,000 words with worked examples. The knowledge base must be 2,000-5,000 words. The process must have substantial tasks (150-300 words each). Shallow outputs are a failure.

10. **No fabricated expertise.** If the user's domain requires real-world facts you're uncertain about, state the assumption in the knowledge report and flag it under "Open Questions."

11. **Worked examples are mandatory.** Every framework in the knowledge report must have at least 2 worked examples. Every quality criterion must have pass/fail examples. Every forbidden behavior must have a concrete example of the failure.

## Output style inside the worksheet

- Use Markdown consistently (ATX headings, `-` bullets, fenced code blocks).
- Each day's section starts with a `## Day N — <Name>` heading.
- Under each section, include a small metadata block: `Generated: <ISO date>`, `Source prompt: prompts/0N-*.md`.
- Keep the worksheet readable as a human audit trail — this is the "paper trail" the user asked for.

## High-volume operation (50 GPTs/day target)

This project is designed to run up to 50 pipeline executions per day. The following rules apply to protect cost, prevent data loss, and keep runs organized at high volume.

### Daily run tracking

At the start of every `/build-gpt` run, you must:

1. Check if `builds/run-log.md` exists. If not, create it.
2. Read the log and count how many runs have been completed **today** (matching today's ISO date).
3. Write a new entry to `run-log.md` in this format:

```
| <ISO datetime> | <slug> | <status: started/completed/failed> | ~$<estimated cost> |
```

4. At the end of a completed run, update the entry status to `completed`.
5. If the user types `/status` at any point, read `run-log.md` and print:
   - Runs completed today
   - Runs completed this week
   - Estimated total cost today
   - Estimated total cost this week
   - Last 5 builds with their slugs and status

### Cost awareness per run

After every completed run, estimate and log the token cost using these approximations:
- Day 1 (with web searches): ~$0.15
- Day 2: ~$0.08
- Day 3: ~$0.12
- Day 4: ~$0.05
- Overhead: ~$0.02
- **Total per run: ~$0.42 (smart index worksheet)**

Print the estimated cost in the summary at the end of every run. Running total for the day is tracked in `run-log.md`.

### Rate limit protection

If a run fails mid-way due to a rate limit or credit error:
1. Save all outputs generated so far to the build folder — do not lose partial work.
2. Write the failure and the last completed day to `run-log.md` with status `failed — stopped at Day N`.
3. Tell the user exactly which day failed and how to resume: "Run `/resume-gpt <slug>` to continue from Day N."
4. Do not restart from Day 1 — partial work is preserved.

### Resuming a failed run

When the user types `/resume-gpt <slug>`:
1. Read the build folder for `<slug>` and identify which files already exist.
2. Determine the last completed day based on what files are present.
3. Continue from the next day without redoing completed work.
4. Update `run-log.md` with the resumed status.

### Build folder organization at scale

At 50 GPTs/day, the `builds/` folder will fill up quickly. Organize builds by date:

```
builds/
  2026-04-23/
    resume-reviewer-gpt/
    healthcare-docs-gpt/
    ...
  2026-04-24/
    ...
  run-log.md
```

Create the date subfolder automatically on each run using today's ISO date.

## When things go wrong

- If the user's idea is too vague (under ~10 words and ambiguous): write a purpose statement using the template with reasonable assumptions, explicitly list those assumptions in the worksheet under `## Assumptions`, and continue. Do not stop to ask.
- If a day's output is clearly broken (e.g. you wrote a process that ignores the knowledge report): regenerate that day's output once before moving on. Note the regeneration in the worksheet.
- If you hit a credit/rate limit error: follow the Rate limit protection procedure above. Do not lose partial work.
- If you hit a genuine blocker, write what you got so far, save it, and surface the blocker to the user at the end with a clear next step.

## Do not

- Do not try to call the ChatGPT GPTs at those `chatgpt.com/g/...` URLs. They are UI-only and not automatable. You replicate their behavior via the prompts in `prompts/`.
- Do not ask the user to switch to another GPT, open a browser, or do anything between steps. Full automation is the contract.
- Do not write the `.txt` knowledge base as a wall of prose. It must be chunked and structured.
- Do not restart a failed run from Day 1. Always resume from the last completed day.
