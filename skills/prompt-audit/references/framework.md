# R/T/S/C/E/N Prompt Engineering Framework

A structured approach to writing LLM prompts that produce consistent, high-quality outputs. Six sections, applied selectively based on prompt purpose.

## Purpose Classification

Before applying the framework, classify the prompt:

| Class | When to Use | Recommended Sections |
|-------|-------------|---------------------|
| **Directive** | Simple behavioral instruction. Single purpose, <100 words, no variables, no output variability. | Role + Task + Notes |
| **Structured** | Multi-step analysis with specific output schema. Scoring rubrics, classification, data extraction, JSON output. | Full R/T/S/C/E/N |
| **Generative** | Creative or persuasive output with audience/tone requirements. Multiple variants, segments, or styles. | Full R/T/S/C/E/N |

**Classification signals:**

| Signal | Points toward |
|--------|--------------|
| < 100 words, no variables, single output format | Directive |
| Scoring ranges, rubric dimensions, JSON schema | Structured |
| Numbered analytical steps, multi-field output | Structured |
| Audience/tone specification, creative output | Generative |
| Multiple variants (segments, personas, channels) | Generative |
| Variables for dynamic content | Structured or Generative |

**When in doubt, use the full framework.** Borderline prompts benefit more from added structure than they suffer from it.

**When the full framework is NOT recommended:** format enforcers, simple role definitions, single-step instructions with no output variability, system prompts under 100 words with a clear narrow purpose. These benefit from clarity and brevity; forcing 6 sections dilutes the instruction.

---

## Section Definitions

### `# Role` (Required for all classes)

Identity, expertise, stakes, and tone. 2-4 sentences.

- State the stakes: "Your scoring directly determines which prospects receive outreach" -- not just "You are a scoring analyst"
- Define tone and approach: peer-to-peer, data-driven, conservative, etc.
- Make the LLM understand *why accuracy matters* for this specific task

### `# Task` (Required for all classes)

Objective statement, numbered chain-of-thought steps, and variable definitions.

- **Structured prompts:** use analytical steps (interpret, evaluate, synthesize, calculate)
- **Generative prompts:** use procedural steps (write hook, build body, close with CTA)
- Place variables with their data after the steps using `{{double_brace}}` syntax
- Each step should be atomic and independently verifiable

### `# Specifics` (Structured and Generative only)

Hard constraints, quantified rules, and domain-specific guidance. Use `##` sub-sections for complex prompts.

- Quantify everything: word counts, score ranges, character limits
- For scoring prompts: define rubric dimensions with numeric ranges and example signals per tier
- For content prompts: break into output components (## Hook, ## Body, ## CTA) with rules per component
- For analysis prompts: define data sources and expected section structure
- Include domain-specific language maps (what phrases resonate with which audience)

### `# Context` (Recommended for Structured and Generative)

Background the LLM needs that isn't in the data variables. Positioning, strategy, market dynamics.

- Positioning and background go here; data goes in Task variables
- Explain *why* this task exists in the broader system
- Pre-empt common misunderstandings through context, not disclaimers

### `# Examples` (Recommended for Structured and Generative)

Few-shot examples showing desired output. 2-3 per prompt, labeled by category.

- Use `## Example N (Label)` headers (e.g., `## Example 1 (Agency)`)
- Show the exact output format with variable placeholders rendered in context
- Cover different variants (segments, edge cases, complexity levels)
- For JSON output: show the complete schema with sample values

### `# Notes` (Required for all classes)

Output constraints, banned patterns, format validation. **Placed LAST** to exploit the "Lost in the Middle" effect (LLMs attend most to beginning and end of prompts).

- Output format specification (JSON schema, plain text, markdown structure)
- "Do NOT" rules: banned phrases, forbidden structures, common failure modes
- Validation rules: what makes output invalid
- Conservative scoring guidance (for scoring/classification prompts)
- Hard limits: character counts, field constraints, format requirements

---

## Structural Variations by Archetype

| Archetype | Examples | Specifics Pattern | Output Format |
|-----------|---------|-------------------|---------------|
| Outreach/copy | Full examples per segment | Tone, length, CTA rules | Plain text or Subject + Body |
| Scoring/classification | JSON schema in Notes | Rubric with numeric ranges | Strict JSON |
| Content creation | 1-2 examples | Component sub-sections (Hook, Body, CTA) | Plain text |
| Analysis/reporting | Optional | Data sources, section structure | Markdown with headers |
| Summarization | Optional | Length, tone, audience constraints | Varies |

---

## Universal Best Practices

1. **Constraint placement:** Hard bans and output format always go in Notes (end position = high attention)
2. **Variables:** Use `{{key}}` syntax. All substitution is literal; no computed values or conditionals
3. **Few-shot examples:** Label by category, show exact output format, cover multiple variants
4. **Sub-sections:** Use `##` within Specifics for complex rubrics, multi-part outputs, or format specs
5. **Anti-patterns:** Explicitly list banned phrases, forbidden structures, and common failure modes
6. **Conservative defaults:** For scoring/classification, false positives waste more resources than false negatives
7. **Truthfulness:** Include "if data is thin, acknowledge it rather than fabricating" guidance
