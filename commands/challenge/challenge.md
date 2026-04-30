# /challenge — "Is This the Best You Can Do?"

A quality gate for any work produced in your workspace. Run this against a plan, prompt, deliverable, script, or any output before considering it final.

## Philosophy

When someone brings you work to review, the first question is: "Is this the best you can do?" If the answer is no, send them back to improve it. If the answer is yes, *then* your review drives growth — not remedial fixes.

This command forces that self-critique so work reaches "best I can do" quality before you review it. The goal is version 1 that's already been pressure-tested — not version 4 after three rounds of "what did we miss?"

## When to Use

- After a planning command produces a plan — before approving it
- After writing a prompt, system message, or agent configuration
- After producing a client deliverable, proposal, or email
- After building a workflow, template, or command
- Anytime something feels "done" but hasn't been stress-tested

## How to Run

- `/challenge path/to/file.md` — challenge a specific file
- `/challenge` then paste content — challenge something from outside the workspace
- `/challenge` (no path, no paste) — defaults to challenging the last Claude assistant turn in the current session. If the last turn was a tool result or a trivial confirmation rather than a substantive output, ask: "Nothing reviewable in the last turn. Paste the content or provide a path."
- After any command that produces output — run `/challenge` to gut-check it (uses the no-arg default above)
- `/challenge --quick path/to/file` — single-pass gut-check, compressed report, no iteration cycles
- `/challenge --check [path]` — audit criteria freshness without rewriting
- `/challenge --sync path/to/project` — set up or update Challenge Criteria for a specific project
- `/challenge --sync` — sync Challenge Criteria across all active projects
- `/challenge --prune` — review low-activity heuristics and retire stale entries with consent

**Modes are mutually exclusive.** If multiple mode flags are provided, use the first one and ignore the rest. Priority: `--quick` > `--check` > `--sync` > `--prune`. If no flag is provided, run the full challenge (default critique mode).

---

## Mode: Quick (`--quick`)

If the argument includes `--quick`, run a compressed single-pass evaluation. No iteration cycles, no dimensional breakdown.

1. Read the work being challenged — fully.
2. Read `CLAUDE.md` for Challenge Criteria (if present, weight them heavily).
3. Evaluate holistically — what's strong, what's weak, what's missing. Focus on substance over structure.
4. Produce a compressed report:

```
## Quick Challenge — {what's being reviewed}

### Strong
{1-3 bullets}

### Needs Work
{Numbered list — issue + fix, no fluff}

### Verdict
{SHIP IT / REWORK / RETHINK — with one sentence of reasoning}
```

5. Do not iterate. Do not run Step 8b (agent review). The quick mode is a gut-check, not a full review.
6. **Lightweight heuristic extraction.** If the artifact is a repeatable type (plan, skill, agent, prompt, workflow, script — same detection rules as Step 10), run **Step 10 sub-steps 1-4 only**:
   - Review the findings, filter for systemic issues, increment `seen` / add new entries to `~/.claude/challenge-heuristics.md`, and refresh the Approaching Promotion section.
   - **Skip sub-step 5** (promotion candidate presentation) — that's the expensive part with file edits and user prompts. Quick mode shouldn't ask the user to make decisions about distant target files.
   - **Skip sub-step 6** (Heuristics Update report) — quick mode's compressed report has no place for it. Heuristic counts get rolled into a one-line trailer if anything was added: `Heuristics: +{N} new, +{N} reinforced.`
   This keeps the learning loop alive in quick mode without bloating it.

---

## Mode: Check (`--check`)

Lightweight audit that flags potentially stale Challenge Criteria without rewriting them. Use before deciding whether a full `--sync` is needed.

### `/challenge --check path/to/project` — Single Project

