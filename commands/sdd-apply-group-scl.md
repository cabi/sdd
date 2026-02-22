---
name: sdd-apply-group-scl
description: Execute all tasks in a specific group via subagent with SCL memory context and control validation
---

Execute all tasks in a task group using a subagent with SCL-enhanced scope boundaries and memory context.

**Usage:** `/sdd-apply-group-scl <number>`

**Process (SCL-Enhanced):**

## Phase 1: Retrieve

1. **Parse tasks.md** for the specified group
2. **Load memory context**:
   - Read `.memory/decisions.json` for relevant decisions
   - Read `.memory/requirements.json` for task requirements
   - Read `.memory/citations.json` for existing citations
   - Read prior group outcomes from `.memory/episodes.json`
3. **Read regulation.md** for constraints
4. **Verify preconditions**:
   - Check dependency groups complete
   - Check required files exist
   - Check required decisions in memory

## Phase 2: Control (Pre-Dispatch)

5. **CONTROL.check_preconditions()**:
   - Verify all preconditions satisfied
   - IF not satisfied: BLOCK and report missing
6. **Generate scope constraints**:
   - Extract allowed files from task metadata
   - Generate blocked files list
   - Define required citations

## Phase 3: Dispatch

7. **Build subagent prompt** with:
   - Memory context (decisions, requirements, prior outcomes)
   - Scope constraints (allowed/blocked files)
   - Task list with evidence citations
   - Validation criteria
   - Completion signal requirement
8. **Dispatch subagent** with constrained scope

## Phase 4: Verify

9. **Wait for completion signal**: "GROUP N COMPLETE"
10. **Verify scope compliance**:
    - Files modified ⊆ allowed files
    - No blocked files touched
    - Required citations present
11. **Verify task completion**:
    - All task checkboxes marked
    - Validation criteria satisfied
12. **Verify tests pass** (if specified)

## Phase 5: Memory Update

13. **Update memory**:
    - Record files created/modified
    - Update requirement status
    - Record new citations
    - Log episode in episodes.json
14. **Log control checkpoint**

**Subagent Prompt Structure:**
```
You are executing Group N: <Group Name> of <spec-name>.

## Memory Context

### Decisions You MUST Follow
[Relevant decisions with sources]

### Requirements You MUST Satisfy
[Relevant requirements with sources]

### Prior Work Outcomes
[What was done in prior groups]

## Constraints (VIOLATION = FAILURE)

### Allowed Files
You MAY only create/modify: [list]

### Blocked Files
You MUST NOT touch: [list]

### Required Citations
Every file MUST include:
// Implements: REQ-ID (per specs/.../spec.md#L<N>)

## Your Tasks
[Task list with evidence citations]

## Completion Criteria
You MUST:
1. Complete ALL tasks
2. Verify all files exist
3. Ensure all tests pass
4. Output "GROUP N COMPLETE" as final line

DO NOT:
- Start work on Group N+1
- Modify files outside allowed_files
- Skip validation steps
```

**Output:**
```
✓ Retrieved: Memory context (3 decisions, 5 requirements)
✓ Control: Preconditions verified
✓ Dispatch: Subagent for Group 2 with scope constraints

[Subagent executes...]

✓ Completion Signal: "GROUP 2 COMPLETE"
✓ Scope Verified: All files in allowed list
✓ Tasks Verified: 3/3 complete
✓ Tests: All passing

Memory Updates:
  requirements.json: AUTH-001, AUTH-002 → implemented
  citations.json: +5 new citations
  episodes.json: +1 episode

Ready for next group. Use /sdd-apply-group-scl 3 or /sdd-apply-all-scl
```

**If control blocks:**
```
✗ Control: BLOCKED

Precondition failures:
  - Group 1 not complete (required for Group 2)
  - Missing file: src/models/User.ts

Required actions:
  1. Complete Group 1 first
  2. Or use /sdd-apply-group-scl 1
```

**If scope violated:**
```
✗ Scope Violation Detected

Files modified outside allowed scope:
  - src/core/Database.ts (blocked)

Tasks completed: 2/3
Memory state preserved.

Options:
  [1] Rollback changes and retry
  [2] Accept violation and continue (requires override)
  [3] Manual intervention
```

**Loads skills:** `sdd-control`, `sdd-memory`
