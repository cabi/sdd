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

**Loads skills:** `sdd-control`, `sdd-memory`, `sdd-tasks-scl`
