---
name: daquele-jeito
description: "Workflow for non-trivial execution projects — plan-first with structured questions, visible progress, 4-axis audit, improvement loop, calibrated effort, bug autonomy, subagents. ACTIVATE ONLY when: (1) user invokes via /daquele-jeito or /that-way slash command, OR (2) one of the trigger phrases (case-insensitive) appears as an INVOCATION IMPERATIVE in the prompt — Portuguese: 'faz daquele jeito' / 'faça daquele jeito'; English: 'do it right' / 'the right way' / 'do it that way' / 'just do it that way' — typically as a final/standalone instruction meaning 'apply this workflow to the request above/below'. ACTIVATES (PT): 'I want X. [long description]. Faz daquele jeito.' (final imperative), 'Faça daquele jeito the project setup', 'Faz daquele jeito: build X'. ACTIVATES (EN): 'I want to build X. [long description]. Do it right.' (final imperative), 'Just do it that way: build X', 'Build it the right way.' (standalone bare imperative), 'Do it that way.' (standalone). DOES NOT ACTIVATE (PT): 'faz daquele jeito alternativo que mencionei' (reference to a discussed method), 'não faz daquele jeito' (negation), 'se fizer daquele jeito...' (conditional), 'você fez daquele jeito ontem' (past tense), 'como funciona o faz daquele jeito?' (meta-question). DOES NOT ACTIVATE (EN): 'do it the right way we discussed' / 'do it that way we agreed on' (reference to a specific prior method), 'don't do it that way' / 'don't do it right with shortcuts' (negation), 'if we do it the right way, it takes longer' / 'if you just do it that way...' (conditional), 'you did it right yesterday' / 'they did it that way last sprint' (past tense), 'how do you do it right in React?' / 'what does \"the right way\" mean here?' (meta-question), 'this is the right way to handle errors' / 'the right way to deploy is X' (descriptive copular sentence, not imperative), 'I want to do it right' / 'we should do it the right way' / 'I'm trying to do it right' (expression of intent or aspiration, not bare imperative invocation), 'you have to do it right or it breaks' (causal/conditional explanation). Rule of thumb: trigger phrase preceded by negation ('don't', 'not', 'não'), conditional ('if', 'when', 'se'), past tense ('did', 'fez'), volitional/modal verbs ('want to', 'try to', 'should', 'have to', 'querer'), or copular descriptive frames ('this is X', 'X is Y'), or followed by qualifiers ('we discussed', 'from before', 'alternativo', 'anterior', 'in React') = REFERENCE/DESCRIPTION, not invocation. The trigger must be a BARE IMPERATIVE — a command standing alone or as the final/leading clause instructing 'apply this approach'. The English triggers ('do it right', 'the right way', 'do it that way', 'just do it that way') are common everyday phrases — apply disambiguation MORE strictly for English, and prefer the unambiguous slash command (/daquele-jeito or /that-way) when there's any doubt. WHEN IN DOUBT, DO NOT ACTIVATE."
---

# Daquele-jeito — workflow for execution projects

Apply this workflow to the user's request and to all subsequent work in this conversation. Applies to structural tasks — code, infra, content, design.

**Activation announcement:** the **first time** this workflow is activated in a conversation, open the response with a short sentence in the format *"Doing the [type of work] daquele jeito..."* — e.g. *"Doing the SaaS planning daquele jeito..."*, *"Doing the bug fix daquele jeito..."*, *"Doing the comparative analysis daquele jeito..."*. In subsequent activations within the same conversation, **don't repeat** — user already knows it's active.

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
- One at a time when it can be a click-question (up to 4 options)
- Short numbered batch if they're interdependent (answering together makes sense)
- Each question must materially change the plan — if the answer changes nothing, cut it

**Don't ask** what `grep`, `cat`, `find` or a direct Read solves (see §6). Ask only what the user knows and the repo doesn't answer.

### 1.2 Plan

Checklist in the conversation, with a "done" criterion per step. Each item should have:
- **Clear scope** — one sentence on what changes
- **Verifiable done** — output, test, command, link (cf. §3)
- **Explicit assumptions** when present, marked (e.g. *"assuming Postgres + Drizzle"*)

If prior discovery (grep/read in §6) informed the plan, mention it briefly — gives the user visibility into what backed the decisions.

**Large projects (>~5 anticipated steps):** sketch **macro phases** first (1-3 phases, one-sentence description each) and detail the full checklist **only for the current phase**. When the current phase closes, detail the next one. Avoids a 30-item plan that ages out before it's executed.

**Research/analysis:** for comparisons, investigations, recommendations, the plan is the **investigative approach** (which sources, which dimensions), not a construction checklist. "Done per step" = question answered with evidence.

### 1.3 Validation

