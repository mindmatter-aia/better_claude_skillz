---
name: implement-fix
description: Implement bug fix based on RCA
argument-hint: "[rca-file]"
---

# Implement Fix: Bug Fix Implementation

## Overview

Implement a bug fix based on the root cause analysis, following test-driven development principles.

## Input

RCA file: `$ARGUMENTS`

## Process

### 1. Read RCA Document

Thoroughly read the RCA to understand:
- Root cause
- Fix strategy
- Impact assessment
- Prevention measures

### 2. Write Failing Test (TDD)

**Before fixing the bug, write a test that reproduces it:**

```bash
# Create or update test file
# Run test to confirm it fails
```

**Test should:**
- Reproduce the exact bug scenario
- Fail with the current code
- Pass once the fix is implemented

**Example test structure:**
```python
def test_bug_issue_123():
    """Test for bug: [description]

    GitHub Issue: #123
    RCA: .claude/plans/rca-issue-123.md
    """
    # Arrange: Set up conditions that trigger bug
    [setup code]

    # Act: Execute the buggy code path
    result = [function_that_has_bug]()

    # Assert: Verify expected behavior (currently fails)
    assert result == expected_value
```

### 3. Verify Test Fails

Run the test to confirm it fails as expected:

```bash
[test command for project]
```

Output should show the test failing, confirming we've reproduced the bug.

### 4. Implement the Fix

Follow the fix strategy from the RCA:

**Implementation guidelines:**
- Make minimal changes to fix the specific issue
- Don't refactor unrelated code
- Follow existing code patterns
- Add comments explaining the fix if non-obvious
- Consider edge cases

**Code changes:**
- Update affected files
- Add necessary validation
- Improve error handling
- Update related code if needed

### 5. Verify Test Passes

Run the specific test again:

```bash
[test command]
```

The previously failing test should now pass.

### 6. Run Full Test Suite

Ensure no regressions:

```bash
[full test suite command]
```

All tests should pass.

### 7. Manual Verification

Test the fix manually following original reproduction steps:

1. [Step 1 from reproduction]
2. [Step 2]
3. [Step 3]

Verify:
- Bug no longer occurs
- Expected behavior works
- No new issues introduced

### 8. Implement Prevention Measures

From the RCA, implement prevention steps:

- [ ] Add additional test cases for edge cases
- [ ] Update validation logic
- [ ] Add documentation
- [ ] Update error messages
- [ ] Add logging if appropriate

### 9. Code Review

Perform self-review:

- [ ] Fix addresses root cause (not just symptoms)
- [ ] Code follows project conventions
- [ ] Tests are comprehensive
- [ ] No over-engineering
- [ ] Comments explain "why" not "what"
- [ ] Error handling is appropriate

### 10. Document the Fix

Update or create documentation:

**In code:**
```python
# Fix for GitHub Issue #123: [brief description]
# Root cause: [one-line explanation]
# See: .claude/plans/rca-issue-123.md
```

**Update RCA document** with implementation notes:
```markdown
## Implementation

**Date Implemented:** YYYY-MM-DD
**Files Changed:**
- `[file]` - [what was changed]

**Test Added:**
- `[test file]` - [test description]

**Verification:**
- Test passes
- Full suite passes
- Manual verification successful
```

## Validation Checklist

Before committing:

- [ ] Failing test created that reproduces bug
- [ ] Test failed before fix
- [ ] Fix implemented following RCA strategy
- [ ] Test now passes
- [ ] Full test suite passes (no regressions)
- [ ] Manual verification successful
- [ ] Prevention measures implemented
- [ ] Code reviewed
- [ ] Documentation updated
- [ ] RCA document updated with implementation notes

## Output Format

Create implementation report: `.claude/code-reviews/YYYY-MM-DD-bug-fix-[issue].md`

```markdown
# Bug Fix Implementation: [Bug Title]

**Issue:** #[number]
**RCA:** [path to RCA file]
**Date:** YYYY-MM-DD

## Summary

[Brief description of what was fixed and how]

## Changes Made

### Files Modified

- `[file-path]` - [description of changes]
- `[file-path]` - [description of changes]

### Tests Added

- `[test-file]` - [test description]
- Coverage added: [X] test cases

## Verification

### Test Results

**Before Fix:**
\`\`\`
[output showing failing test]
\`\`\`

**After Fix:**
\`\`\`
[output showing passing test]
\`\`\`

**Full Suite:**
\`\`\`
[output showing all tests pass]
\`\`\`

### Manual Testing

- [x] Followed original reproduction steps
- [x] Bug no longer occurs
- [x] Expected behavior confirmed
- [x] No new issues observed

## Prevention Measures

- [x] Test case added for bug scenario
- [x] Test case added for edge cases
- [ ] Documentation updated (if applicable)
- [ ] Validation logic strengthened (if applicable)

## Ready for Commit

All validation passed. Ready to commit with:
`/commit`
```

## Commit Message Template

When ready to commit, use this format:

```
fix(scope): [brief description of fix]

Fixes #[issue-number]

Root cause: [one-line explanation]
Solution: [one-line explanation]

RCA: .claude/plans/rca-[issue].md
```

## Output

After implementation, provide:

```
Bug fix implemented and verified

Issue: #[number]
Root Cause: [brief description]
Fix: [brief description]

Changes:
- Modified: X files
- Tests added: Y test cases

Verification:
Specific test passes
Full test suite passes (N tests)
Manual verification successful

Ready for commit.
```

## Notes

- Always write the test BEFORE fixing the bug
- The test should fail initially (confirming bug exists)
- Make minimal changes to fix the specific issue
- Ensure full test suite passes (no regressions)
- Update RCA with implementation details
- Don't skip manual verification