1. Navigate to the project path. Read `CLAUDE.md`.
2. If no `## Challenge Criteria` section exists, report "No criteria found — run `--sync` to create."
3. If criteria exist:
   a. Determine the freshness baseline. First, look for a `<!-- last-updated: YYYY-MM-DD -->` HTML comment immediately under the `## Challenge Criteria` heading — if present, use that date (this is the canonical signal, written by `--sync`). If absent (legacy criteria), fall back to `git blame` on the `## Challenge Criteria` line. Then run `git log --oneline --since="{date}"` to see what changed since.
   b. Scan project structure for new directories, new major files, or removed components.
   c. Compare each criterion against current project state — flag any that reference outdated concepts (renamed modules, removed statuses, changed architectures).
   d. Score staleness: **Fresh** (all criteria still accurate), **Drifting** (1-2 criteria reference outdated state), **Stale** (3+ criteria outdated or project has significantly evolved).
4. Report:

```
## Criteria Check — {project name}

Criteria count: {N}
Last modified: {date from git blame on the Challenge Criteria section}
Commits since: {count}
Staleness: **{Fresh / Drifting / Stale}**

### Findings
- {criterion bullet} — {Fresh / Outdated: reason}
- ...

### Recommendation
{No action needed / Consider running `--sync` to update / `--sync` strongly recommended}
```

### `/challenge --check` — Batch

1. Discover projects (same logic as `--sync` batch discovery).
2. Run the single-project check on each.
3. Produce a summary table:

```
| Project | Criteria | Staleness | Recommendation |
|---------|----------|-----------|----------------|
| {name}  | {N}      | Fresh     | No action      |
| {name}  | {N}      | Stale     | Sync recommended |
```

**Note:** `--check` is an audit, not a critique. Do not run Step 8b (agent review) or Step 10 (heuristic extraction).

---

## Mode: Sync Criteria (`--sync`)

If the argument is `--sync`, switch to **criteria-sync mode** instead of critique mode. This mode manages the `## Challenge Criteria` sections in project CLAUDE.md files — it does not produce Challenge Reports.

### `/challenge --sync path/to/project` — Single Project

1. Navigate to the provided project path.
2. Check if `CLAUDE.md` exists at that path:
   - **No CLAUDE.md found:** Inform the user that no `CLAUDE.md` was found. Ask if the path is correct, or if they'd like to create a minimal one. Do not proceed without a `CLAUDE.md` to write to.
   - **CLAUDE.md exists with `## Challenge Criteria`:** This is an **update**. Read the full CLAUDE.md, scan project structure and recent git history (`git log --oneline -20`), compare existing criteria against current project state, and propose updated criteria. Show old vs new.
   - **CLAUDE.md exists without `## Challenge Criteria`:** This is a **new setup**. Read the full CLAUDE.md, scan project structure and recent git history, and draft new criteria.
3. Present the proposed criteria to the user for approval. The user may approve, modify, or reject.
4. On approval, write the `## Challenge Criteria` section to the project's CLAUDE.md. If the section already exists, replace it. If it doesn't exist, append it at the end of the file.
5. Confirm what was written.

### `/challenge --sync` — All Projects (Batch)

1. Ask the user to confirm they want to sync all active projects.
2. Ask for the root directory containing their projects (or use the current working directory if it looks right).
3. Discover projects by recursively scanning for directories containing `CLAUDE.md` (max depth 4). For each candidate directory:
   a. Skip if the directory is inside a path matched by the nearest ancestor `.gitignore` (e.g., `node_modules/`, `dist/`, `build/`).
   b. Skip if the directory name starts with `.` (hidden directories) except `.claude/`.
   c. Skip if the directory path contains `/archive/` or `/archived/` (completed work).
   d. If a `.primeignore` exists in the candidate, note it — this project has context optimization configured, which signals active maintenance.
