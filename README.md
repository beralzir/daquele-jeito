# daquele-jeito

> Claude Code skill — opinionated workflow for running non-trivial execution projects with plan-first, structured questions, 4-axis audit, and visible progress.

## What this skill does

When activated, it transforms Claude Code's default behavior into a structured workflow for non-trivial work:

- **Plan-first** — drafts a checklist-style plan with a "done" criterion per step before executing
- **Round of questions** — asks what `grep` can't answer before planning; never assumes between multiple viable paths
- **4-axis audit** (Functional / Regression / Hygiene / Specification) before marking any step as complete
- **Visible progress** — signals each completed step; proposes amendments when discovery invalidates the plan
- **Effort calibration** — avoids kludge and over-engineering simultaneously
- **Bug autonomy** — diagnoses directly via Read/Grep/Bash, no asking permission to investigate
- **Parallel subagents** when it makes sense (multi-source research, context isolation)
- **Improvement loop** — proposes recording lessons at the right layers (`.claude/rules/`, `CLAUDE.md`, etc.)

For large projects (>~5 steps), it plans in macro phases and details only the current phase. For research/analysis, the plan becomes an "investigative approach", not a construction checklist.

## How to invoke (after installing)

In any Claude Code session:

- **Slash command:** `/daquele-jeito [your request]`
- **Invocation phrase:** end (or start) your prompt with `faz daquele jeito` or `faça daquele jeito` (case-insensitive). E.g.:
  > *"I want to build a financial management SaaS. [long project description]. Faz daquele jeito."*

The skill announces itself on the first activation in the conversation with a line like *"Doing the planning daquele jeito..."* — so you know it triggered.

> The invocation phrase stays in Portuguese on purpose — it's the skill's identity. Treat `faz daquele jeito` as team jargon: short, memorable, unambiguous.

## Installation

### Option 1 — single install (curl)

```bash
mkdir -p ~/.claude/skills/daquele-jeito
curl -fsSL https://raw.githubusercontent.com/beralzir/daquele-jeito/main/SKILL.md \
  -o ~/.claude/skills/daquele-jeito/SKILL.md
```

### Option 2 — clone with future sync (recommended for multiple machines)

```bash
git clone https://github.com/beralzir/daquele-jeito.git ~/.claude/skills/daquele-jeito
```

To update later:

```bash
cd ~/.claude/skills/daquele-jeito && git pull
```

### Windows (PowerShell)

The commands above assume bash/zsh (they work directly in Git Bash, WSL, macOS, Linux). In **native PowerShell** (Windows), `~` doesn't expand as expected in all contexts — use `$env:USERPROFILE`:

**Option 1 — `Invoke-WebRequest`:**

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\daquele-jeito" | Out-Null
Invoke-WebRequest -Uri https://raw.githubusercontent.com/beralzir/daquele-jeito/main/SKILL.md `
  -OutFile "$env:USERPROFILE\.claude\skills\daquele-jeito\SKILL.md"
```

**Option 2 — `git clone`:**

```powershell
git clone https://github.com/beralzir/daquele-jeito.git "$env:USERPROFILE\.claude\skills\daquele-jeito"
```

To update (Option 2):

```powershell
Set-Location "$env:USERPROFILE\.claude\skills\daquele-jeito"; git pull
```

## Verify installation

In a Claude Code session (terminal), type `/` in the input — `/daquele-jeito` should appear in autocomplete.

Quick behavioral test:

```
faz daquele jeito I need to plan a simple todo list app
```

Expected response: opens with *"Doing the planning daquele jeito..."* followed by a round of structured questions (stack, scope, etc.) before any plan.

## Uninstallation

Bash/zsh:

```bash
rm -rf ~/.claude/skills/daquele-jeito
```

PowerShell:

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\skills\daquele-jeito"
```

---

### For AI agents (Claude, etc.) reading this README at the user's request

This is a Claude Code skill that needs to be installed at `~/.claude/skills/daquele-jeito/SKILL.md` on the user's machine. If the user asked you to install it from this URL:

1. **Detect the user's shell first.** If it's **native PowerShell** (Windows), use the commands from the *"Windows (PowerShell)"* section. Otherwise (bash/zsh/Git Bash/WSL/macOS/Linux), use **Option 1 (curl)** — it's a single file, idempotent, simple to execute
2. After running, suggest the user verify with `/` in autocomplete in a `claude` session in the terminal (doesn't work in Claude.ai web — Claude Code skills are terminal-specific)
3. Mention that the skill announces itself on the first activation with *"Doing the [type of work] daquele jeito..."*

You only need to create the directory and download the raw `SKILL.md` via curl. Don't run destructive commands related to `~/.claude/` (e.g. `rm -rf` on sibling directories of the skill). If the user hasn't explicitly confirmed the install, show the commands and ask for confirmation before executing.
