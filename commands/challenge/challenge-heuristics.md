# Challenge Heuristics

Accumulated lessons from /challenge reviews of repeatable artifacts (plans, skills, agents, prompts, workflows, scripts).
These heuristics are loaded by /plan, the planner agent, and /challenge itself (artifact-type-filtered) to prevent recurring gaps.
Lifecycle: Active → seen 3+ becomes Promoted (absorbed into permanent target files) → low-activity entries may be Retired via `/challenge --prune` (preserved for audit, not deleted).

## Active

### Cross-Platform Shell Compatibility
- **Check:** When plan targets multiple OS platforms, verify the shell config strategy accounts for different default shells (bash vs zsh vs fish)
- **Why:** macOS defaults to zsh since Catalina (2019). Plans that create `.bashrc` files for macOS will silently fail — the config never gets loaded.
- **Applies when:** Plan involves dotfile deployment, shell configuration, or cross-platform CLI tooling setup
- **Artifact types:** plan
- **Stack:** shell
- **Cluster:** Cross-Platform Compatibility
- **Source:** unknown (pre-schema)
- **Seen:** 1 | **First seen:** 2026-04-03 | **Last seen:** 2026-04-03

### Deploy/Sync Symmetry
- **Check:** When plan has both a deploy (repo → machine) and sync (machine → repo) pipeline, verify they use the same file operations. If sync uses `rsync --delete`, deploy should too.
- **Why:** Asymmetry between `cp -r` (deploy) and `rsync --delete` (sync) means file deletions don't propagate on re-deploy. Removed commands/agents persist on target machines.
- **Applies when:** Plan involves bidirectional config synchronization between a repo and live system
- **Artifact types:** plan
- **Stack:** shell, generic
- **Cluster:** Pipeline Symmetry
- **Source:** unknown (pre-schema)
- **Seen:** 1 | **First seen:** 2026-04-03 | **Last seen:** 2026-04-03

### Gitignore vs Force-Include Conflicts
- **Check:** When plan adds a flag to optionally include normally-excluded content, verify the `.gitignore` won't silently block the commit even after files are synced to the repo directory
- **Why:** Users will run the sync, see files in the directory, try to commit, and nothing happens. Silent git ignoring is confusing.
- **Applies when:** Plan involves optional inclusion of gitignored content (learned instincts, generated reports, cache files)
- **Artifact types:** plan
- **Stack:** generic
- **Cluster:** Pipeline Symmetry
- **Source:** unknown (pre-schema)
- **Seen:** 1 | **First seen:** 2026-04-03 | **Last seen:** 2026-04-03

### Multi-Entry-Point Validation Coverage
- **Check:** When plan adds a validation rule (e.g., cutoff date, business constraint), verify it's applied at ALL entry points that create or approve the entity — not just the primary CRUD endpoint. Check intake pipelines, bulk imports, approval flows, and webhook handlers.
- **Why:** Validation in `create_order()` only catches manual orders. Orders from web forms, Drive watcher, intake approval, or future channels bypass it silently.
- **Applies when:** Plan adds a new business rule validation to an entity that has multiple creation/ingestion paths
- **Artifact types:** plan
- **Stack:** generic
- **Cluster:** Coverage Completeness
- **Source:** unknown (pre-schema)
- **Seen:** 2 | **First seen:** 2026-04-03 | **Last seen:** 2026-04-30

### Cron Frequency Must Match Retention Policy
- **Check:** When a plan sets a data retention window (e.g., "delete after 2 days") AND creates a recurring cleanup job, verify the job runs frequently enough to enforce the policy. A weekly cron with a 2-day retention means files accumulate for up to 9 days.
- **Why:** Retention policy gives a false sense of security if enforcement is too infrequent. The actual retention becomes `policy + schedule_interval`, not just the policy.
- **Applies when:** Plan involves data retention policies with automated cleanup (cron, scheduled tasks, garbage collection)
- **Artifact types:** plan
- **Stack:** generic
- **Cluster:** Coverage Completeness
- **Source:** unknown (pre-schema)
- **Seen:** 1 | **First seen:** 2026-04-05 | **Last seen:** 2026-04-05