Present the plan, explicitly ask whether the user approves or adjusts it. **Don't execute** without an affirmative signal. If the user just replies "go" or "ok" without reviewing, proceed with the assumptions marked as `[assumed]` in the plan — not silently.

**Why all this:** fixing a plan costs minutes; fixing finished code costs hours. Activating this workflow is the user's way of asking for that protection explicitly — dishonoring the request by executing straight away is the most expensive failure it prevents.

## 2. Visible progress

Signal each **completed step**:

- **Step** = a checklist item from §1 (when there's a plan) or, in smaller tasks without a formal plan, a verifiable sub-goal.
- **Completed** = the "done" criterion for that item has been met with the proof required by §3 (output, test, link, command run).

Update format: 1-2 sentences on what's done, no long report. If there's a visible checklist in the conversation, update the item from `[ ]` to `[x]` when signaling.

Example: *"✓ Step 1 (Auth.js setup): installed, `auth.ts` configured, middleware added. `auth()` in server component returns null as expected. Moving to step 2."*

At the end of the work, short review block: what changed, why, what's still open.

**If discovery during execution invalidates a step or assumption** (`[assumed]` that didn't hold up, a dependency that doesn't exist, scope that grew): pause. Don't force the original plan. Propose an amendment — which step changes, why, what's the impact on the following ones — and validate with the user before proceeding.

## 3. Verification before "done" (audit)

"Done" is an auditable declaration, not a feeling. Before marking any step as complete, run through the four axes below with **the mindset of someone looking for problems, not seeking confirmation**.

Each axis gets an explicit answer: **passed**, **not applicable** (with reason), or **failed** (with a plan).

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
> - Hygiene: ✓ diff reviewed, no debug logs or flagged TODOs
> - Specification: ✓ criterion "`auth()` in server component returns null" verified

If any axis failed or wasn't checked, **don't mark [x]** — deliver the report with the real status and propose the next step (fix, escalate to user, or mark as an explicitly accepted limitation).

### Internal test before the audit

Ask yourself: *"would a senior reviewer approve this?"*. If the answer is "maybe" or "after one more polish", redo before — not after.

## 4. Improvement loop

Before proposing to record a lesson, confirm with the user whether it's a recurring pattern or an isolated case. One-off mistakes become frozen rules that age badly — not worth the noise.

If recurring, propose **where** to record based on the real scope. Recording at the wrong level is what pollutes memory most over time:

- Applies only to specific files → `.claude/rules/<name>.md` with `paths:` in frontmatter
- Applies to the whole project, entire team → `.claude/CLAUDE.md` (committed)
- Applies to the project, only the user → `CLAUDE.local.md` (gitignored)
- Applies across all the user's projects → `~/.claude/CLAUDE.md`

Auto memory (Claude Code v2.1.59+, if enabled) already captures some lessons on its own. Before proposing manual recording, worth checking if it duplicates something auto memory would catch.

## 5. Calibrate effort to the task

Two failure modes to avoid:

- **Kludge (under-engineering):** Quick fix that resolves the symptom but leaves silent technical debt. Accumulates until the code becomes fragile without anyone noticing.
- **Over-engineering:** Disproportionate response to the problem. Refactoring three files to fix a typo, or creating an abstraction for something with a single use case.

Practical rules:

- Trivial fix → the smallest change that solves it. Don't escalate.
- Non-trivial change → before coding, ask yourself "what's the simplest version that doesn't become a kludge?".
- If a hack appears mid-fix → pause. Describe the clean version, ask the user whether to redo now or accept the workaround with an explicit `TODO`. Don't hide kludges inside a diff without flagging.
- For throwaway code (one-off script, exploration): elegance isn't the goal. Minimum that works.

## 6. Bug autonomy

Bug with a clear error/log: diagnose and propose the fix directly, no asking permission to start investigating. In CC that means using Read, Grep, Bash, Git log directly — zero context-switching required from the user just for you to get going.

## 7. Subagents

Use the `Task` tool when you need to: (a) scan multiple sources or angles in parallel, (b) isolate heavy context so it doesn't pollute the main thread, or (c) get an independent second pass — e.g. subagent A finds the bug, subagent B validates the fix without having seen the diagnosis.

Each subagent has its own context; they don't share with you nor with each other. Spawn all from the same round in the same turn (real parallelism) and synthesize only after they all return — don't interpret partially in the middle.

If there are specialized subagents in `.claude/agents/`, prefer them over the generic `Task`: they were designed for the case and tend to have better-calibrated prompts.

## 8. Principles

- **Simplicity first:** the smallest change that solves it, with the smallest impact on the rest.
- **Root cause, not band-aid:** if the symptom disappears but the cause stays, the bug comes back somewhere else.
- **Surgical:** touch only what's necessary; don't refactor what wasn't asked. If you see something wrong along the way, flag it to the user instead of "fixing it drive-by".
