---
name: validate
description: Run project-specific validation suite
---

# Validate: Run Project Tests and Checks

## Overview

This command runs the project's full validation suite. The specific commands depend on the project's tech stack and should be defined in the project's CLAUDE.md file.

## Standard Validation Levels

### Level 1: Syntax & Style

**Python Projects:**
```bash
# Linting
ruff check .
# or
pylint src/
# or
flake8 .

# Formatting check
black --check .
# or
ruff format --check .

# Type checking
mypy src/
```

**Node.js/JavaScript Projects:**
```bash
# Linting
npm run lint
# or
eslint src/

# Formatting check
prettier --check "src/**/*.{js,jsx,ts,tsx}"

# Type checking (TypeScript)
tsc --noEmit
```

**PHP Projects:**
```bash
# Linting
./vendor/bin/phpcs

# Static analysis
./vendor/bin/phpstan analyse
```

### Level 2: Unit Tests

**Python:**
```bash
# pytest
pytest tests/ -v
# With coverage
pytest --cov=app --cov-report=term-missing

# unittest
python -m unittest discover tests/
```

**Node.js:**
```bash
# Jest
npm test
# or
jest --coverage

# Vitest
npm run test
```

**PHP:**
```bash
# PHPUnit
./vendor/bin/phpunit tests/
```

### Level 3: Integration Tests

Run integration or end-to-end tests if configured:

```bash
# Playwright (Node.js)
npx playwright test

# Pytest integration tests
pytest tests/integration/ -v

# PHP integration tests
./vendor/bin/phpunit tests/Integration/
```

### Level 4: Build Verification

Ensure project builds successfully:

```bash
# Node.js
npm run build

# Python (if applicable)
python -m build

# PHP (if applicable)
composer install --no-dev
```

## Project-Specific Instructions

**IMPORTANT:** Check the project's CLAUDE.md file for specific validation commands. The project should define:

```markdown
## Validation Commands

### Syntax & Style
\`\`\`bash
[project-specific linting commands]
\`\`\`

### Unit Tests
\`\`\`bash
[project-specific test commands]
\`\`\`

### Integration Tests
\`\`\`bash
[project-specific integration test commands]
\`\`\`

### Build
\`\`\`bash
[project-specific build commands]
\`\`\`
```

## Execution Strategy

1. **Read Project CLAUDE.md** first to get specific commands
2. **Run validation levels in order** (1 -> 2 -> 3 -> 4)
3. **Stop on first failure** and report the issue
4. **Provide clear summary** of all validation results

## Output Report

After running all validations, provide:

### Validation Summary

```
Level 1: Syntax & Style - PASSED
Level 2: Unit Tests - PASSED (15 tests, 0 failures)
Level 3: Integration Tests - PASSED (5 tests, 0 failures)
Level 4: Build - PASSED

Overall Status: ALL VALIDATIONS PASSED
```

Or if failures:

```
Level 1: Syntax & Style - PASSED
Level 2: Unit Tests - FAILED (15 tests, 2 failures)

Failed Tests:
- test_user_authentication: AssertionError
- test_data_validation: KeyError

Overall Status: VALIDATION FAILED
```

## Notes

- Some projects may not have all validation levels
- Adapt commands based on project's actual setup
- If validation commands are missing, suggest adding them to project CLAUDE.md
