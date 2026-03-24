---
name: prompt-audit
description: Discover, audit, and rewrite LLM prompts using the R/T/S/C/E/N framework
argument-hint: "[discover|audit|rewrite] [path]"
---

Base directory for this skill: {injected by Claude Code at runtime}

Parse `$ARGUMENTS` for mode and optional path. If `$ARGUMENTS` starts with `discover`, `audit`, or `rewrite`, use that mode. Otherwise default to `discover`. If a path is provided after the mode, use it; otherwise use the current working directory.

Read the framework reference before any audit or rewrite work:
`{skill_base_dir}/references/framework.md`

where `{skill_base_dir}` is the base directory shown above.

---

## Mode 1: Discover

Scan the project for all LLM prompts. Present an inventory with purpose classification.

### Step 1: Scan using tiered detection

**Tier 1 — Standalone template files (high confidence):**
- Glob for `**/*.md` in directories named `prompts/`, `templates/`, `prompt*/`, `system_messages/`, `ai/`, `llm/`, `ai_prompts/`, `llm_prompts/`
- Glob for `**/*.txt` or `**/*.jinja2` in the same directories
- Grep YAML/YML files for keys: `prompt:`, `system_prompt:`, `system_message:`
- Content-based fallback: any `.md` file containing BOTH `# Role` AND (`# Task` OR `# System Prompt`) headers
- Standalone files matching `**/system_prompt*.*`, `**/prompt_template*.*`

**Tier 2 — Inline prompt strings (medium confidence):**
- Python: `system_prompt\s*=`, `system\s*=`, `SYSTEM_PROMPT`, `_PROMPT\s*=`, `register_default(`, multiline `"""` strings containing `You are`
- JS/TS: `const.*[Pp]rompt`, `system:`, `role:\s*["']system["']`, template literals containing `You are`
- Go: `prompt\s*:=`, `systemPrompt`, `const.*Prompt`
- Ruby: `SYSTEM_PROMPT`, `prompt =`, heredocs containing `You are`
- Rust: `SYSTEM_PROMPT`, `let.*prompt`, `system_prompt`
- Java/Kotlin: `SYSTEM_PROMPT`, `systemPrompt`, multiline `"""` containing `You are`

**Tier 3 — Config-embedded prompts (lower confidence):**
- JSON files with `prompt`, `system_prompt`, `system_message` keys
- YAML files with same keys
- `.env` files with `*_PROMPT=` variables (flag existence only, do not read values)

**Exclusions:** Skip `node_modules/`, `.venv/`, `dist/`, `build/`, `vendor/`, `.git/`, `__pycache__/`, `*.min.js`

### Step 2: Classify each prompt

Read the content of each discovered prompt and classify using framework.md signals:

| Signal | Class |
|--------|-------|
| < 100 words, no variables, single output format | Directive |
| Scoring rubric, JSON schema, multi-step analysis, multi-field output | Structured |
| Audience/tone spec, creative output, multiple variants/segments | Generative |

When signals are ambiguous, default to Structured.

### Step 3: Deduplicate

If the same prompt appears in multiple tiers (e.g., a standalone `.md` file AND a `register_default()` call), show it once with the highest-confidence tier. Note the duplicate in the Format column: "Standalone MD (also registered inline at path:line)".

### Step 4: Present inventory

```
# Prompt Inventory — {project name}

Found {N} prompts across {M} files

| # | Name/Purpose | File | Format | Tier | Class |
|---|-------------|------|--------|------|-------|
| 1 | JSON format enforcer | utils/format.py:12 | Inline Python | 2 | Directive |
| 2 | Lead scoring prompt | prompts/scoring.md | Standalone MD | 1 | Structured |
| 3 | Email outreach template | prompts/outreach.md | Standalone MD | 1 | Generative |

Summary: {D} Directive, {S} Structured, {G} Generative
- Directive prompts: lightweight review only (Role + Task + Notes)
- Structured/Generative prompts: full R/T/S/C/E/N audit recommended
```

