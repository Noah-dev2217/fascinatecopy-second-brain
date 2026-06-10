# INSTALL.md — Setting up your second brain

Step-by-step installation. Tested on Windows 11 with PowerShell. **About 15 minutes if everything goes smoothly.**

Open this file in one window and your terminal in another. Run the commands in order, one at a time. Don't skip steps.

---

## Step 0 — Decide where your vault lives

Pick a folder path. Most of us use `C:\<YourName>'sBrain` (no spaces, no special characters except the apostrophe is fine).

Examples:
- `C:\JonsBrain`
- `C:\JuliasBrain`
- `C:\Rinoah'sBrain`

**Write down your chosen path.** You'll use it in every command below. For this guide we'll use `C:\YourBrain` as a placeholder — replace it with your actual path everywhere.

---

## Step 1 — Install prerequisites

You need four things on your machine. Check each one before installing. If `--version` returns a number, you already have it.

### 1.1 Node.js (required for Claude Code)

Open PowerShell and run:

```powershell
node --version
```

If you see a version number (e.g. `v20.10.0`), skip to 1.2.

If you see an error, download Node.js LTS from https://nodejs.org and install with defaults. Then close and reopen PowerShell and try `node --version` again.

### 1.2 Claude Code

```powershell
claude --version
```

If you see a version number, skip to 1.3.

If not, install:

```powershell
npm install -g @anthropic-ai/claude-code
```

Then `claude --version` should work. If it doesn't, close and reopen PowerShell.

### 1.3 Git

```powershell
git --version
```

If you see a version number, skip to 1.4.

If not, download Git for Windows from https://git-scm.com/download/win and install with defaults.

### 1.4 Obsidian

Download from https://obsidian.md and install. You don't need to open it yet — we'll come back to it in Step 4.

---

## Step 2 — Create the vault folder

In PowerShell:

```powershell
mkdir C:\YourBrain
cd C:\YourBrain
```

(Replace `C:\YourBrain` with your actual path.)

---

## Step 3 — Copy the template files into your vault

You should have this repo cloned somewhere on your machine. If not, clone it first:

```powershell
cd C:\Users\<your-username>\Downloads
git clone <repo-url>
cd fascinatecopy-second-brain
```

Now copy the three template files into your vault:

```powershell
# Replace C:\YourBrain with your actual vault path
copy template\CLAUDE.md C:\YourBrain\
copy template\karpathy-llm-wiki-idea.md C:\YourBrain\
copy template\karpathy-llm-wiki-second-brain.md C:\YourBrain\
```

Verify the three files landed:

```powershell
cd C:\YourBrain
dir
```

You should see `CLAUDE.md`, `karpathy-llm-wiki-idea.md`, and `karpathy-llm-wiki-second-brain.md` in the listing.

---

## Step 4 — Open the vault in Obsidian

1. Open Obsidian.
2. If it's your first time: click **"Open folder as vault"** on the welcome screen.
3. If you've used Obsidian before: click the vault switcher (top-left vault name), then **"Open another vault"** → **"Open folder as vault"**.
4. Browse to `C:\YourBrain` and click **Select Folder**.
5. If Obsidian asks "Trust author?" — click **Trust author and enable plugins** (it's your folder).

You should now see Obsidian showing your vault with the three template files in the sidebar.

### Install the Dataview plugin (optional but recommended)

This lets the vault's index queries render properly later.

1. Open **Settings** (gear icon, bottom left)
2. Click **Community plugins** → **Turn on community plugins**
3. Click **Browse** → search "Dataview" → **Install** → **Enable**
4. Close settings

---

## Step 5 — Scaffold the vault with Claude Code

Back in PowerShell, in your vault folder:

```powershell
cd C:\YourBrain
claude
```

Claude Code will launch in that folder. Wait for it to initialize.

Then paste this **kickoff prompt** as your first message and press Enter:

```
You are now my LLM wiki agent.

I have three files in this folder:
1. karpathy-llm-wiki-idea.md — the CANONICAL spec by Andrej Karpathy. Read this first. Source of truth.
2. karpathy-llm-wiki-second-brain.md — supporting walkthrough.
3. CLAUDE.md — my vault schema with conventions already locked in.

Read CLAUDE.md fully. It tells you exactly how this vault is organized and what conventions to follow. Then:

1. Scaffold the folder structure: raw/, raw/assets/, wiki/, wiki/concepts/, wiki/entities/, wiki/projects/, wiki/sources/, wiki/workflows/
2. Create empty wiki/index.md, wiki/log.md, and wiki/hot.md (with brief placeholder content)
3. Run `git init` and create an initial commit titled "scaffold: vault skeleton"
4. Tell me what you created, and ask any clarifying questions you have about the conventions in CLAUDE.md before I start ingesting sources

Don't ingest anything yet. Just scaffold and confirm you understand CLAUDE.md.
```

Claude Code will create the folder structure, initialize git, and probably ask a few clarifying questions about the conventions.

**Answer the questions honestly.** Your answers shape how Claude Code handles your future ingests, so it's worth spending five minutes on them rather than rushing through.

---

## Step 6 — Verify everything worked

In PowerShell:

```powershell
cd C:\YourBrain
dir
```

You should see:
- `CLAUDE.md`
- `karpathy-llm-wiki-idea.md`
- `karpathy-llm-wiki-second-brain.md`
- `raw\` folder (containing `assets\`)
- `wiki\` folder (containing `index.md`, `log.md`, `hot.md`, and the five subfolders)
- `.git\` folder

In Obsidian, refresh the file tree (right-click → refresh, or close/reopen the vault). You should see all of the above in the sidebar.

If yes — **you have a working vault**. Move on to `FIRST_INGEST.md`.

---

## Troubleshooting

### `claude: command not found` after installation

Close PowerShell completely, then reopen it. New programs sometimes don't appear in PATH until a new shell session starts.

### `Cannot be loaded because running scripts is disabled on this system` (PowerShell error)

Run this once to allow npm-installed CLIs:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Type `Y` when prompted. Then try the command again.

### Claude Code can't find files in the vault

Make sure you're running `claude` *from inside the vault folder*, not from your home directory. The `cd C:\YourBrain` step before launching `claude` is critical — Claude Code scopes itself to the working directory.

### Obsidian shows the folder as empty after I copied files in

Right-click anywhere in the file tree → **Refresh**. Obsidian sometimes caches the folder state.

### The kickoff prompt asked something I don't know how to answer

That's normal. Tell Claude Code "I'm not sure — give me your best default and we'll change it later." The convention-retrofit meta-rule means you can change your mind any time and the vault stays consistent.

### git complains "Author identity unknown"

One-time setup, run these in PowerShell (using your actual name and email):

```powershell
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Then retry the scaffold step.

---

Once verification passes, you're done with install. Open `FIRST_INGEST.md`.
