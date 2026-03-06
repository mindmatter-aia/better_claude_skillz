---
name: rca
description: Perform root cause analysis for a bug
argument-hint: "[bug-description]"
---

# RCA: Root Cause Analysis

## Overview

Perform systematic root cause analysis for a bug to understand the underlying issue before implementing a fix.

## Input

Bug description or GitHub issue number: `$ARGUMENTS`

## Process

### 1. Gather Information

**If GitHub issue provided:**
```bash
gh issue view [issue-number]
```

**If bug description provided:**
Document the symptoms:
- What is the expected behavior?
- What is the actual behavior?
- When does it occur?
- Steps to reproduce
- Error messages or logs

### 2. Reproduce the Bug

Attempt to reproduce the issue locally:

```bash
# Follow reproduction steps
# Capture error output
# Note exact conditions
```

Document:
- Can reproduce: Yes/No
- Frequency: Always/Sometimes/Rare
- Environment: Dev/Staging/Production
- Conditions required

### 3. Investigate the Code

**Trace the execution path:**

1. Find the entry point (API endpoint, function call, etc.)
2. Follow the code path step by step
3. Identify where actual behavior diverges from expected
4. Read relevant code sections completely

**Questions to answer:**
- What code is involved?
- What data flows through this code?
- What assumptions does the code make?
- Are there edge cases not handled?

### 4. Check Related Systems

**Review:**
- Recent commits: `git log --since="2 weeks ago" --oneline`
- Recent changes to affected files: `git log -p -- [file-path]`
- Dependencies: Check for recent updates
- Configuration: Environment variables, settings
- External services: API changes, database schema

### 5. Analyze Root Cause

**Common root causes:**

- **Logic Error:** Incorrect conditional, off-by-one, wrong algorithm
- **State Management:** Race condition, incorrect state updates
- **Data Validation:** Missing validation, incorrect constraints
- **Error Handling:** Unhandled exception, incorrect error recovery
- **Integration:** API changes, breaking dependency updates
- **Configuration:** Wrong environment settings, missing config
- **Assumptions:** Invalid assumptions about input/state

**Identify THE root cause:**
- Not just the symptom
- Not just the trigger
- The underlying reason the bug exists

### 6. Assess Impact

**Scope of impact:**
- How many users affected?
- What functionality is broken?
- Data integrity concerns?
- Security implications?

**Severity classification:**
- **Critical:** Data loss, security breach, complete system failure
- **High:** Major functionality broken, affects many users
- **Medium:** Minor functionality broken, workaround exists
- **Low:** Cosmetic issue, edge case, minimal impact

### 7. Identify Fix Strategy

**Potential approaches:**

1. **Direct Fix:** Correct the logic/code directly
2. **Validation Fix:** Add missing validation/error handling
3. **Refactor:** Restructure code to prevent issue class
4. **Configuration:** Update settings/environment
5. **Dependency:** Update or replace problematic dependency

**Choose best approach based on:**
- Complexity of implementation
- Risk of introducing new bugs
- Time to implement
- Long-term maintainability

## Output Format

Create RCA document: `.claude/plans/rca-[issue-number or brief-description].md`

```markdown
# Root Cause Analysis: [Bug Title]

**Issue:** [GitHub issue number or brief description]
**Date:** YYYY-MM-DD
**Severity:** Critical | High | Medium | Low

## Symptoms

**Expected Behavior:**
[What should happen]

**Actual Behavior:**
[What actually happens]

**Steps to Reproduce:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Error Messages:**
\`\`\`
[Error output or logs]
\`\`\`

## Reproduction

- **Can Reproduce:** Yes | No
- **Frequency:** Always | Sometimes | Rare
- **Environment:** Development | Staging | Production
- **Conditions Required:** [List conditions]

## Code Analysis

**Affected Files:**
- `[file-path]` (lines X-Y)
- `[file-path]` (lines X-Y)

**Execution Path:**
1. [Entry point]
2. [Function/method call]
3. [Where it breaks]

**Code Snippet:**
\`\`\`language
[Relevant code showing the issue]
\`\`\`

## Root Cause

**Category:** Logic Error | State Management | Data Validation | Error Handling | Integration | Configuration | Assumptions

**Explanation:**
[Detailed explanation of the underlying cause]

**Why it occurred:**
[Why this bug exists - missing test? Unclear requirement? Edge case?]

## Impact Assessment

**Severity:** Critical | High | Medium | Low

**Scope:**
- Users affected: [number or percentage]
- Features broken: [list]
- Data integrity: [concerns if any]
- Security: [implications if any]

**Urgency:** Immediate | High | Medium | Low

## Fix Strategy

**Recommended Approach:** [Chosen strategy]

**Rationale:**
[Why this approach over alternatives]

**Implementation Complexity:** Low | Medium | High

**Risk Level:** Low | Medium | High

**Estimated Time:** [time estimate]

## Alternatives Considered

1. **[Alternative 1]**
   - Pros: [benefits]
   - Cons: [drawbacks]
   - Why not chosen: [reason]

2. **[Alternative 2]**
   - Pros: [benefits]
   - Cons: [drawbacks]
   - Why not chosen: [reason]

## Prevention

**How to prevent similar bugs:**
- [ ] Add validation for [X]
- [ ] Add test case for [scenario]
- [ ] Update documentation for [Y]
- [ ] Add linting rule for [pattern]
- [ ] Update code review checklist

## Next Steps

1. Create implementation plan
2. Write failing test that reproduces bug
3. Implement fix
4. Verify fix resolves issue
5. Update tests and documentation
6. Deploy and monitor

**Ready for:** `/implement-fix .claude/plans/rca-[name].md`
```

## Output Confirmation

After creating RCA:

1. Confirm file path
2. Summarize root cause in 1-2 sentences
3. Confirm severity and urgency
4. Recommend next action (usually: create implementation plan)

## Notes

- Take time to truly understand the root cause
- Don't rush to implement without full analysis
- Consider systemic issues, not just point fixes
- Document thoroughly for future reference
- If root cause is unclear, gather more information before proceeding
