# `/prompt-audit` -- Discover, Audit, and Rewrite LLM Prompts

A Claude Code skill that finds all LLM prompts in your codebase, classifies them by purpose, grades them against a proven prompt engineering framework, and offers to rewrite them -- giving every project consistent, high-quality prompts.

## Overview

Most codebases accumulate LLM prompts organically -- a system message here, an inline string there, a template file somewhere else. Over time they drift in quality: some have detailed instructions, others are one-liners that could be doing more. There's no standard structure and no way to audit them.

`/prompt-audit` solves this with a three-step workflow:

1. **Discover** -- scans your project for prompts across standalone files, inline code strings, and config files in any major language
2. **Audit** -- grades each prompt against the R/T/S/C/E/N framework (Role, Task, Specifics, Context, Examples, Notes), adapted to the prompt's purpose
3. **Rewrite** -- proposes restructured prompts for your review, one at a time, with accept/modify/skip controls

The skill is smart enough to know that not every prompt needs the full framework. A one-line JSON format enforcer should stay simple. A multi-step scoring rubric with structured output needs all six sections.

## Key Features

- **Purpose classification** -- categorizes each prompt as Directive (simple), Structured (analytical), or Generative (creative) and adapts grading accordingly
- **Multi-language discovery** -- finds prompts in Python, JavaScript/TypeScript, Go, Ruby, Rust, and Java/Kotlin
- **Three-tier detection** -- standalone template files (high confidence), inline code strings (medium), config-embedded (lower)
- **Qualitative grading** -- Strong / Needs Work / Major Gaps (not false-precision numeric scores)
- **Interactive rewrite** -- extract to standalone files, rewrite in place, or hybrid mode with per-prompt user review
- **Deduplication** -- detects when the same prompt appears in multiple locations
- **Zero-results handling** -- actionable suggestions when no prompts are found

## The R/T/S/C/E/N Framework

A structured approach to writing LLM prompts, based on prompt engineering best practices:

| Section | Purpose | Required for |
|---------|---------|-------------|
| **Role** | Identity, expertise, stakes, tone | All prompts |
| **Task** | Objective + numbered chain-of-thought steps + variables | All prompts |
| **Specifics** | Quantified constraints, rubrics, domain-specific guidance | Structured, Generative |
| **Context** | Background the LLM needs beyond the data variables | Structured, Generative |
| **Examples** | Few-shot examples labeled by category | Structured, Generative |
| **Notes** | Output constraints, banned patterns, format validation (placed last for maximum attention) | All prompts |

The full framework specification is in [`references/framework.md`](references/framework.md).

### Purpose Classification

Not every prompt needs six sections. The skill classifies prompts and recommends the right level of structure:

| Class | Description | Recommended Sections |
|-------|-------------|---------------------|
| **Directive** | Simple instruction, <100 words, no variables | Role + Task + Notes |
| **Structured** | Multi-step analysis, scoring rubrics, JSON output | Full R/T/S/C/E/N |
| **Generative** | Creative output with audience/tone requirements | Full R/T/S/C/E/N |

## Installation

### Quick Install

```bash
cp -r prompt-audit ~/.claude/skills/
```

See [INSTALL.md](INSTALL.md) for detailed instructions.

## Usage

### Discover (default)

Scan the current project for all LLM prompts:

```
/prompt-audit
```

Scan a specific directory:

```
/prompt-audit discover path/to/project
```

Output is a numbered inventory table with format, tier, and classification for each prompt.

### Audit

Grade all discovered prompts against the framework:

```
/prompt-audit audit
```

Steps through prompts one at a time. Each gets a scorecard with section grades, additional checks, and specific recommendations. Ends with an aggregate summary.

### Rewrite

Propose R/T/S/C/E/N rewrites with interactive review:

```
/prompt-audit rewrite
```

Choose from three modes:
- **Extract** -- write restructured prompts to standalone `.md` files
- **In-place** -- rewrite standalone files directly; annotate inline strings
- **Hybrid** -- extract for inline prompts, in-place for standalone files

For each prompt: review the proposed rewrite, then **accept**, **modify** (up to 3 rounds), or **skip**.

## What It Finds

### Tier 1 -- Standalone Template Files (High Confidence)
- `.md`, `.txt`, `.jinja2` files in `prompts/`, `templates/`, `llm/`, `ai/` directories
- Any `.md` file containing `# Role` and `# Task` headers (framework signature)
- Files matching `system_prompt*.*`, `prompt_template*.*`

### Tier 2 -- Inline Code Strings (Medium Confidence)
- Python: `system_prompt=`, `SYSTEM_PROMPT`, `_PROMPT =`, `register_default()`
- JS/TS: `const.*Prompt`, `system:`, `role: "system"`
- Go: `prompt :=`, `systemPrompt`, `const.*Prompt`
- Ruby, Rust, Java/Kotlin: similar patterns

### Tier 3 -- Config-Embedded (Lower Confidence)
- JSON/YAML with `prompt`, `system_prompt`, `system_message` keys
- `.env` files with `*_PROMPT=` variables (flagged only, values not read)

## Example Output

### Discovery

```
# Prompt Inventory -- my-project

Found 12 prompts across 8 files

| # | Name/Purpose          | File                    | Format        | Tier | Class      |
|---|-----------------------|-------------------------|---------------|------|------------|
| 1 | JSON formatter        | utils/format.py:12      | Inline Python | 2    | Directive  |
| 2 | Lead scoring          | prompts/scoring.md      | Standalone MD | 1    | Structured |
| 3 | Email outreach        | prompts/outreach.md     | Standalone MD | 1    | Generative |
| 4 | Intent classifier     | bot/intent.py:84        | Inline Python | 2    | Structured |

Summary: 4 Directive, 5 Structured, 3 Generative
```

### Audit

```
## Prompt 2 of 12: Lead scoring (Structured)
File: prompts/scoring.md
Classification: Structured -- multi-dimension scoring with JSON output

| Section   | Grade   | Note                              |
|-----------|---------|-----------------------------------|
| Role      | Present | Identity + stakes                 |
| Task      | Present | 5 numbered analytical steps       |
| Specifics | Present | Scoring rubric with ranges        |
| Context   | Present | Business positioning              |
| Examples  | Missing | No few-shot examples              |
| Notes     | Present | JSON schema + constraints at end  |

Verdict: Needs Work
Recommendation: Add 2-3 examples showing scored output for different prospect types
```

## FAQ

### Q: Will this modify my code without asking?
No. Discovery and audit are read-only. Rewrite mode always asks for confirmation per prompt. For inline code strings, in-place mode only adds comments -- it never modifies the prompt string itself.

### Q: What if it classifies a prompt wrong?
Classification is a recommendation, not a gate. You can select any prompt for full audit regardless of its classification. The reasoning is always shown so you can challenge it.

### Q: Does it work on projects without any prompts?
Yes. Discovery handles zero results gracefully with actionable suggestions.

### Q: How does it handle prompts that appear in multiple places?
Deduplication shows each prompt once at its highest-confidence tier, with a note about other locations (e.g., "Standalone MD (also registered inline at engine/scoring.py:25)").

## Credits

Created by **Nick Martin** of [PatriotAgentic LLC](https://patriotagentic.com).

Prompt structure inspiration from **Liam Ottley** of [Morningside AI](https://www.morningside.ai/).

## License

See repository [LICENSE](../../LICENSE).
