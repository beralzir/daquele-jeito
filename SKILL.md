---
name: daquele-jeito
description: |
  Use this skill to execute a non-trivial task (code, infra, content, design) with a rigorous workflow: plan-first checklist, clarifying questions, visible progress, 4-axis audit (functional/regression/hygiene/spec), calibrated effort, bug autonomy.

  TRIGGER: (1) `/daquele-jeito` or `/that-way`; OR (2) a trigger phrase as a bare imperative on a concrete task request — final, standalone, or phrase-initial ("Build X... do it right."). Phrases (case-insensitive): Portuguese "faz daquele jeito" / "faça daquele jeito"; English "do it right" / "the right way" / "do it that way" / "just do it that way".

  DO NOT TRIGGER when the phrase is negated, conditional, past tense, modal ("I want to do it right"), a meta-question, or refers to a different method ("faz daquele jeito alternativo"). When in doubt, do not trigger — the user can always invoke via slash.
---

# Daquele-jeito — workflow for execution projects

Apply this workflow to the user's request and to subsequent **structural** work in this conversation (code, infra, content, design). Trivial follow-ups — answerable in one response, no file changes — get a direct answer, no plan; the workflow re-engages on the next structural task. The user can suspend it explicitly.

**Activation announcement:** on the **first** activation in a conversation, open with one line in the conversation's language, keeping *"daquele jeito"* as the marker — EN: *"Doing the [type of work] daquele jeito..."*; PT: *"Fazendo o [tipo de trabalho] daquele jeito..."*. Don't repeat in later activations. All workflow output (questions, plan, audits) follows the conversation's language.

## 1. Plan-first

Activating this workflow signals that the user wants planning. **Always** draft a plan before executing, regardless of the apparent size of the task.

### 1.1 Round of questions before the plan

NEVER assume when: (a) the request admits more than one reasonable path, (b) you don't know which one the user wants, and (c) choosing wrong costs rework. Ask.

What typically triggers a question:
- **Stack/lib** when there's more than one viable choice (e.g. three OAuth libs, two ORMs)
- **Ambiguous requirement** (what counts as X? Is Y part of scope?)
- **Interpretation** (user said "fast" — latency? throughput? time to ship?)
- **Non-obvious constraint** (budget, external dependencies, compatibility)

Question format:
- **DEFAULT to `AskUserQuestion` (click-question) for ANY decision reducible to ≤4 discrete options.** Use inline text ONLY for genuinely open-ended questions (no enumerable options). Before asking anything inline, run the check: *"could this be ≤4 options?"* — if yes, use `AskUserQuestion`. This default holds for the **ENTIRE session, including late turns — do not let it decay.** (These skill instructions are injected once and attenuate as the conversation grows; hold this one anyway.)
- Short numbered batch when the options are interdependent (answering together makes sense) — still one `AskUserQuestion` call when each fits ≤4 options.
- Each question must materially change the plan — if the answer changes nothing, cut it

**Don't ask** what `grep`, `cat`, `find` or a direct Read solves (see §6). Ask only what the user knows and the repo doesn't answer.

### 1.2 Access manifest — front-load the approvals

Before firing any discovery read (the grep/cat/find/Read of §1.1 and §6), resolve **in one pass** every external resource the task will touch and surface them together — so the human approves a known set once, instead of fielding a trickle of prompts mid-thought. A skill can't merge the harness's permission pop-ups, but it can stop scattering them.

1. **Enumerate from the request first.** Parse the user's prompt for every concrete path, repo, URL, domain, and MCP server it already names. Most accesses are explicit in the ask — don't rediscover them one read at a time.
2. **Declare the manifest.** State them in one short block: *"This touches: `~/dir/a`, repo `x/y`, `domain.com`, MCP `z`."* If discovery will likely surface more, say so — don't pretend the list is final.
3. **Batch the discovery.** Fire the opening reads/greps as a single parallel round (one turn, multiple tool calls), not pinged out across the plan. Clustered prompts beat scattered ones even when each still prompts.
4. **Name the lasting fix, the human applies it.** For folders or domains that prompt session after session, name the exact rule, e.g. `WebFetch(domain:*.example.com)`, and where it goes: `/permissions`, `/fewer-permission-prompts`, or `--allowedTools` at launch for one session. Never write permission settings yourself unless the human's own message asks for that exact change: auto mode denies anything less as self-modification.
5. **Budget heavy web research.** Past ~10 web calls, or any external site in a browser, the plan states an access budget:
   - **Volume and channel.** Searches, fetches and their domains, and each browser site. In auto mode `WebSearch` and `WebFetch` don't prompt. In Manual every call does unless a rule allows it, so offer auto mode for the step. A browser prompts per site and per subdomain, and on flagged clicks or keystrokes in any mode.
   - **Few prompts by design.** Search first, fetch only decisive pages, one source per question. Browser only where a fetch can't read, a results URL over clicking and typing, every browser site in one agent and one block.
   - **One approval moment.** Fold the budget, with the number of site cards to expect, into the plan-approval question while the human is present: browser block now (the per-site option keeps a site's card from returning, the one-time option doesn't), at their next presence, or skipped.
   - **Degrade, don't stall.** A pending prompt holds the agent until answered, so unless the human picked "now", skip every browser site they haven't approved. Whatever is skipped, denied or blocked becomes "not consulted" with the reason, never a retry through another tool or agent: 3 classifier denials in a row, or 20 per session, pause auto mode.