4. Present the discovered project list. Ask the user to confirm which projects to sync (all, or a subset).
4b. **Cost estimate.** Before proceeding to analysis, present a budget estimate so the user can size the operation:

   ```
   Sync cost estimate
   - Projects to analyze: {N}
   - Per-project context: ~5K tokens (CLAUDE.md + structure scan + git log + criteria draft)
   - Estimated total: ~{N × 5}K tokens
   - Mode: [Live write / Dry run (analyze and present, don't write)]

   Proceed? (yes / dry-run first / cancel)
   ```

   - **yes** — proceed to step 5 with live writes (subject to step 8 approval)
   - **dry-run first** — proceed through analysis and presentation but skip step 9 writes; user can re-invoke `--sync` after reviewing
   - **cancel** — abort

   Default to dry-run when N > 10 projects (large batches deserve preview-first as the default).
5. **Stack clustering.** Before analyzing individually, group confirmed projects by tech stack signature (language + framework + data layer). For each cluster of 2+ projects:
   - Identify shared concerns (e.g., all Python/FastAPI/SQLite projects share WAL concurrency concerns)
   - Draft shared baseline criteria for the cluster
   - When generating per-project criteria, start from the cluster baseline and add project-specific concerns
   - In the batch presentation, note which criteria came from cluster baselines vs. project-specific analysis
   - Single-project clusters skip this step — analyze individually as before.
6. For each confirmed project, analyze and generate criteria (see "What Sync Evaluates" below), incorporating cluster baselines where applicable.
7. Present ALL proposed criteria as a batch — one section per project showing the proposed bullets. For projects with existing criteria, show old vs new.
8. The user approves all, approves selectively, or requests modifications per project.
9. On approval, write the `## Challenge Criteria` sections to each project's CLAUDE.md.
10. Produce a sync summary:

```
## Sync Summary

| Project | Cluster | Status | Criteria Count |
|---------|---------|--------|----------------|
| {name}  | {stack} | Created / Updated / Skipped | {N} bullets ({shared} shared + {specific} specific) |
| ...     | ...     | ...    | ...            |
```

### What Sync Evaluates

When generating criteria for a project, analyze:

- **Tech stack** — Languages, frameworks, infrastructure. What are the common failure modes and quality concerns for this stack?
- **Business stakes** — Client work (contractual obligations, delivery milestones)? Internal tooling (operational reliability, uptime)? Experimental (learning, lower stakes)?
- **Deployment model** — Docker/Doppler, GitHub Actions, manual, local only? What could break in production?
- **Security posture** — Secrets management, user-facing inputs, API surface area, access control model.
- **Domain constraints** — Business rules, regulatory requirements, audience skill level, contractual scope.
- **Recent evolution** — New modules, phase transitions, architectural shifts visible in git history or project structure.

### Criteria Format

Each project's criteria should follow this format in the CLAUDE.md:

```markdown
## Challenge Criteria

<!-- last-updated: YYYY-MM-DD -->

When `/challenge` runs in this project, additionally evaluate:
- **{Concern}** — {What to check and why it matters for this specific project}
- **{Concern}** — {What to check and why it matters for this specific project}
- ...
```

When `--sync` writes or updates the section, set the `last-updated` date to today (this is the freshness signal `--check` reads). On update, replace the existing comment rather than appending a new one.

Guidelines for criteria:
- **3-5 bullets per project.** Enough to be useful, not so many that they dilute focus.
- **Be specific to the project.** Reference actual concepts — module names, API specs, deployment targets, business rules found during analysis. Generic advice like "handle errors properly" is not a criterion.
- **Focus on what would be embarrassing to miss.** Each bullet should catch something that a generic challenge review would overlook.
- **No duplication with global rules.** If the user's global CLAUDE.md rules already cover something (e.g., security, immutability), don't repeat it in criteria. Focus on what's unique to this project.

**After sync completes, do not enter critique mode. The sync is its own workflow.**

---

## Mode: Prune (`--prune`)

Retire stale or unproductive heuristics from `~/.claude/challenge-heuristics.md` (or `.claude/challenge-heuristics.md` in the project) with user consent. This mode does not produce a Challenge Report — it manages the heuristic ledger.

### Purpose

