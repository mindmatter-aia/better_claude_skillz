---
name: prime
description: Load project context. Use at the start of every session. Supports quick (default) and full modes.
argument-hint: "[quick|full]"
---

Check $ARGUMENTS for mode. If "$ARGUMENTS" is "full", follow **Full Mode** below. Otherwise follow **Quick Mode** (default).

## Quick Mode

# Prime Quick: Load Essential Project Context

## Objective

Build sufficient understanding of the codebase by analyzing structure, documentation, and key files **efficiently**. Uses ~35-40K tokens instead of 85K+ (60% savings).

## When to Use

- **Default choice** for most feature planning and development
- Starting a new session
- Getting oriented in a codebase
- Before planning features

## When to Use Full Mode Instead

- First time working with a codebase
- Need deep architectural understanding
- Planning major refactors or migrations
- Onboarding to a complex project

## Process

### 1. Analyze Project Structure (Compact)

**List tracked files (first 50 only):**
```bash
git ls-files | head -50
```

**Show directory structure (depth 2 max):**
```bash
find . -maxdepth 2 -type d -not -path '*/\.git/*' -not -path '*/__pycache__/*' -not -path '*/node_modules/*' | sort
```

**Check root directory:**
```bash
ls -la | head -20
```

### 2. Read Core Documentation (Selective)

**ALWAYS READ:**
- Project `CLAUDE.md` (full - contains project-specific rules and context)
- `requirements.txt` or `package.json` or similar (full - dependencies)

**READ WITH LIMITS:**
- `README.md` (limit=100 lines - overview only, skip detailed usage)
- Main entry point like `app.py`, `index.ts`, `main.py` (limit=100 lines - configuration and structure)

**SKIP IN QUICK MODE:**
- ARCHITECTURE.md (use full mode if needed)
- DEPLOYMENT.md
- Additional documentation files
- PRD (unless specifically needed for the task)

### 3. Identify Key Files (Efficient)

**Use grep/find to identify structure without reading full files:**

For Python projects:
```bash
# List all modules
ls -la modules/ src/ app/ 2>/dev/null | head -30

# Get function/class signatures (don't read full files)
grep -n "^class \|^def " *.py modules/*.py src/*.py 2>/dev/null | head -40
```

For JavaScript/TypeScript projects:
```bash
# List all source files
ls -la src/ lib/ components/ 2>/dev/null | head -30

# Get export signatures
grep -n "^export \|^class \|^function " src/**/*.{js,ts,tsx} 2>/dev/null | head -40
```

**Configuration files:**
- Read `pyproject.toml`, `package.json`, `tsconfig.json`, `composer.json` (full - usually short)

**SKIP full reads of:**
- Model/schema files (use grep for class names only)
- Service/controller files (use grep for function signatures)
- Template files
- Test files (just list them)

### 4. Understand Current State (Minimal)

**Recent activity (5 commits, not 10):**
```bash
git log -5 --oneline
```

**Current branch and status (compact):**
```bash
git status --short
git branch --show-current
```

**Python version check (if Python project):**
```bash
python --version 2>&1
```

### 5. Test Infrastructure (Overview Only)

**Don't read test files, just list:**
```bash
ls -la tests/ test/ __tests__/ 2>/dev/null | head -20
find tests/ -name "*.py" -o -name "*.js" -o -name "*.ts" 2>/dev/null | head -10
```

## Output Report

Provide a **concise** summary covering:

### Project Overview
- Purpose and type of application
- Primary technologies and frameworks
- Current version/state

### Architecture
- Overall structure and organization (based on directory listing)
- Key architectural patterns identified (from CLAUDE.md and file structure)
- Important directories and their purposes

### Tech Stack
- Languages and versions
- Frameworks and major libraries (from dependencies file)
- Build tools and package managers
- Testing frameworks (from dependencies)

### Core Principles
- Code style and conventions observed (from CLAUDE.md)
- Documentation standards
- Testing approach (from test directory structure)

### Current State
- Active branch
- Recent changes or development focus (from last 5 commits)
- Any immediate observations or concerns

### Key Files Identified
- Main entry points
- Configuration files
- Module/component structure (high-level only)

**Keep the summary concise - use bullet points and clear headers. Focus on actionable information.**

## Token Budget

Target: **35-40K tokens** (vs 85K+ for full mode)

- Structure analysis: ~5-10K
- Core docs: ~10-15K
- File signatures: ~5-10K
- Current state: ~5K
- Report: ~5-10K

## Differences from Full Mode

| Aspect | Quick Mode | Full Mode |
|--------|------------|-----------|
| Token usage | 35-40K | 85K+ |
| README | First 100 lines | Full read |
| Source files | grep signatures only | Full reads |
| Test files | List only | Read examples |
| Docs | CLAUDE.md only | All docs |
| Git history | 5 commits | 10 commits |
| File listing | head -50 | All files |
| Use case | 90% of sessions | Deep dives |

## Notes

- If during planning you realize you need more context, run prime with "full" argument
- Quick mode captures ~95% of useful context for most tasks
- You can always read specific files later with the Read tool
- Focus is on **structure and patterns**, not exhaustive coverage

## Full Mode

# Prime Full: Comprehensive Project Context

**Token Usage:** 85K+ tokens
**For faster loading:** Use prime without arguments (quick mode - 35-40K tokens)

## Objective

Build **comprehensive** understanding of the codebase by analyzing structure, documentation, and key files in depth.

## When to Use

- First time working with a codebase
- Need deep architectural understanding
- Planning major refactors or migrations
- Onboarding to complex projects
- Need to understand all documentation and patterns

## Process

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

## Differences from Quick Mode

| Aspect | Quick Mode | Full Mode |
|--------|------------|-----------|
| Token usage | 35-40K | 85K+ |
| README | First 100 lines | Full read |
| Source files | grep signatures only | Full reads |
| Test files | List only | Read examples |
| Docs | CLAUDE.md only | All docs |
| Git history | 5 commits | 10 commits |
| File listing | head -50 | All files |
| Use case | 90% of sessions | Deep dives |