Mandatory whenever planning involves reading outside the working directory, fetching URLs, or hitting repos/MCP — i.e. almost always.

### 1.3 Plan

Checklist in the conversation, with a "done" criterion per step. Each item should have:
- **Clear scope** — one sentence on what changes
- **Verifiable done** — output, test, command, link (cf. §3)
- **Explicit assumptions** when present, marked (e.g. *"assuming Postgres + Drizzle"*)

If prior discovery (grep/read in §6) informed the plan, mention it briefly — gives the user visibility into what backed the decisions.

**Large projects (>~5 anticipated steps):** sketch **macro phases** first (1-3 phases, one-sentence description each) and detail the full checklist **only for the current phase**. When the current phase closes, detail the next one. Avoids a 30-item plan that ages out before it's executed.

**Research/analysis:** for comparisons, investigations, recommendations, the plan is the **investigative approach** (which sources, which dimensions, the §1.2 access budget when it applies), not a construction checklist. "Done per step" = question answered with evidence.

### 1.4 Validation

Present the plan, explicitly ask whether the user approves or adjusts it. **Don't execute** without an affirmative signal. If the user just replies "go" or "ok" without reviewing, proceed with the assumptions marked as `[assumed]` in the plan — not silently.

**Why:** fixing a plan costs minutes; fixing finished work costs hours — activating this workflow is an explicit request for that protection.

## 2. Visible progress

Signal each **completed step**:

