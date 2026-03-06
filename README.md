# Shared Skillz -- Claude Code Toolkit

A curated collection of commands, agents, and skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code), Anthropic's official CLI for Claude.

**Total items:** 64 (8 commands + 13 agents + 43 skills)

---

## Table of Contents

- [Commands](#commands) (8)
- [Agents](#agents) (13)
- [Skills](#skills) (43)
- [Installation](#installation)
- [Contributing](#contributing)
- [License](#license)

---

## Commands

Slash commands that invoke specific workflows in Claude Code.

| Command | Description |
|---------|-------------|
| [`/challenge`](commands/challenge/) | Quality gate that pressure-tests any work before shipping |
| [`/go-build`](commands/go-build/) | Fix Go build errors, vet warnings, and linter issues incrementally |
| [`/go-review`](commands/go-review/) | Comprehensive Go code review for idiomatic patterns and security |
| [`/go-test`](commands/go-test/) | Enforce TDD workflow for Go with table-driven tests |
| [`/plan`](commands/plan/) | Create step-by-step implementation plan before writing code |
| [`/prime-full`](commands/prime-full/) | Load comprehensive project context (85K+ tokens) |
| [`/prime-quick`](commands/prime-quick/) | Load essential project context quickly (35-40K tokens) |
| [`/stats`](commands/stats/) | Display session statistics, context usage, and system info |

---

## Agents

Specialized subagents that handle complex tasks autonomously.

### Code Quality

| Agent | Description | Tools | Model |
|-------|-------------|-------|-------|
| [`code-reviewer`](agents/code-reviewer/) | Expert code review for quality, security, and maintainability | Read, Grep, Glob, Bash | opus |
| [`go-reviewer`](agents/go-reviewer/) | Go-specific code review for idiomatic patterns and concurrency | Read, Grep, Glob, Bash | opus |
| [`python-reviewer`](agents/python-reviewer/) | Python code review for PEP 8, type hints, security | Read, Grep, Glob, Bash | opus |
| [`refactor-cleaner`](agents/refactor-cleaner/) | Dead code cleanup and consolidation | Read, Write, Edit, Bash, Grep, Glob | opus |

### Architecture and Planning

| Agent | Description | Tools | Model |
|-------|-------------|-------|-------|
| [`architect`](agents/architect/) | System design, scalability, and architectural decisions | Read, Grep, Glob | opus |
| [`planner`](agents/planner/) | Implementation planning for complex features | Read, Grep, Glob | opus |

### Build and Fix

| Agent | Description | Tools | Model |
|-------|-------------|-------|-------|
| [`build-error-resolver`](agents/build-error-resolver/) | Fix TypeScript and build errors with minimal diffs | Read, Write, Edit, Bash, Grep, Glob | opus |
| [`go-build-resolver`](agents/go-build-resolver/) | Fix Go build errors, vet issues, and linter warnings | Read, Write, Edit, Bash, Grep, Glob | opus |

### Testing

| Agent | Description | Tools | Model |
|-------|-------------|-------|-------|
| [`tdd-guide`](agents/tdd-guide/) | Test-driven development enforcing write-tests-first methodology | Read, Write, Edit, Bash, Grep | opus |
| [`e2e-runner`](agents/e2e-runner/) | End-to-end testing with Playwright and artifact management | Read, Write, Edit, Bash, Grep, Glob | opus |

### Security and Database

| Agent | Description | Tools | Model |
|-------|-------------|-------|-------|
| [`security-reviewer`](agents/security-reviewer/) | Vulnerability detection and OWASP Top 10 analysis | Read, Write, Edit, Bash, Grep, Glob | opus |
| [`database-reviewer`](agents/database-reviewer/) | PostgreSQL query optimization, schema design, and RLS | Read, Write, Edit, Bash, Grep, Glob | opus |

### Documentation

| Agent | Description | Tools | Model |
|-------|-------------|-------|-------|
| [`doc-updater`](agents/doc-updater/) | Codemap generation and documentation maintenance | Read, Write, Edit, Bash, Grep, Glob | opus |

---

## Skills

Reusable workflow patterns activated as slash commands.

### Planning and Execution

| Skill | Description |
|-------|-------------|
| [`/plan-ecc`](skills/plan-ecc/) | Restate requirements, assess risks, create implementation plan |
| [`/plan-feature`](skills/plan-feature/) | Comprehensive feature planning with deep codebase analysis |
| [`/execute`](skills/execute/) | Execute an implementation plan |
| [`/execution-report`](skills/execution-report/) | Document what was implemented vs planned |
| [`/orchestrate`](skills/orchestrate/) | Sequential agent workflow for complex tasks |
| [`/system-review`](skills/system-review/) | Analyze implementation against plan for process improvements |

### Code Quality and Review

| Skill | Description |
|-------|-------------|
| [`/code-review`](skills/code-review/) | Technical code review that runs pre-commit |
| [`/code-review-ecc`](skills/code-review-ecc/) | Comprehensive security and quality review of uncommitted changes |
| [`/coding-standards`](skills/coding-standards/) | Universal coding standards for TypeScript, JavaScript, React, Node.js |
| [`/refactor-clean`](skills/refactor-clean/) | Safely identify and remove dead code with test verification |

### Testing

| Skill | Description |
|-------|-------------|
| [`/tdd`](skills/tdd/) | Enforce test-driven development workflow |
| [`/tdd-workflow`](skills/tdd-workflow/) | TDD with 80%+ coverage including unit, integration, and E2E |
| [`/e2e`](skills/e2e/) | Generate and run end-to-end tests with Playwright |
| [`/test-coverage`](skills/test-coverage/) | Analyze coverage and generate missing tests to reach 80%+ |
| [`/golang-testing`](skills/golang-testing/) | Go testing patterns: table-driven tests, benchmarks, fuzzing |
| [`/python-testing`](skills/python-testing/) | Python testing with pytest, TDD, fixtures, mocking |

### Language Patterns

| Skill | Description |
|-------|-------------|
| [`/frontend-patterns`](skills/frontend-patterns/) | React, Next.js, state management, and performance patterns |
| [`/backend-patterns`](skills/backend-patterns/) | API design, database optimization, and server-side patterns |
| [`/golang-patterns`](skills/golang-patterns/) | Idiomatic Go patterns, concurrency, and best practices |
| [`/python-patterns`](skills/python-patterns/) | Pythonic idioms, PEP 8, type hints, and best practices |
| [`/postgres-patterns`](skills/postgres-patterns/) | PostgreSQL query optimization, schema design, and indexing |

### Security

| Skill | Description |
|-------|-------------|
| [`/security-review`](skills/security-review/) | Comprehensive security checklist and vulnerability patterns |

### Context and Documentation

| Skill | Description |
|-------|-------------|
| [`/prime`](skills/prime/) | Load project context at session start |
| [`/optimize-context`](optimize-context/) | Smart file exclusion for efficient context loading |
| [`/update-codemaps`](skills/update-codemaps/) | Analyze codebase and update architecture documentation |
| [`/update-docs`](skills/update-docs/) | Sync documentation from source-of-truth files |
| [`/checkpoint`](skills/checkpoint/) | Save current session state for safe rollback |
| [`/strategic-compact`](skills/strategic-compact/) | Context compaction at logical intervals |
| [`/iterative-retrieval`](skills/iterative-retrieval/) | Progressive context retrieval for multi-agent workflows |
| [`/create-prd`](skills/create-prd/) | Create Product Requirements Document from conversation |

### Continuous Learning

| Skill | Description |
|-------|-------------|
| [`/continuous-learning-v2`](skills/continuous-learning-v2/) | Instinct-based learning system with confidence scoring |
| [`/learn`](skills/learn/) | Extract reusable patterns from current session |
| [`/evolve`](skills/evolve/) | Cluster related instincts into skills, commands, or agents |
| [`/instinct-status`](skills/instinct-status/) | Show all learned instincts with confidence levels |
| [`/instinct-export`](skills/instinct-export/) | Export instincts for sharing |
| [`/instinct-import`](skills/instinct-import/) | Import instincts from other sources |
| [`/skill-create`](skills/skill-create/) | Generate SKILL.md files from local git history |

### Evaluation and Verification

| Skill | Description |
|-------|-------------|
| [`/eval`](skills/eval/) | Eval-driven development workflow |
| [`/eval-harness`](skills/eval-harness/) | Formal evaluation framework for Claude Code sessions |
| [`/validate`](skills/validate/) | Run project-specific validation suite |
| [`/verify`](skills/verify/) | Comprehensive verification on current codebase state |

### Bug Fixing

| Skill | Description |
|-------|-------------|
| [`/rca`](skills/rca/) | Root cause analysis for bugs |
| [`/implement-fix`](skills/implement-fix/) | Implement bug fix based on RCA |

### Python Review

| Skill | Description |
|-------|-------------|
| [`/python-review`](skills/python-review/) | Python code review for PEP 8, type hints, security, idioms |

---

## Installation

### Install Everything

**Linux/macOS:**
```bash
git clone https://github.com/mindmatter-aia/shared_skillz.git
cd shared_skillz

# Commands
cp commands/*/*.md ~/.claude/commands/

# Agents
for dir in agents/*/; do
  cp "$dir"/*.md ~/.claude/agents/
done

# Skills
for dir in skills/*/; do
  name=$(basename "$dir")
  mkdir -p ~/.claude/skills/"$name"
  cp "$dir"/*.md ~/.claude/skills/"$name"/
done

# optimize-context (special structure)
cp -r optimize-context ~/.claude/skills/
```

**Windows PowerShell:**
```powershell
git clone https://github.com/mindmatter-aia/shared_skillz.git
cd shared_skillz

# Commands
Get-ChildItem commands\*\*.md | ForEach-Object {
    Copy-Item $_.FullName "$env:USERPROFILE\.claude\commands\"
}

# Agents
Get-ChildItem agents\*\*.md | Where-Object { $_.Name -ne "README.md" } | ForEach-Object {
    Copy-Item $_.FullName "$env:USERPROFILE\.claude\agents\"
}

# Skills
Get-ChildItem -Directory skills\* | ForEach-Object {
    $dest = "$env:USERPROFILE\.claude\skills\$($_.Name)"
    New-Item -ItemType Directory -Force -Path $dest | Out-Null
    Copy-Item "$($_.FullName)\*.md" $dest
}

# optimize-context
Copy-Item -Recurse optimize-context "$env:USERPROFILE\.claude\skills\"
```

### Install Individual Items

Each item has its own README with install instructions. Navigate to any directory for details.

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for submission guidelines.

### Quick Checklist

1. Fork this repository
2. Add your item in the correct directory (`commands/`, `agents/`, or `skills/`)
3. Include both the definition file and a README.md
4. Test in a real project
5. Submit a pull request

---

## Acknowledgments

**Built on ideas from:**
- [Cole Medin's PIV Loop](https://github.com/coleam00) -- Prime, Plan, Execute, Validate methodology
- [Affaan Mustafa's Everything Claude Code](https://github.com/affaan-m/everything-claude-code) -- Comprehensive Claude Code tooling
- [The Longform Guide](https://x.com/affaanmustafa) by @affaanmustafa -- Strategic context management patterns
- [Homunculus](https://github.com/humanplane/homunculus) by humanplane -- Instinct-based learning system
- [Supabase Agent Skills](https://github.com/supabase/agent-skills) -- PostgreSQL best practices

---

## License

MIT License -- see [LICENSE](LICENSE) for details.

Copyright (c) 2026 Nick Martin, PatriotAgentic LLC

---

**Maintained by:** [mindmatter-aia](https://github.com/mindmatter-aia)
**Issues:** [GitHub Issues](https://github.com/mindmatter-aia/shared_skillz/issues)
**Last Updated:** March 2026
**Items Count:** 64 (8 commands + 13 agents + 43 skills)
