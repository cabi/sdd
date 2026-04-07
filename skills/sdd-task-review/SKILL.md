---
name: sdd-task-review
description: Methodology for iterative task review with analyst feedback. Implements a mandatory 3-iteration review loop where tasks are created, critiqued, and refined to maximize implementation readiness.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: advanced
  author: OpenCode
  version: "1.0.0"
---

# SDD Task Review Loop

Iterative task refinement through structured critique and revision cycles.

## Overview

This skill implements a mandatory 3-iteration review loop:

```
┌─────────────────────────────────────────────────────────────────┐
│                    TASK REVIEW LOOP                              │
│                  (orchestrated by /sdd-artefact)                  │
│                                                                   │
│  ┌──────────────────┐                                            │
│  │  @sdd-task       │                                            │
│  │  MODE=create     │                                            │
│  │  → tasks.md v1   │                                            │
│  └────────┬─────────┘                                            │
│           │                                                       │
│           ▼                                                       │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  ITERATION 1                                                  │ │
│  │  ┌─────────────────┐  ┌─────────────────────┐               │ │
│  │  │ @sdd-task-      │─►│ task-review-        │               │ │
│  │  │ analyst         │  │ iteration-1.md      │               │ │
│  │  └─────────────────┘  └───────┬─────────────┘               │ │
│  │                               │                              │ │
│  │  ┌─────────────────┐          │                              │ │
│  │  │ @sdd-task       │◄─────────┘                              │ │
│  │  │ MODE=revise     │                                         │ │
│  │  │ → tasks.md v2   │                                         │ │
│  │  └─────────────────┘                                         │ │
│  └─────────────────────────────────────────────┬───────────────┘ │
│                                                │                  │
│                                                ▼                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  ITERATION 2-3 (same pattern)                                │ │
│  │  analyst → task-review-N.md → task agent revise → tasks.md  │ │
│  └─────────────────────────────────────────────┬───────────────┘ │
│                                                │                  │
│                                                ▼                  │
│                                     ┌──────────────────┐          │
│                                     │ Final tasks.md   │          │
│                                     │ (after iter 3)   │          │
│                                     └──────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

## When to Use Review Loop

**ALWAYS use review loop for:**
- Tasks derived from a design that went through the design review loop
- Any task set intended for subagent execution (`/sdd-apply-group`, `/sdd-apply-all`)
- Multi-group task breakdowns with dependencies
- Tasks covering cross-cutting concerns or migrations

**Review loop adds ~15-20 minutes** but significantly improves implementation quality.

## Review Loop Protocol

### Iteration Structure

Each iteration follows the same pattern:

```javascript
async function reviewIteration(tasks, iterationNumber) {
  // 1. Invoke analyst
  const critique = await invokeAgent('sdd-task-analyst', {
    tasks: tasks,
    iteration: iterationNumber,
    previousReviews: getPreviousReviews(iterationNumber)
  });

  // 2. Save critique report
  writeReport(`task-review-iteration-${iterationNumber}.md`, critique);

  // 3. Enforce mandatory iteration count and final gate
  if (iterationNumber < 3) {
    const revisedTasks = reviseTasks(tasks, critique);
    documentChanges(iterationNumber, critique.issues, revisedTasks.changes);
    return { approved: false, tasks: revisedTasks };
  }

  // 4. Iteration 3 final gate
  const hasBlockingIssues = critique.criticalCount > 0 || critique.majorCount > 0;
  if (critique.verdict !== 'APPROVE' || hasBlockingIssues) {
    return { approved: false, tasks, blocked: 'Final gate failed' };
  }

  // 5. Final polish and completion
  const finalizedTasks = applyFinalPolish(tasks, critique.suggestions);
  documentRemainingMinors(finalizedTasks, critique.minorFindings);
  documentChanges(iterationNumber, critique.issues, finalizedTasks.changes);
  return { approved: true, tasks: finalizedTasks };
}
```

### 3-Iteration Mandatory Loop

The loop **MUST** complete all 3 iterations:

| Iteration | Focus | Expected Outcome |
|-----------|-------|------------------|
| **1** | Find all critical issues | Address coverage gaps, cyclic dependencies, missing requirement references |
| **2** | Verify fixes, find major issues | Address sizing problems, vague descriptions, missing task types |
| **3** | Final verification | APPROVE with 0 critical, 0 major unresolved, unresolved minors documented |

Even if iteration 1 or 2 returns APPROVE, continue through iteration 3 for final verification.

### Early Termination

The loop **MUST NOT** terminate before iteration 3.

### Minor Findings Policy

- MIN-* findings **SHOULD** be fixed during each iteration.
- Any unresolved MIN-* finding at iteration 3 **MUST** be documented with rationale in `Task Iteration History`.
- Any MIN-* finding that impacts implementation correctness, parallel execution safety, or requirement coverage **MUST** be reclassified to MAJOR or CRITICAL.

## Revision Guidelines

### Addressing Critical Issues

For each CRITICAL issue:

```markdown
### Change Log Entry