- **Step** = a checklist item from §1 (when there's a plan) or, in smaller tasks without a formal plan, a verifiable sub-goal.
- **Completed** = the "done" criterion for that item has been met with the proof required by §3 (output, test, link, command run).

Update format: 1-2 sentences on what's done, no long report. If there's a visible checklist in the conversation, update the item from `[ ]` to `[x]` when signaling.

Example: *"✓ Step 1 (Auth.js setup): installed, `auth.ts` configured, middleware added. `auth()` in server component returns null as expected. Moving to step 2."*

At the end of the work, short review block: what changed, why, what's still open.

**If discovery during execution invalidates a step or assumption** (`[assumed]` that didn't hold up, a dependency that doesn't exist, scope that grew): pause. Don't force the original plan. Propose an amendment — which step changes, why, what's the impact on the following ones — and validate with the user before proceeding.

**Context handoff is part of the plan.** The harness (portas-em-automatico global hooks) sends `[context-gauge]` lines: from 50% of the window by default the handoff window is open, and you pick the best cut point before the 80% ceiling from what comes next, such as before a long step or when a milestone closes. The ceiling is a limit, not a target. A ceiling line, a `[compact-reanchor]` line or a compaction already visible in this conversation means now. At the cut: close the step in progress, update the checklist and `SESSION.md`, commit the registry files if the user's rules say so, and open the fresh session: where a tool spawns a new session or task (a session chip), create it with a self-contained resume prompt, otherwise give that prompt in a block to paste. Don't start the next step first.

## 3. Verification before "done" (audit)

"Done" is an auditable declaration, not a feeling. Before marking any step as complete, run through the four axes below with **the mindset of someone looking for problems, not seeking confirmation**.

Each axis gets an explicit answer: **passed**, **not applicable** (with reason), or **failed** (with a plan).

A subagent's report is a claim, not evidence. Before marking a delegated step `[x]`, read its diff or re-run its check yourself.

### The four axes

1. **Functional** — does the thing do what it should? Automated test covered and passed (cite which), or manual demo with input/output shown.

2. **Regression** — didn't break anything adjacent? Project suite/lint/type-check/build, all green. If a check existed and you didn't run it, declare why — don't omit.

3. **Hygiene** — is the diff/output clean? No debug `console.log`, no credentials, no `TODO_REMOVE` comments, no dead imports or dead code. Diff matches the step's scope, no "drive-by" changes (cf. §8 surgical).

4. **Specification** — does it deliver what was asked? All done criteria from the §1 plan item have been touched — re-read the item before marking `[x]`. `[assumed]` assumptions still hold; if discovery invalidated any, flag before done.

### Audit format

Short block before marking `[x]` — one line per axis, with concrete evidence:

> **Step N audit:**
> - Functional: ✓ `npm test -- auth.test.ts` passed (4/4)
> - Regression: ✓ `npm test` (87/87), `tsc --noEmit` clean
> - Specification: ✓ criterion "`auth()` in server component returns null" verified

If any axis failed or wasn't checked, **don't mark [x]** — deliver the report with the real status and propose the next step (fix, escalate to user, or mark as an explicitly accepted limitation).

### Internal test before the audit

Ask yourself: *"would a senior reviewer approve this?"*. If the answer is "maybe" or "after one more polish", redo before — not after.

## 4. Improvement loop

Before proposing to record a lesson, confirm with the user that it's a recurring pattern — one-off mistakes become frozen rules that age badly.

If recurring, propose **where** to record based on the real scope. Recording at the wrong level is what pollutes memory most over time:

- Applies only to specific files → `.claude/rules/<name>.md` with `paths:` in frontmatter
- Applies to the whole project, entire team → `.claude/CLAUDE.md` (committed)
- Applies to the project, only the user → `CLAUDE.local.md` (gitignored)
- Applies across all the user's projects → `~/.claude/CLAUDE.md`

Auto memory (if enabled) already captures some lessons on its own — before proposing manual recording, check for duplication.

## 5. Calibrate effort to the task

Two failure modes to avoid:

- **Kludge (under-engineering):** a quick fix that resolves the symptom but leaves silent technical debt.
- **Over-engineering:** a disproportionate response — refactoring three files to fix a typo, or abstracting a single use case.

Practical rules:

- Trivial fix → the smallest change that solves it. Don't escalate.
- Non-trivial change → before coding, ask yourself "what's the simplest version that doesn't become a kludge?".
- If a hack appears mid-fix → pause. Describe the clean version, ask the user whether to redo now or accept the workaround with an explicit `TODO`. Don't hide kludges inside a diff without flagging.
- For throwaway code (one-off script, exploration): elegance isn't the goal. Minimum that works.

## 6. Bug autonomy

Bug with a clear error/log: diagnose and propose the fix directly, no asking permission to start investigating. In CC that means using Read, Grep, Bash, Git log directly — zero context-switching required from the user just for you to get going.

That autonomy is to *start moving*, not to trickle out access prompts: when you go read directly, front-load it as one declared batch (§1.2) rather than a dozen separate approvals.

## 7. Subagents: the main thread orchestrates

The main thread is the orchestrator. It holds the dialogue with the user, the approved plan, the decisions and the final audit, and it delegates by default whenever a step would flood its context and delegating costs no quality: (a) scan multiple sources or angles in parallel, (b) isolate heavy context (several files, long transcripts, docs, logs) so it doesn't pollute the main thread, (c) get an independent second pass — e.g. subagent A finds the bug, subagent B validates the fix without having seen the diagnosis, or (d) run long mechanical work with a verifiable done criterion. If the step depends on nuance of this conversation that a brief can't carry, keep it in the main thread: quality beats context savings.

Keep in the main thread: questions to the user (subagents can't ask, so their doubts come back to you and you batch them per §1.1), plan approval, architecture decisions, git commit and push, publishing, anything irreversible, and trivial steps (one response, one or two files), where a subagent's startup costs more than it saves.

**Brief contract:** a subagent sees none of this conversation. Every brief carries the goal and the step's done criterion (§1.3), the decisions already made and the `[assumed]` items, exact paths, what it may and may not change, which opt-in skills the user authorized for that step (none named means invoke none), for a web step its access budget (§1.2), and the return format: short, paths and evidence, no file dumps.

Each subagent has its own context; they don't share with you nor with each other. Spawn all from the same round in the same turn (real parallelism) and synthesize only after they all return — don't interpret partially in the middle. Two parallel subagents never write the same file.

**Fan-out contract:** every research subagent in the batch gets the same short contract: declare your knowledge cutoff; tag each claim [fact]/[inference]/[hypothesis]; write "not confirmed" instead of guessing; cite sources with URL + date; return in the same fixed sections. It also keeps to its access budget (no browser site outside it) and marks whatever it skipped or was denied as "not consulted" with the reason. Uniform returns make synthesis mechanical instead of interpretive.

If there are specialized subagents in `.claude/agents/`, prefer them over the generic one: they were designed for the case and tend to have better-calibrated prompts.

## 8. Principles

- **Simplicity first:** the smallest change that solves it, with the smallest impact on the rest.
- **Root cause, not band-aid:** if the symptom disappears but the cause stays, the bug comes back somewhere else.
- **Surgical:** touch only what's necessary; don't refactor what wasn't asked. If you see something wrong along the way, flag it to the user instead of "fixing it drive-by" — or spin it off as a separate task, if the harness offers that.