### Research Findings Must Map to Tasks
- **Check:** When a plan has a research/findings section and a tasks section, verify every PENDING or CRITICAL finding in the research has a corresponding task. Scan for findings marked "PENDING" with no task, or research categories that were discovered but dropped.
- **Why:** OpenClaw session logs (4.8M, HIGH) and WhatsApp .bak files (CRITICAL) were found in research but had no remediation task — they fell through the cracks between research and planning.
- **Applies when:** Plan has separate research/discovery and implementation phases
- **Artifact types:** plan
- **Stack:** generic
- **Cluster:** Plan Discipline
- **Source:** unknown (pre-schema)
- **Seen:** 3 | **First seen:** 2026-04-05 | **Last seen:** 2026-04-30

### Navigation Registration for New Views
- **Check:** When plan creates a new frontend view/page, verify it also includes steps to register the route in App.tsx AND add a sidebar/nav entry in the navigation component. A view without a nav link is unreachable by users.
- **Why:** AssignmentView was created with a route but no sidebar entry — users couldn't find the page without typing the URL directly.
- **Applies when:** Plan creates new frontend views or pages that need to be user-accessible
- **Artifact types:** plan
- **Stack:** react, frontend
- **Cluster:** Frontend View Lifecycle
- **Source:** unknown (pre-schema)
- **Seen:** 1 | **First seen:** 2026-04-04 | **Last seen:** 2026-04-04

### Frontend-Backend Task Symmetry
- **Check:** When a frontend task depends on data not currently returned by the API, verify a corresponding backend task exists to provide that data. A frontend column without a backend field is a dead end.
- **Why:** Frontend tasks that say "add pricing columns" will fail at implementation if no backend task extends the API response to include pricing data.
- **Applies when:** Plan includes frontend UI enhancements that display new data fields
- **Artifact types:** plan
- **Stack:** frontend, backend
- **Cluster:** Pipeline Symmetry
- **Source:** unknown (pre-schema)
- **Seen:** 1 | **First seen:** 2026-04-03 | **Last seen:** 2026-04-03

### No Wildcards in Sudoers
- **Check:** When a plan creates sudoers entries, verify no entry uses wildcards (`*`) in the command arguments. Wildcards in sudoers allow privilege escalation — `find` with `*` permits `-exec` to run arbitrary commands as root.
- **Why:** VPS security audit plan had `deploy ALL=(root) NOPASSWD: /usr/bin/find /home/deploy -maxdepth 4 *` and `/usr/bin/stat *` — both allow arbitrary root command execution, undermining the least-privilege rationale.
- **Applies when:** Plan creates or modifies sudoers files, sudo configurations, or privilege escalation rules
- **Artifact types:** plan, script
- **Stack:** shell, security
- **Cluster:** Shell Script Robustness
- **Source:** unknown (pre-schema)
- **Seen:** 1 | **First seen:** 2026-04-11 | **Last seen:** 2026-04-11

### Per-Unit Failure Isolation in Loops
- **Check:** When a plan has a script that loops over multiple targets (servers, repos, services), verify that a failure on one target doesn't block processing of subsequent targets. Each iteration needs its own error handling, failure recording, and continue logic.
- **Why:** VPS audit plan had a single failure stub for a sequential loop over 2 VPSs. A network timeout on VPS 1 would have prevented VPS 2 from being audited, silently halving coverage.
- **Applies when:** Plan involves scripts that iterate over multiple independent targets (servers, repos, containers, environments)
- **Artifact types:** plan, script
- **Stack:** shell, generic
- **Cluster:** Shell Script Robustness
- **Source:** unknown (pre-schema)
- **Seen:** 3 | **First seen:** 2026-04-11 | **Last seen:** 2026-04-30

### Shell Error Flag Compatibility
- **Check:** When a plan specifies `set -euo pipefail` for a script that also uses `|| true` or `|| echo` fallbacks, verify that `-e` (errexit) won't conflict. In some contexts, `-e` causes exit before `|| true` can execute (especially in subshells and pipelines).
- **Why:** VPS collection script plan specified `set -euo pipefail` (from one pattern) but also said "each command wrapped in `|| true`" (from another pattern). The WSL2 audit it was mirroring actually uses `set -uo pipefail` (no `-e`) precisely for this reason.
- **Applies when:** Plan creates shell scripts with graceful-degradation requirements (partial results better than no results)
- **Artifact types:** plan, script
- **Stack:** shell
- **Cluster:** Shell Script Robustness
- **Source:** unknown (pre-schema)
- **Seen:** 2 | **First seen:** 2026-04-11 | **Last seen:** 2026-04-16

