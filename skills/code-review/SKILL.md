---
name: code-review
description: Technical code review for quality and bugs that runs pre-commit
---

# Code Review: Pre-Commit Technical Analysis

Perform technical code review on recently changed files.

## Core Principles

Review Philosophy:

- Simplicity is the ultimate sophistication - every line should justify its existence
- Code is read far more often than it's written - optimize for readability
- The best code is often the code you don't write
- Elegance emerges from clarity of intent and economy of expression

## What to Review

Start by gathering codebase context to understand the codebase standards and patterns.

Start by examining:

- CLAUDE.md
- README.md
- Key files in core modules
- Documented standards in docs/ or .claude/reference/

After you have a good understanding, run these commands:

```bash
git status
git diff HEAD
git diff --stat HEAD
```

Then check the list of new files:

```bash
git ls-files --others --exclude-standard
```

Read each new file in its entirety. Read each changed file in its entirety (not just the diff) to understand full context.

For each changed file or new file, analyze for:

### 1. Logic Errors

- Off-by-one errors
- Incorrect conditionals
- Missing error handling
- Race conditions
- Null/undefined handling

### 2. Security Issues

- SQL injection vulnerabilities
- XSS vulnerabilities
- Insecure data handling
- Exposed secrets or API keys
- Authentication/authorization bypasses
- Command injection

### 3. Performance Problems

- N+1 queries
- Inefficient algorithms
- Memory leaks
- Unnecessary computations
- Missing indexes (database)

### 4. Code Quality

- Violations of DRY principle
- Overly complex functions
- Poor naming
- Missing type hints/annotations
- Inconsistent formatting
- Magic numbers/strings

### 5. Adherence to Codebase Standards

- Adherence to standards documented in CLAUDE.md
- Adherence to patterns in .claude/reference/ docs
- Linting and formatting standards
- Logging standards
- Testing standards
- Architectural patterns

## Verify Issues Are Real

- Run specific tests for issues found
- Confirm type errors are legitimate
- Validate security concerns with context

## Output Format

Save a new file to `.claude/code-reviews/YYYY-MM-DD-[feature-name].md`

**Stats:**

- Files Modified: X
- Files Added: X
- Files Deleted: X
- New lines: X
- Deleted lines: X

**For each issue found:**

```
severity: critical|high|medium|low
file: path/to/file.py
line: 42
issue: [one-line description]
detail: [explanation of why this is a problem]
suggestion: [how to fix it]
```

If no issues found: "Code review passed. No technical issues detected."

## Severity Guidelines

**Critical:**
- Security vulnerabilities
- Data loss risks
- System crashes

**High:**
- Logic errors affecting functionality
- Performance issues causing timeouts
- Missing critical error handling

**Medium:**
- Code quality issues
- Minor performance concerns
- Inconsistent patterns

**Low:**
- Style inconsistencies
- Naming improvements
- Documentation suggestions

## Important

- Be specific (line numbers, not vague complaints)
- Focus on real bugs, not style preferences
- Suggest fixes, don't just complain
- Flag security issues as CRITICAL
- Distinguish between must-fix and nice-to-have
