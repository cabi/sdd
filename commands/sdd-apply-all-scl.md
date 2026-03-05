---
name: sdd-apply-all-scl
description: Execute all remaining task groups via subagents with SCL memory persistence
---

Execute all remaining task groups in dependency order using subagents with SCL-enhanced context management.

**Usage:** `/sdd-apply-all-scl [options]`

**Options:**
- No option: Ask for confirmation mode
- `--auto`: Run all without asking
- `--confirm-each`: Confirm after each group (default)
- `--dry-run`: Show plan without executing

**Process (SCL-Enhanced):**

## Phase 1: Retrieve

1. **Parse tasks.md** for all groups and metadata
2. **Build dependency graph** from group metadata
3. **Topological sort** to determine execution order
4. **Load full memory state**:
    - All decisions, requirements, citations
    - Prior episodes
    - Control checkpoints
    - Read `regulation.md` for scope and validation rules

## Phase 2: Plan

5. **Generate execution plan**:
   - Group order respecting dependencies
   - Parallel-safe groups identified
   - Estimated total time
6. **Show plan to user** with:
   - Group sequence
   - Memory context summary per group
   - Scope constraints per group

## Phase 3: Confirm

7. **Ask for confirmation mode**:
   ```
   Execution Plan for: user-authentication
   
   | Group | Tasks | Depends | Parallel | Memory Context | Est. |
   |-------|-------|---------|----------|----------------|------|
   | 1. Setup | 3 | - | no | 0 dec, 2 req | 1h |
   | 2. Core | 5 | 1 | yes | 2 dec, 5 req | 3h |
   | 3. API | 4 | 2 | no | 4 dec, 8 req | 2h |
   | 4. Tests | 3 | 3 | no | 6 dec, 10 req | 2h |
   
   Total: 15 tasks, ~8 hours
   Parallel execution: Groups 2A, 2B can run together
   
   Confirmation mode:
   [1] Auto (no prompts) - NOT RECOMMENDED
   [2] Confirm after each group - RECOMMENDED
   [3] Show me memory context first
   [4] Cancel
   ```

## Phase 4: Execute

8. **For each group in order**:

   ### Pre-Execution
   - **CONTROL.check_preconditions()**
   - **Build memory context** (decisions, requirements from prior groups)
   - **Generate scope constraints**
   - **IF blocked**: HALT and explain

   ### Dispatch
   - Dispatch subagent with full context
   - Wait for "GROUP N COMPLETE"

   **Subagent Prompt Structure (per group):**
   ```
   You are executing Group N: <Group Name> of <spec-name>.

   ## Memory Context

   ### Decisions You MUST Follow
   [Relevant decisions with sources]

   ### Requirements You MUST Satisfy
   [Relevant requirements with sources]

   ### Prior Work Outcomes
   [What was done in prior groups]

   ### Regulation Rules (from regulation.md)
   [Applicable scope and validation rules for this group]

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

   ### Post-Execution
   - **CONTROL.verify_scope_completion()**
   - **Update memory** (files, status, citations)
   - **Log episode**
   - **IF confirm mode**: Ask to continue

## Phase 5: Final Verification

9. **CONTROL.check_termination()**:
   - All tasks complete
   - All requirements verified
   - Goal fidelity score
10. **Generate summary**:
    - Files created/modified
    - Requirements implemented
    - Test coverage
    - Memory state

**Memory Persistence:**

Between each group, the system **MUST**:
1. Update `.memory/episodes.json` with group outcome
2. Update `.memory/requirements.json` with status changes
3. Update `.memory/citations.json` with new citations
4. Log checkpoint in `.memory/control-log.json`

**Parallel Execution:**

For groups marked `parallel-safe` with same dependencies:
```
Groups 2A and 2B both depend on Group 1, both parallel-safe.

Option:
[1] Sequential (2A → 2B) - Safer, easier to debug
[2] Parallel (2A || 2B) - Faster, but isolated memory

Note: Parallel groups have isolated memory context.
      Group 2B will NOT see Group 2A's outcomes until merged.
```

**Output:**
```
✓ All groups complete

Execution Summary:
  Groups: 4/4
  Tasks: 15/15 (100%)
  Duration: 7.5 hours
  
Memory State:
  decisions: 8 recorded
  requirements: 12 implemented, 2 pending
  citations: 34 recorded
  episodes: 4 logged

Files:
  Created: 12 files
  Modified: 3 files

Tests:
  Unit: 15/15 passing
  Integration: 3/3 passing
  Coverage: 87%

Goal Fidelity: 0.92

Verification:
  [✓] All requirements have implementations
  [✓] All implementations have citations
  [✓] All tests pass
  [✓] Memory consistent

Ready for: /sdd-verify-scl and /sdd-archive
```

**If execution fails:**
```
✗ Execution stopped at Group 3

Failure in Group 3: API Layer

Error: Control validation failed
  - Task 3.2 requires file from Group 2
  - File not found: src/auth/service/AuthService.ts

Memory state preserved at Group 2 completion.

Options:
  [1] Retry Group 3 after fixing issue
  [2] Rollback to Group 2 and retry
  [3] Manual intervention
  [4] View memory state for debugging
```

---

## Valid Next Commands

**After all groups completed:**
- `/sdd-verify-scl` - Verify implementation with memory tracing
- `/sdd-status` - Confirm all tasks done
- `/sdd-memory-status` - View final memory state
- `/sdd-archive` - Archive the change (after verification)

**Do NOT suggest:**
- ❌ `/sdd-apply-group-scl` (all groups already done)
- ❌ `/sdd-apply-all-scl` (all groups already done)
- ❌ `/sdd-artefact-scl` (already completed)
- ❌ `/sdd-verify` (use /sdd-verify-scl for SCL workflow)

**Loads skills:** `sdd-control`, `sdd-memory`
