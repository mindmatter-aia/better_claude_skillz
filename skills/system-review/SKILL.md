---
name: system-review
description: Analyze implementation against plan for process improvements
argument-hint: "[plan] [report]"
---

# System Review: Process Improvement Analysis

Perform a meta-level analysis of how well the implementation followed the plan and identify process improvements.

## Purpose

**System review is NOT code review.** You're not looking for bugs in the code - you're looking for bugs in the process.

**Your job:**

- Analyze plan adherence and divergence patterns
- Identify which divergences were justified vs problematic
- Surface process improvements that prevent future issues
- Suggest updates to Layer 1 assets (CLAUDE.md, plan templates, commands, references)

**Philosophy:**

- Good divergence reveals plan limitations -> improve planning
- Bad divergence reveals unclear requirements -> improve communication
- Repeated issues reveal missing automation -> create commands

## Context & Inputs

You will analyze these key artifacts:

**Plan File:** `$ARGUMENT1`
Read this to understand what the agent was SUPPOSED to do.

**Execution Report:** `$ARGUMENT2`
Read this to understand what the agent ACTUALLY did and why.

**Planning Command:**
Read your planning command to understand the planning process.

**Execution Command:**
Read your execution command to understand the execution process.

## Analysis Workflow

### Step 1: Understand the Planned Approach

Read the plan file and extract:

- What features were planned?
- What architecture was specified?
- What validation steps were defined?
- What patterns were referenced?

### Step 2: Understand the Actual Implementation

Read the execution report and extract:

- What was implemented?
- What diverged from the plan?
- What challenges were encountered?
- What was skipped and why?

### Step 3: Classify Each Divergence

For each divergence identified in the execution report, classify as:

**Good Divergence (Justified):**

- Plan assumed something that didn't exist in the codebase
- Better pattern discovered during implementation
- Performance optimization needed
- Security issue discovered that required different approach

**Bad Divergence (Problematic):**

- Ignored explicit constraints in plan
- Created new architecture instead of following existing patterns
- Took shortcuts that introduce tech debt
- Misunderstood requirements

### Step 4: Trace Root Causes

For each problematic divergence, identify the root cause:

- Was the plan unclear? Where? Why?
- Was context missing? Where? Why?
- Was validation missing? Where? Why?
- Was manual step repeated? Where? Why?

### Step 5: Generate Process Improvements

Based on patterns across divergences, suggest:

- **CLAUDE.md updates:** Universal patterns or anti-patterns to document
- **Reference doc updates:** Task-specific patterns that need clarification
- **Plan command updates:** Instructions that need clarification or missing steps
- **Execute command updates:** Validation steps that should be added
- **New commands:** Manual processes that should be automated

## Output Format

Save your analysis to: `.claude/system-reviews/YYYY-MM-DD-[feature-name]-review.md`

### Report Structure:

#### Meta Information

- Plan reviewed: [path]
- Execution report: [path]
- Date: [YYYY-MM-DD]

#### Overall Alignment Score: __/10

Scoring guide:

- 10: Perfect adherence, all divergences justified
- 7-9: Minor justified divergences
- 4-6: Mix of justified and problematic divergences
- 1-3: Major problematic divergences

#### Divergence Analysis

For each divergence from the execution report:

```yaml
divergence: [what changed]
planned: [what plan specified]
actual: [what was implemented]
reason: [agent's stated reason from report]
classification: good | bad
justified: yes/no
root_cause: [unclear plan | missing context | missing validation | etc]
```

#### Pattern Compliance

Assess adherence to documented patterns:

- [ ] Followed codebase architecture
- [ ] Used documented patterns (from CLAUDE.md and references)
- [ ] Applied testing patterns correctly
- [ ] Met validation requirements

#### System Improvement Actions

Based on analysis, recommend specific actions:

**Update CLAUDE.md:**

- [ ] Document [pattern X] discovered during implementation
- [ ] Add anti-pattern warning for [Y]
- [ ] Clarify [technology constraint Z]

**Update Reference Docs:**

- [ ] Add pattern to `.claude/reference/[category]/[doc].md`
- [ ] Clarify ambiguous instruction in [doc]
- [ ] Add example for [pattern]

**Update Plan Command:**

- [ ] Add instruction for [missing step]
- [ ] Clarify [ambiguous instruction]
- [ ] Add validation requirement for [X]

**Update Execute Command:**

- [ ] Add [validation step] to execution checklist
- [ ] Add reminder to check [X] before implementing

**Create New Command:**

- [ ] `/[command-name]` for [manual process repeated 3+ times]

**Create New Reference Doc:**

- [ ] `.claude/reference/[category]/[new-doc].md` for [pattern area]

#### Key Learnings

**What worked well:**

- [specific things that went smoothly]

**What needs improvement:**

- [specific process gaps identified]

**For next implementation:**

- [concrete improvements to try]

## Important

- **Be specific:** Don't say "plan was unclear" - say "plan didn't specify which auth pattern to use"
- **Focus on patterns:** One-off issues aren't actionable. Look for repeated problems.
- **Action-oriented:** Every finding should have a concrete asset update suggestion
- **Suggest improvements:** Don't just analyze - actually suggest the text to add to CLAUDE.md or commands
- **Distinguish severity:** Not all improvements are equally important

## When to Run System Review

**Always run after:**
- First implementation of a new feature type
- Multiple problematic divergences in execution report
- Discovering a gap in documentation or commands

**Consider running after:**
- Every 3-5 feature implementations
- Monthly retrospective
- When onboarding to a new tech stack

## Success Criteria

A good system review:
- Identifies root causes, not just symptoms
- Provides specific, actionable improvements
- Results in updates to at least one system asset
- Prevents similar issues in future implementations