**Issue:** CRIT-001 - Requirement AUTH-003 has no task coverage
**Resolution:** Added task 2.4 for password reset implementation
**Location:** tasks.md#Group 2, Task 2.4

**Added:**
> - [ ] 2.4 Implement password reset endpoint
>   - POST /auth/reset-password with token validation
>   - _Requirements: auth-003_
>   - _Creates: src/auth/routes/reset.ts_
```

### Addressing Major Issues

For each MAJOR issue:

```markdown
**Issue:** MAJ-003 - Task 2.1 is too large (covers 3 requirements, 4 files)
**Resolution:** Split into tasks 2.1a, 2.1b, 2.1c
**Location:** tasks.md#Group 2
```

### Tracking Changes

Maintain a change log at the end of tasks.md:

```markdown
## Task Iteration History

### Iteration 3 → Final (Current)
**Issues Addressed:** 1 minor, 0 critical, 0 major
- MIN-001: Added file hint to task 4.2 for clarity

### Iteration 2 → 3
**Issues Addressed:** 1 major, 0 critical
- MAJ-001: Split task 2.1 into 2.1a, 2.1b (was too large)

### Iteration 1 → 2
**Issues Addressed:** 2 critical, 3 major
- CRIT-001: Added task 2.4 for missing requirement AUTH-003
- CRIT-002: Fixed circular dependency between groups 2 and 3
- MAJ-001 through MAJ-003: Improved task descriptions, added file hints
```

## Review Reports Location

All review reports are saved in the change directory:

```
.specs/changes/<change-name>/
├── tasks.md                          # Final tasks (after 3 iterations)
├── task-review-iteration-1.md        # First critique
├── task-review-iteration-2.md        # Second critique
└── task-review-iteration-3.md        # Third critique
```

## Quality Metrics

Track these metrics across iterations:

| Metric | Target |
|--------|--------|
| Critical Issues | 0 by iteration 3 |
| Major Issues | 0 unresolved by iteration 3 |
| Requirements Covered | 100% |
| Design Elements Covered | 100% |
| Tasks Appropriately Sized | ≥90% |
| Dependency Graph Valid | Yes |
| Tasks with Requirement Refs | 100% |

### Metric Improvement Pattern

```
Iteration 1: 3 critical, 5 major, 8 minor → REVISE
Iteration 2: 0 critical, 2 major, 4 minor → REVISE
Iteration 3: 0 critical, 0 major, 1 minor → APPROVE
```

## Integration with SDD Workflow

### Command Integration

The review loop is orchestrated by `/sdd-artefact` (or `/sdd-ff`). The command:

1. Invokes `@sdd-task` with MODE="create" to generate the initial tasks
2. Runs 3 iterations, each consisting of:
   a. Invokes `@sdd-task-analyst` with ITERATION=N to produce a critique
   b. Invokes `@sdd-task` with MODE="revise" and ITERATION=N to apply fixes
3. After iteration 3, verifies the final gate (APPROVE, 0 critical, 0 major)

No separate command needed - review is always performed automatically during task creation.

### Agent Roles

- **`@sdd-task`** (loads `sdd-tasks` skill): Creates initial tasks and applies revisions
- **`@sdd-task-analyst`** (loads this skill): Produces critique reports

## Analyst Agent Behavior

The `sdd-task-analyst` agent:

1. **Reads the tasks** and all context files (specs, design, proposal)
2. **Performs systematic analysis** across all 10 categories
3. **Prioritizes issues** by severity
4. **Writes critique report** to `task-review-iteration-N.md`
5. **Returns verdict** with reasoning

### Verdict Types

| Verdict | Meaning | Action |
|---------|---------|--------|
| **REVISE** | Significant issues found | Must revise before next iteration |
| **CONDITIONAL** | Minor issues only | May proceed after quick fixes |
| **APPROVE** | Ready for implementation | Continue to next iteration or finalize |

## Example: 3-Iteration Review

### Iteration 1

**Analyst finds:**
- CRIT-001: Requirement AUTH-003 (password reset) has no task
- CRIT-002: Groups 2 and 3 both modify `src/auth/routes/index.ts` but both marked parallel-safe
- MAJ-001: Task 2.1 covers 3 requirements and modifies 4 files — too large
- MAJ-002: No testing tasks found

**Task author revises:**
- Adds task 2.4 for password reset
- Groups 2 and 3 set to sequential dependency
- Splits task 2.1 into 2.1a, 2.1b, 2.1c
- Adds Group 4 (Testing) with appropriate tasks

### Iteration 2

**Analyst finds:**
- MAJ-003: Task 3.1 description is vague ("create the route")
- MAJ-004: Task 2.3 references requirement AUTH-007 but AUTH-007 doesn't exist
- MIN-001: Task 1.2 missing _Modifies file hint

**Task author revises:**
- Rewrites task 3.1 with specific endpoint, request/response format
- Fixes requirement reference to AUTH-004
- Adds _Modifies: package.json to task 1.2

### Iteration 3

**Analyst finds:**
- MIN-002: Consider merging tasks 5.1 and 5.2 (both tiny documentation updates)

**Verdict:** APPROVE

**Task author finalizes:**
- Merges tasks 5.1 and 5.2
- Writes final tasks.md
- Confirms 0 critical / 0 major unresolved

## Anti-Patterns to Avoid

### 1. Skipping Iterations

**BAD:** "Iteration 1 looks good, let's stop"
**GOOD:** Complete all 3 iterations for thorough review

### 2. Not Addressing All Issues

**BAD:** "I'll fix the critical ones, skip the majors"
**GOOD:** Address ALL critical and major issues each iteration

### 2b. Misclassifying Minors

**BAD:** "This parallel-safety concern is minor"
**GOOD:** Reclassify implementation-correctness and parallel-safety items to MAJOR or CRITICAL

### 3. Repeating the Same Tasks

**BAD:** Submit unchanged tasks to next iteration
**GOOD:** Document specific changes made

### 4. Ignoring Previous Reviews

**BAD:** Analyst re-finds the same issue from iteration 1
**GOOD:** Task author confirms fix, analyst verifies

## Checklist: Before Each Iteration

- [ ] Previous review report read
- [ ] All issues from previous iteration addressed
- [ ] Changes documented in tasks
- [ ] No new dependency cycles introduced
- [ ] All requirements still covered after changes

## Checklist: After 3 Iterations

- [ ] 0 critical issues remaining
- [ ] 0 major unresolved issues remaining
- [ ] Unresolved minor issues (if any) documented with rationale
- [ ] 100% requirement coverage confirmed
- [ ] 100% design element coverage confirmed
- [ ] All groups have _Meta fields
- [ ] Dependency graph is a valid DAG
- [ ] All tasks have requirement references
- [ ] Task sizes are appropriate (2-4 hours)
- [ ] Change log shows progression
- [ ] All review reports saved