**If zero prompts found:**
> No LLM prompts found in {path}. This could mean:
> - Prompts are in an unusual location — try `/prompt-audit discover path/to/specific/dir`
> - Prompts are embedded in code patterns not covered by current heuristics
> - This project doesn't use LLM prompts
>
> Search patterns used: {list the Tier 1/2/3 patterns tried}

**If prompts found**, ask: "Proceed to audit these prompts? (yes / select specific numbers / adjust scope)"

**Limit output to 50 results.** If more are found, show the first 50 and note: "{X} additional prompts found. Show next page? (yes / filter by tier / filter by class)"

---

## Mode 2: Audit

Grade each discovered prompt against the R/T/S/C/E/N framework, adapted to its class.

If discover has not been run in this session, run it first automatically.

Read `{skill_base_dir}/references/framework.md` for the grading criteria.

### Scoring Rubric

**Structured and Generative prompts** — qualitative tiers:

| Tier | Meaning | Criteria |
|------|---------|----------|
| **Strong** | Follows recommended structure well | All required sections present and substantive; best practices followed |
| **Needs Work** | Foundation exists but gaps are clear | 1-2 required sections missing or weak; some best practices missing |
| **Major Gaps** | Significant restructuring needed | 3+ sections missing; no chain-of-thought; constraints scattered or absent |

**Directive prompts** — pass/fail: clear role, clear instruction, output format specified? Pass. Otherwise fail with specific recommendation.

### Per-prompt audit (stepped, one at a time)

For each prompt, present and wait for user acknowledgment before proceeding to the next:

**Header:**
```
## Prompt {N} of {total}: {name} ({Class})
File: {path}:{line}
Classification: {Class} — {reasoning}
Recommended structure: {sections list}
```

**For Directive prompts:**

| Section | Status | Note |
|---------|--------|------|
| Role | Present / Weak / Missing | Has identity + purpose? |
| Task | Present / Weak / Missing | Clear instruction? |
| Notes | Present / Weak / Missing | Output format specified? |
| Full R/T/S/C/E/N | Not recommended | This prompt is appropriately simple |

**Verdict:** Pass / Fail + specific lightweight recommendations.

**For Structured prompts:**

| Section | Grade | Criteria |
|---------|-------|----------|
| Role | Present / Weak / Missing | Identity + stakes + tone? |
| Task | Present / Weak / Missing | Numbered analytical steps + variables? |
| Specifics | Present / Weak / Missing | Quantified constraints / rubric? |
| Context | Present / Weak / Missing | Relevant background? |
| Examples | Present / Weak / Missing | 2+ labeled examples? |
| Notes | Present / Weak / Missing | Output format + constraints at end? |

Additional checks:
- Chain-of-thought steps (yes/no)
- Variable system (detected/none)
- Constraint placement (beginning/middle/end — end is correct)
- Few-shot examples (count)
- Anti-pattern list (yes/no)
- Output format specification (yes/no)
- Scoring guidance: conservative defaults stated? (scoring prompts only)

**Verdict:** Strong / Needs Work / Major Gaps + specific recommendations.

**For Generative prompts:**

Same grading table as Structured, plus:
- Tone specification (yes/no)
- Audience definition (yes/no)
- Banned phrases / anti-patterns (yes/no)
- Example variety (single variant / multiple segments or styles)

**Verdict:** Strong / Needs Work / Major Gaps + specific recommendations.

### Aggregate summary (after all prompts)

```
# Audit Summary

| Class | Count | Strong | Needs Work | Major Gaps |
|-------|-------|--------|------------|------------|
| Directive | {n} | {n} pass | {n} fail | — |
| Structured | {n} | {n} | {n} | {n} |
| Generative | {n} | {n} | {n} | {n} |

Top recommendations:
1. {Most common gap across all prompts}
2. {Second most common}
3. {Third most common}
```

