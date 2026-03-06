---
name: execution-report
description: Document what was implemented vs planned
argument-hint: "[plan-file]"
---

# Execution Report: Implementation Documentation

## Purpose

Document what was actually implemented compared to the plan, including any divergences and reasons.

## Input

Provide the plan file that was executed: `$ARGUMENTS`

## Report Structure

Create a report in `.claude/code-reviews/YYYY-MM-DD-[feature-name]-execution.md`

### 1. Implementation Overview

**Plan Executed:** [path to plan file]
**Date:** [YYYY-MM-DD]
**Feature:** [feature name]

**Summary:**
[2-3 sentence summary of what was built]

### 2. Tasks Completed

List all tasks from the plan with status:

```markdown
- [x] Task 1: Create database schema
- [x] Task 2: Implement API endpoints
- [x] Task 3: Add unit tests
- [ ] Task 4: Add integration tests (skipped - see divergences)
```

### 3. Files Created

```
- src/models/user.py (234 lines)
- src/routes/auth.py (189 lines)
- tests/test_auth.py (156 lines)
```

### 4. Files Modified

```
- src/main.py (added router registration, +15 lines)
- src/database.py (added new table, +8 lines)
```

### 5. Validation Results

**Syntax & Style:**
```bash
$ ruff check .
All checks passed!
```

**Unit Tests:**
```bash
$ pytest tests/ -v
======================== 24 passed in 2.31s ========================
```

**Coverage:**
```bash
$ pytest --cov=src --cov-report=term
TOTAL coverage: 87%
```

### 6. Divergences from Plan

Document any differences between plan and implementation:

#### Divergence 1: [Brief description]

**Planned:**
[What the plan specified]

**Actually Implemented:**
[What was done instead]

**Reason:**
[Why the divergence occurred]

**Classification:** Justified | Problematic

**Examples:**
- Justified: Used library X instead of Y because it's already in dependencies
- Justified: Added error handling not in plan for robustness
- Problematic: Skipped validation step that should have been done
- Problematic: Used different pattern than existing codebase

### 7. Challenges Encountered

**Challenge 1:** [Description]
- **Solution:** [How it was resolved]
- **Time impact:** [None/Minor/Major]

### 8. Testing Summary

**Unit Tests:**
- Tests written: X
- Tests passing: X
- Coverage: X%

**Integration Tests:**
- Tests written: X
- Tests passing: X

**Manual Testing:**
- [List manual tests performed and results]

### 9. Acceptance Criteria Status

From the plan, review each acceptance criterion:

- [x] Feature implements all specified functionality
- [x] All validation commands pass with zero errors
- [x] Unit test coverage meets requirements (87% > 80%)
- [ ] Integration tests verify end-to-end workflows (not yet implemented)
- [x] Code follows project conventions and patterns
- [x] No regressions in existing functionality

**Overall:** X/Y criteria met

### 10. Next Steps

If feature is incomplete:
- [ ] Task that still needs completion
- [ ] Integration tests to be added
- [ ] Documentation to be updated

If feature is complete:
- Ready for code review
- Ready for commit
- Ready for deployment (if applicable)

### 11. Lessons Learned

**What worked well:**
- [Things that went smoothly]

**What could be improved:**
- [Process improvements identified]
- [Planning improvements needed]

## Output

After creating the report:

1. Confirm the file path where it was written
2. Provide executive summary of implementation
3. Highlight any critical divergences
4. Suggest whether system review is needed (if multiple problematic divergences)