Heuristics are extracted from real challenge findings, but some never reproduce. Without a retirement workflow, the Active section drifts toward noise. `--prune` surfaces low-activity entries for per-item Keep / Retire decisions. **Retired entries are preserved in a `## Retired` section, never deleted.**

### Process

1. **Load the heuristics file.** Read `~/.claude/challenge-heuristics.md` (global) or `.claude/challenge-heuristics.md` (project-local) if it exists. If both exist, prefer the project-local one and note the global one wasn't pruned.
2. **Identify retirement candidates.** A candidate has BOTH:
   - `seen: 1` (only one extraction event), AND
   - `last_seen` more than 30 days ago (relative to today's date).

   These thresholds are soft — flag candidates, do not auto-retire.
3. **Present per-entry prompts.** For each candidate:

   ```
   ### {Heuristic Name}
   Last seen: {date} ({N days ago})
   Cluster: {cluster or "ungrouped"}
   Stack: {stack tags}
   Source: {source or "unknown (pre-schema)"}

   **Check:** {check}
   **Why:** {why}

   Keep / Retire / Skip? (default: Keep)
   ```

4. **Process responses.**
   - **Keep:** leave entry in Active, advance to next.
   - **Retire:** ask one-line reason ("stale, never reproduced" / "rejected after review" / etc.), then move the entry to the `## Retired` section using the Retired entry format. Append a `(retired YYYY-MM-DD)` suffix to the heading.
   - **Skip:** advance, do not modify.
5. **Produce a prune summary:**

   ```
   ## Prune Summary

   Candidates surfaced: {N}
   - Kept: {N}
   - Retired: {N}
   - Skipped: {N}

   Active heuristics remaining: {N}
   ```

6. **No critique cycles.** This mode does not run Step 5 dimensional review, Step 8b agent dispatch, or Step 10 heuristic extraction.

### Rules

- **Never retire without consent.** Each candidate gets its own prompt. Bulk-retire is not a feature.
- **Never delete.** Retired entries move to `## Retired`, where they remain visible for audit.
- **Never re-prune within 30 days.** If you just retired or kept an entry, do not surface it again until at least 30 days have passed (track via the `last_seen` or a `last_pruned` annotation if added).
- **Approaching Promotion entries are off-limits.** Heuristics with `seen: 2` (one trigger from promotion) are never prune candidates regardless of age.

---

## Steps

1. Read `CLAUDE.md` for workspace conventions and priorities. Look for a `## Challenge Criteria` section — if present, incorporate those project-specific standards into your evaluation, weighting them heavily in the "Fit for Purpose" and "Risk & Blind Spots" dimensions. If no Challenge Criteria section exists, evaluate using the generic dimensions below.
2. Read the work being challenged — fully, not skimming
3. Identify what this work is (plan, prompt, script, deliverable, code, template, etc.) and what it's trying to accomplish. Use the artifact-type detection rules from Step 10 (`Plan`, `Skill/Command`, `Agent`, `Prompt`, `Workflow`, `Script`).
4. Read any relevant context — project state files, the brief it was built from, the problem it's solving. Don't critique in a vacuum.
4b. **Load applicable heuristics.** If the artifact type was identified in Step 3, load `~/.claude/challenge-heuristics.md` (and `.claude/challenge-heuristics.md` if it exists in the project — project-local takes precedence on duplicates). From the **Active** section, select entries matching:
   - `Artifact types:` includes the detected type, AND
   - `Stack:` is `generic` OR overlaps with the project's stack (inferred from `CLAUDE.md`, dependency files, or directory layout). Default to `generic` if stack is unknown.

   Cap the loaded subset at the 8 most-recently-seen matches to bound context cost. If `--quick` mode, skip this step. Hold the loaded heuristics for use in Step 5.

5. Switch into critical reviewer mode. Not adversarial — rigorous. The goal is to surface everything that a thoughtful expert would catch. **If the artifact is source code, dispatch the appropriate reviewer agent now (per Step 8b) so it runs concurrently with the dimensional review below — harvest its findings when assembling the Final Report in Step 9.** Evaluate across these dimensions, adapting weight based on what's being reviewed:

### Completeness
- Does it cover everything it should? What's missing?
- Are there edge cases, failure modes, or scenarios that were ignored?
- Would someone executing this hit a dead end because something wasn't addressed?
- Are there unstated assumptions that need to be made explicit?

### Clarity
- Is every part unambiguous? Could anything be misread or misinterpreted?
- Would someone unfamiliar with the backstory know exactly what to do?
- Is anything vague where it should be specific?
- Are instructions actionable or just directional?

### Logic & Structure
- Does the reasoning hold up? Are there logical gaps or unjustified leaps?
- Is the sequence right? Are dependencies respected?
- Is there unnecessary complexity that could be simplified without losing quality?
- Does the structure serve the content or fight against it?

### Fit for Purpose
- Does this actually solve the problem it's supposed to solve?
- Is it aligned with the goals, priorities, and constraints of the project?
- Does it account for real-world constraints — time, tools, skill level, budget?
- Is it the right level of depth for its audience?

### Risk & Blind Spots
- What could go wrong that isn't addressed?
- What assumptions is this built on — and are they actually valid?
- Is there a "what if" scenario that would break this?
- What would someone trying to poke holes in this say?

### The Bar
- If the creator was asked "is this honestly the best you could do?" — would the answer be yes?
- What separates this from genuinely excellent work of the same type?
- What would make someone look at this and say "this person knows what they're doing"?

### Heuristics Check
For each heuristic loaded in Step 4b, verify the artifact addresses what the heuristic's `Check:` requires. Treat heuristics as a checklist of patterns from prior failures. For each:
- **Pass** — the artifact already handles this. Note silently; do not pad the report.
- **Fail** — the artifact has the gap the heuristic warns about. Surface as a finding under "What Needs Work" annotated with `(heuristic: {name})`.
- **N/A** — the heuristic's `Applies when:` condition isn't met by this artifact. Skip silently.

After running the dimensional pass, increment the `seen` count and update `last_seen` for any heuristics whose Check fired (not just whose `Applies when` matched). This drives the promotion / pruning lifecycle.

6. Produce the Challenge Report:

```
## Challenge Report — {what's being reviewed}

### What's Strong
{Specific things done well — genuine, not just softening for the critique}

### What Needs Work
1. **{Issue}** — {What's wrong and why it matters}
   → Fix: {Specific, actionable change}
2. ...

### What's Missing
{Things that should exist but don't — real gaps, not nice-to-haves}

### The Hard Question
Is this the best you can do? {Direct yes/no with reasoning}

### Verdict
{One of:}
- SHIP IT — Solid work. Suggestions above are polish, not problems.
- REWORK — Good foundation, real gaps. Fix the issues and re-evaluate.
- RETHINK — The approach itself has problems. Step back before fixing details.
- ESCALATE — (Final Report only) 3-cycle cap reached and the work still isn't shippable. Surface for human redesign rather than further iteration. Do not emit on the initial critique pass.
```

7. **If the work was produced by Claude in this session** (a plan, a prompt, a draft — something Claude wrote):
   - If verdict is REWORK: apply all fixes, then re-run the challenge evaluation on the improved version. **Counts as one cycle.**
   - If verdict is RETHINK: explain the fundamental issue, propose a revised approach, get the user's input before rebuilding. **A user-approved rebuild after RETHINK counts as one cycle** (same as REWORK). The cap doesn't reset just because the approach changed.
   - If verdict is SHIP IT: move to step 9.
   - **Hard cap: 3 cycles maximum total** (initial critique + up to 2 rebuild passes of any kind — REWORK, RETHINK-then-rebuild, or any mix). After cycle 3, stop iterating regardless of verdict. If it's not there in 3 rounds, the approach needs human redesign, not more iteration.
   - **Max 1 RETHINK-rebuild per session.** A second RETHINK verdict (after the first rebuild) is the system telling you the approach is fundamentally wrong. Don't loop on RETHINK — escalate.
   - **Cap-reached + still REWORK → emit ESCALATE in the Final Report.** This signals "polishing won't fix this, the approach itself needs human redesign." Distinct from RETHINK, which is an in-cycle judgment; ESCALATE is the post-cap exit signal.
   - **Second RETHINK before cap → also emit ESCALATE.** If the rebuilt version is itself diagnosed as RETHINK, the system has reached the limit of what self-iteration can do.

8. **If the work was pasted in or is external** (something the user wrote, a client deliverable, third-party content):
   - Present the challenge report as-is. The user decides what to do with the feedback.
   - Do not auto-fix. The point is to give the critique — the user drives the improvements.
   - Skip to step 8b (if code) or step 9.

## Step 8b: Agent-Assisted Review (Code Only)

**This step runs only when the challenged work is source code** (a `.py`, `.ts`, `.go`, `.js` file or a code-heavy change). Skip for plans, prompts, and non-code artifacts.

1. Identify the language/stack of the code being challenged.
2. Dispatch the reviewer agent **at the start of Step 5** (per the note in Step 5), so the agent's review runs concurrently with the dimensional critique. Harvest results when assembling the Final Report (Step 9). Choose the agent based on the language:
   - Python → `python-reviewer` agent
   - Go → `go-reviewer` agent
   - TypeScript/JavaScript → `code-reviewer` agent
   - Any code touching auth, user input, or APIs → additionally dispatch `security-reviewer` agent
   - If no specific agent exists for the language, fall back to the generic `code-reviewer` agent.
3. Incorporate agent findings into the Final Challenge Report (Step 9):
   - Agent-sourced findings go under a **### Agent Review Findings** subsection within "What Needs Work" or "Remaining Findings"
   - De-duplicate: if the main challenge already flagged the same issue, keep the more specific version
   - Agent findings that don't overlap with the main challenge dimensions add genuine depth — especially security and performance concerns
4. **On iteration cycles** (Step 7 REWORK loop): re-dispatch agents only if the fixes touched code that agents originally flagged. Skip re-dispatch if fixes were non-code (clarity, structure, completeness improvements).

**Note:** Agent dispatch is automatic for code artifacts. If the user wants to skip it (e.g., for speed), they should use `--quick` mode instead.

9. **Always produce the Final Challenge Report.** This is what the user sees — every time, no exceptions.

```
## Final Challenge Report — {what was reviewed}

### Iteration Summary
- Cycles run: {1-3}
- Starting verdict: {SHIP IT / REWORK / RETHINK}
- Final verdict: {SHIP IT / REWORK / RETHINK / ESCALATE — `ESCALATE` only when 3-cycle cap was reached and work still isn't shippable}

### What Was Improved (if iterations occurred)
{Numbered list of specific changes made across cycles — what changed and why}

### What's Strong
{Specific things done well in the final version}

### Remaining Findings
{Anything the final critique pass still flagged — things that weren't critical enough
to justify another cycle, or items left when the cap was reached. Each item includes:}
1. **{Finding}** — {What it is, why it matters, and what the fix would be}
   Severity: {Critical / Worth addressing / Minor polish}
2. ...
{If nothing remains, say so: "None — final version passed clean."}

### Your Call
The work above is the best this process could produce. Remaining findings
(if any) are yours to act on or set aside. Options:
- **Accept as-is** — ship the current version
- **Address specific items** — tell me which remaining findings to implement
- **Send it back** — if something fundamental still bothers you
```

The user always gets the final say. The challenge process does the heavy lifting, but it never ships without approval.

## Step 10: Heuristic Extraction

**This step runs when the challenged work is a repeatable artifact type:** implementation plans, skill/command definitions, agent configurations, system prompts, or workflow definitions. Skip for one-off deliverables (emails, reports, client proposals).

Detect artifact type:
- **Plan**: file matches `pl-*.md`, or contains `## IMPLEMENTATION PLAN` or `## Implementation Steps`
- **Skill/Command**: file is under `.claude/commands/` or matches skill definition structure (has `## Steps` or `## Rules`)
- **Agent**: file is under `.claude/agents/`
- **Prompt**: file contains system prompt markers (`## System Prompt`, `<system>`, or similar)
- **Workflow**: file is under `workflows/` or `engine/workflows/`
- **Script**: file matches `*.sh` or `*.bash`, starts with a shell shebang (`#!/bin/bash`, `#!/usr/bin/env bash`, `#!/bin/sh`), or is under `scripts/` or `bin/`

After the Final Challenge Report is delivered:

1. **Review the findings.** Look at "What Needs Work," "What's Missing," and "Remaining Findings" from the final report.

2. **Filter for systemic issues.** A finding is systemic if it reflects a **category of weakness for this artifact type**, not a one-off content gap. Examples:
   - Systemic: "No rollback steps for deployment changes" → applies to any plan involving deployments
   - Systemic: "Error handling paths not specified for API integrations" → applies to any plan with external API calls
   - Not systemic: "Missing step to update the sidebar component" → specific to this plan only

3. **Check the heuristics file.** If `.claude/challenge-heuristics.md` does not exist but `.claude/planning-heuristics.md` does, rename it to `.claude/challenge-heuristics.md` (preserving all content) and add `- **Artifact types:** plan` to each existing entry that lacks the field. Do not create a duplicate file. If neither exists, create `.claude/challenge-heuristics.md` from the template below. For each systemic finding:
   - **Already exists:** Increment the `seen` count and update `last_seen` date. Do not modify other fields.
   - **New:** Add a new entry in the Active section with all schema fields populated:
     - `Artifact types:` — the detected artifact type(s) for this critique
     - `Stack:` — comma-separated tags inferred from the project (read `CLAUDE.md`, dependency files, or directory layout). Default to `generic` if no stack is identifiable. If uncertain between two stacks, list both rather than guessing one.
     - `Cluster:` — match against existing cluster names in the file (e.g., `Shell Script Robustness`, `Pipeline Symmetry`, `Plan Discipline`, `Coverage Completeness`). If the new heuristic doesn't fit any existing cluster, leave empty (`""`); do not invent a single-entry cluster.
     - `Source:` — path of the artifact reviewed (e.g., `.claude/plans/pl-foo-2026-04-30:14.md`). If the artifact came from a paste rather than a file, use `pasted (session {YYYY-MM-DD:HH})`.
     - `Seen:` — initialized to `1`. `First seen:` and `Last seen:` set to today's date.
4. **Maintain the Approaching Promotion section.** After any heuristic add or `seen` increment, regenerate the section by listing all Active entries where `seen == 2` (one trigger from the seen-3+ promotion threshold). This is auto-maintained — do not curate manually.

5. **Check for promotion candidates.** Any active heuristic with `seen: 3+` is a promotion candidate. For each:
   - Draft the specific change to the appropriate target file based on artifact type:
     - Plans → `/plan` command (`~/.claude/commands/plan.md`) or planner agent (`~/.claude/agents/planner.md`)
     - Skills/Commands → the specific skill file that would benefit
     - Agents → the specific agent file
     - Prompts/Workflows → note in the heuristic where the fix was applied
     - Scripts → the specific script or the relevant pattern doc that would benefit
   - Present to the user: "This heuristic has proven its value (seen N times). Promote to permanent instruction?"
   - On approval: make the edit, move the heuristic to the Promoted section with a `promoted_to` reference
   - On rejection: leave as Active (user may want more evidence)

6. **Report.** After the Final Challenge Report, add a brief heuristics summary:

   ```
   ### Heuristics Update
   - New heuristics added: {N}
   - Existing heuristics reinforced: {N}
   - Approaching promotion (seen: 2): {N}
   - Promotion candidates (seen 3+): {N}
   ```

### Challenge Heuristics File Template

If `.claude/challenge-heuristics.md` does not exist when needed (and no `.claude/planning-heuristics.md` to migrate), create it with this structure:

```markdown
# Challenge Heuristics

Accumulated lessons from /challenge reviews of repeatable artifacts (plans, skills, agents, prompts, workflows, scripts).
These heuristics are loaded by /plan, the planner agent, and /challenge itself (artifact-type-filtered) to prevent recurring gaps.
Lifecycle: Active → seen 3+ becomes Promoted (absorbed into permanent target files) → low-activity entries may be Retired via `/challenge --prune` (preserved for audit, not deleted).

## Active

<!-- Each entry: what to check, why it matters, when it applies, artifact types, stack, cluster, source, tracking -->

## Approaching Promotion

<!-- Auto-maintained by Step 10. List Active entries with seen == (promotion_threshold - 1). -->

## Promoted

<!-- Heuristics that proved effective and were absorbed into their target files permanently -->

## Retired

<!-- Heuristics retired via /challenge --prune (preserved for audit, not deleted) -->

## Format Reference

<!-- Active entry format:
### {Heuristic Name}
- **Check:** {What to verify in the artifact}
- **Why:** {What goes wrong when this is missed}
- **Applies when:** {Conditions — e.g., "plan involves deployment changes", "skill defines a new mode"}
- **Artifact types:** {plan, skill, agent, prompt, workflow, script — which types this applies to}
- **Stack:** {comma-separated tags: python, typescript, react, node, go, shell, docker, sql, frontend, backend, security, generic — defaults to "generic" when not stack-specific}
- **Cluster:** {short label grouping related heuristics, e.g., "Shell Script Robustness", "Pipeline Symmetry", "Plan Discipline" — empty string for ungrouped}
- **Source:** {path to the artifact reviewed when the heuristic was extracted, OR "unknown (pre-schema)" for legacy entries}
- **Seen:** {count} | **First seen:** {date} | **Last seen:** {date}
-->

<!-- Promoted entry format:
### {Heuristic Name} (promoted {date})
- **Promoted to:** {file path and section where this now lives}
- **Original check:** {What it was}
-->
```

---

## Rules

- **Be specific.** "This could be better" is useless. "Step 3 doesn't account for empty API responses — add a fallback for when the API returns no results" is useful. Every issue needs a concrete fix.
- **Be honest.** The entire point is to find real problems. If the work is genuinely good, say so. If it's not, say that too. No rubber-stamping.
- **Stay in scope.** A plan critique stays about the plan. Don't redesign the business strategy, rewrite the workspace, or expand into adjacent concerns. Challenge what's in front of you.
- **Don't nitpick.** Style preferences, formatting opinions, and "you could also..." suggestions are not issues. Focus on substance — things that affect whether this work actually succeeds at its job.
- **Know when to stop.** Diminishing returns are real. If iteration changes are getting smaller and more subjective, it's time to ship. Perfectionism is a stall trigger — this command should fight it, not feed it. The 3-cycle hard cap enforces this.
- **Track iterations.** If it takes all 3 cycles without reaching SHIP IT, emit `ESCALATE` as the final verdict (not REWORK or RETHINK). ESCALATE means "polishing won't fix this; the approach itself needs human redesign." Note prominently in the Final Challenge Report — that's useful information for improving the first attempt next time.
- **Credit what's good.** "What's Strong" is not a formality. If the work nails something, name it. People need to see what's working, not just what's broken.
- **Heuristics are earned, not invented.** Only extract heuristics from actual challenge findings on actual repeatable artifacts (plans, skills, agents, prompts, workflows). Never pre-populate heuristics speculatively. The system learns from real failures, not hypothetical ones.
- **Promotion preserves, retirement requires consent.** A heuristic that stops appearing is working — promote it, don't delete it. Only remove a heuristic if the user explicitly rejects it after review.