---

## Mode 3: Rewrite

Propose R/T/S/C/E/N rewrites with user review for each prompt.

If discover and audit have not been run, run them first automatically.

### Step 1: Choose rewrite mode

Present the choice with context about the prompt inventory:

> **Rewrite mode:** Choose how to handle rewrites:
> 1. **Extract** — Write restructured prompts to a `prompts/` directory as standalone `.md` files. Works for all prompt types. Recommended for inline prompts.
> 2. **In-place** — Rewrite standalone `.md` files directly. For inline code strings, adds annotated recommendations above the prompt (rewriting inline strings risks breaking code structure).
> 3. **Hybrid** — Extract for inline prompts, in-place for standalone files. *(Recommended when inventory contains both types)*

If the inventory contains inline prompts, note:
> **Note:** {N} prompts are inline code strings. Extract mode is recommended for these — it creates standalone files you can load at runtime. In-place mode can only annotate inline strings, not restructure them.

For extract mode, ask for the target directory on first prompt (default: `{project}/prompts/`). If the project already has a prompt directory, suggest using it.

### Step 2: Step through prompts (one at a time)

**For Directive prompts:**
1. Show current prompt
2. State: "Classified as Directive. Full R/T/S/C/E/N restructuring is not recommended."
3. If well-structured: "No changes needed — this prompt is appropriately simple."
4. If improvements possible: propose lightweight fixes only (clearer role, explicit format in Notes)
5. Wait for: **accept** / **modify** / **skip**

**For Structured and Generative prompts:**
1. Show the current prompt (summary if > 50 lines)
2. Present the proposed R/T/S/C/E/N rewrite with all recommended sections
3. Highlight what changed and why (referencing framework.md):
   - Sections added (with rationale)
   - Content moved (e.g., constraints moved from middle to Notes)
   - Chain-of-thought steps introduced
   - Examples added (with labeled headers)
4. Wait for: **accept** / **modify** / **skip**
   - **accept** — apply the rewrite (extract to file or edit in place)
   - **modify** — user provides feedback, Claude revises and re-presents (max 3 rounds per prompt; after 3, present current version as final)
   - **skip** — leave unchanged, move to next

### Rewrite behaviors

**Extract mode:**
- Write to `{target_dir}/{name}.md`
- If the project already has a prompt loader or directory, use that path
- For inline prompts being extracted: warn the user that they'll need to update the calling code to load from the file. Do NOT auto-modify the calling code.

**In-place mode:**
- Standalone `.md` files: rewrite the file directly
- Inline code strings: add a `# PROMPT-AUDIT: Restructure to R/T/S/C/E/N` comment block above the prompt with specific recommendations. Do NOT modify the prompt string itself.
- Directive prompts with no changes needed: skip silently

**Hybrid mode:**
- Apply extract behavior to inline prompts
- Apply in-place behavior to standalone files

### Critical rules for rewrites

- **Preserve the project's variable syntax.** If the project uses `{var}`, `${var}`, Jinja `{{ var }}`, or any other convention, match it. Do not impose `{{var}}`.
- **Never modify code structure around inline prompts.** Only touch the prompt string content (in extract mode) or add comments (in in-place mode).
- **When extracting inline prompts to files**, warn the user that they'll need to update the calling code. Do not auto-modify code.
- **Preserve domain-specific content.** The rewrite restructures sections and adds framework elements; it should not change the prompt's business logic, scoring criteria, or domain rules.

---

## Quick Reference

| Command | What it does |
|---------|-------------|
| `/prompt-audit` | Discover mode (default) on current directory |
| `/prompt-audit discover` | Scan and classify all prompts |
| `/prompt-audit discover path/to/dir` | Scan a specific directory |
| `/prompt-audit audit` | Grade prompts against framework (runs discover first if needed) |
| `/prompt-audit rewrite` | Propose rewrites with user review (runs discover + audit first if needed) |
