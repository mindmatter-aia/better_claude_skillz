---
description: Prime agent with comprehensive codebase understanding (full mode)
---

# Prime Full: Comprehensive Project Context

**Token Usage:** 85K+ tokens
**For faster loading:** Use `/prime` (quick mode - 35-40K tokens)

## Objective

Build **comprehensive** understanding of the codebase by analyzing structure, documentation, and key files in depth.

## When to Use

- First time working with a codebase
- Need deep architectural understanding
- Planning major refactors or migrations
- Onboarding to complex projects
- Need to understand all documentation and patterns

## Process

### 0. Apply .primeignore Filters

Before listing or reading any files, check if `.primeignore` exists in the project root:

1. If `.primeignore` exists, read it and parse the patterns (skip lines starting with `#` and blank lines)
2. Apply these patterns as exclusion filters to ALL subsequent file listing and reading in this prime session
3. Support `!pattern` syntax to negate an exclusion (force-include a file)
4. **Protected files** -- NEVER exclude regardless of patterns:
   - `CLAUDE.md` (project instructions)
   - Manifest files: `package.json`, `requirements.txt`, `pyproject.toml`, `composer.json`, `go.mod`, `Cargo.toml`
   - `.primeignore` itself
5. In the output report, note: "X files excluded by .primeignore (estimated ~YK tokens saved)"

If `.primeignore` does not exist, proceed normally with no filtering.

**Tip:** To temporarily bypass .primeignore for a deep dive, rename or delete the file, run /prime-full, then restore it.

### 1. Analyze Project Structure

List all tracked files:
```bash
git ls-files
```

Show directory structure:
On Windows/Linux, run:
```bash
# If tree is available
tree -L 3 -I 'node_modules|__pycache__|.git|dist|build|.venv'

# Otherwise use ls/dir
ls -R | grep ":$" | sed -e 's/:$//' -e 's/[^-][^\/]*\//--/g' -e 's/^/   /' -e 's/-/|/'
```

### 2. Read Core Documentation

- Read CLAUDE.md or similar global rules file (FULL)
- Read README files at project root and major directories (FULL)
- Read any architecture documentation (FULL)
- Read PRD if it exists (.claude/PRD.md) (FULL)

### 3. Identify Key Files

Based on the structure, identify and read:
- Main entry points (main.py, index.ts, app.py, server.js, etc.) (FULL)
- Core configuration files (pyproject.toml, package.json, tsconfig.json, composer.json) (FULL)
- Key model/schema definitions (FULL)
- Important service or controller files (FULL)

### 4. Understand Current State

Check recent activity:
```bash
git log -10 --oneline
```

Check current branch and status:
```bash
git status
```

## Output Report

Provide a concise summary covering:

### Project Overview
- Purpose and type of application
- Primary technologies and frameworks
- Current version/state

### Architecture
- Overall structure and organization
- Key architectural patterns identified
- Important directories and their purposes

### Tech Stack
- Languages and versions
- Frameworks and major libraries
- Build tools and package managers
- Testing frameworks

### Core Principles
- Code style and conventions observed
- Documentation standards
- Testing approach

### Current State
- Active branch
- Recent changes or development focus
- Any immediate observations or concerns

**Make this summary easy to scan - use bullet points and clear headers.**

## Token Budget

Full Mode: **85K+ tokens**

## Differences from /prime (quick mode)

| Aspect | /prime (quick) | /prime-full |
|--------|----------------|-------------|
| Token usage | 35-40K | 85K+ |
| README | First 100 lines | Full read |
| Source files | grep signatures only | Full reads |
| Test files | List only | Read examples |
| Docs | CLAUDE.md only | All docs |
| Git history | 5 commits | 10 commits |
| File listing | head -50 | All files |
| Use case | 90% of sessions | Deep dives |