### Verify Untested External Syntax Before Committing
- **Check:** When a plan introduces syntax for an external tool that has no precedent in the workspace (`@import` references, undocumented CLI flags, third-party config keys, hook syntax), include a verification task that empirically tests the syntax in isolation before dependent tasks rely on it. The verification task should produce a yes/no signal and document the fallback strategy.
- **Why:** An assumed-but-untested syntax becomes a silent failure — the plan completes execution but the integration doesn't actually work. CL-v2 plan originally used `@instincts.md` in CLAUDE.md without verifying Claude Code's import syntax existed; would have shipped a broken auto-load. Fixed by adding T3.4 isolated probe and T3.5 fallback path.
- **Applies when:** Plan integrates with an external tool's import syntax, configuration DSL, hook system, or any syntax not already used elsewhere in the project
- **Artifact types:** plan
- **Stack:** generic
- **Cluster:** Plan Discipline
- **Source:** .claude/plans/pl-claude-cl-v2-activation-2026-04-30:00.md
- **Seen:** 1 | **First seen:** 2026-04-30 | **Last seen:** 2026-04-30

### Stop-Conditions Belong in IMPLEMENT, Not GOTCHA
- **Check:** When a plan task has a "wait for X / when ready / completion criteria" condition, verify it's stated in the IMPLEMENT field with a concrete check command, not buried in GOTCHA where it can be skimmed past.
- **Why:** GOTCHA documents risks; IMPLEMENT documents what to do. Executors read IMPLEMENT first and may skip GOTCHA when the task seems routine. CL-v2 migration review's "no `[edit this line]` markers remain" was buried in GOTCHA — the apply step would have silently skipped files without surfacing the incomplete review. Fixed by promoting to IMPLEMENT and adding a hard pre-flight gate.
- **Applies when:** Plan task has a user-driven gate, a wait-for-condition, or any pre-condition that determines task completion
- **Artifact types:** plan
- **Stack:** generic
- **Cluster:** Plan Discipline
- **Source:** .claude/plans/pl-claude-cl-v2-activation-2026-04-30:00.md
- **Seen:** 1 | **First seen:** 2026-04-30 | **Last seen:** 2026-04-30

### Generative Pipelines Need Rollback Paths
- **Check:** When a plan activates a system that auto-generates content (LLM-driven, observer-driven, agent-driven, ML-driven), verify NOTES or a dedicated task includes a concrete rollback procedure. The procedure must specify: stop signal, archive procedure, blank-state recovery, root-cause-before-restart.
- **Why:** Generative systems can produce broken output faster than humans review it. Without a documented rollback, recovery becomes an emergency rather than a procedure. CL-v2 plan originally had no rollback for observer-gone-rogue; if observer produced 50 garbage instincts, no clear path to clean state. Fixed by adding 6-step rollback procedure to NOTES with explicit "do not restart until root cause is understood."
- **Applies when:** Plan activates an LLM-driven, agent-driven, or observer-driven content/data generation pipeline
- **Artifact types:** plan
- **Stack:** generic, ai-systems
- **Cluster:** Generative Pipeline Discipline
- **Source:** .claude/plans/pl-claude-cl-v2-activation-2026-04-30:00.md
- **Seen:** 1 | **First seen:** 2026-04-30 | **Last seen:** 2026-04-30

## Approaching Promotion

Heuristics one trigger from the seen-3+ promotion threshold. Review for early promotion if the pattern is already well-established.

- **Research Findings Must Map to Tasks** — seen 2 | last seen 2026-04-16
- **Per-Unit Failure Isolation in Loops** — seen 2 | last seen 2026-04-16
- **Shell Error Flag Compatibility** — seen 2 | last seen 2026-04-16

<!-- Maintained by /challenge Step 10. List Active entries where seen == (promotion_threshold - 1). Update on every heuristic add/increment. -->

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

<!-- Retired entry format:
### {Heuristic Name} (retired {date})
- **Reason:** {Why retired — e.g., "stale, never reproduced; rejected on review"}
- **Original check:** {What it was}
-->
