---
name: sdd-apply-all
description: Execute all remaining task groups via subagents
---

Execute all remaining task groups in dependency order using subagents.

**Usage:** `/sdd:apply-all [options]`

**Options:**
- No option: Ask for confirmation mode
- `--auto`: Run all without asking
- `--confirm-each`: Confirm after each group (default prompt)

**Process:**
1. Parse tasks.md for all groups and metadata
2. Build dependency graph
3. Determine execution order (topological sort)
4. Show execution plan to user
5. Ask for confirmation mode
6. For each group in order:
   - Check dependencies satisfied
   - Dispatch subagent with scope constraints
   - Wait for "GROUP N COMPLETE" signal
   - Verify results
   - If confirm mode: ask to continue
7. Report final summary

**Execution Plan Example:**
```
Found 4 task groups:

| Group | Tasks | Depends On | Parallel-Safe |
|-------|-------|------------|---------------|
| 1. Setup | 3 | - | no |
| 2. Core | 3 | 1 | yes |
| 3. API | 4 | 2 | no |
| 4. Test | 2 | 3 | no |

Execution order: 1 → 2 → 3 → 4

Confirmation mode:
[1] Auto (no prompts)
[2] Confirm after each group
[3] Custom
```

**Scope Constraints:**
- Each subagent receives ONLY its group's tasks
- Strict file modification boundaries
- Required completion signal
- Verification after each group

**Error Handling:**
- If subagent exceeds scope → warn, offer rollback
- If subagent stops early → show partial results, offer retry
- If dependency not satisfied → abort, explain why

**Final Summary:**
```
✓ All groups complete

Groups executed: 4
Total tasks: 12/12
Files created: 8
Files modified: 3

Spec: user-authentication is ready for /sdd:archive
```
