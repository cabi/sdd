---
name: sdd-apply-group
description: Execute all tasks in a specific group via subagent
---

Execute all tasks in a task group using a subagent with strict scope boundaries.

**Usage:** `/sdd:apply-group <number>`

**Process:**
1. Parse tasks.md for the specified group
2. Extract group metadata and all tasks
3. Build constrained subagent prompt with:
   - Exact task numbers
   - Allowed file modifications
   - Required completion signal
4. Dispatch subagent
5. Verify "GROUP N COMPLETE" signal
6. Check task checkboxes match expected
7. Verify files modified are in allowed list
8. Report results

**Scope Constraints:**
- Subagent receives ONLY tasks for its group
- Subagent can ONLY modify files listed in task metadata
- Subagent MUST output completion signal and stop
- Any scope violation triggers warning

**Example:**
```
/sdd:apply-group 2

AI: Dispatching subagent for Group 2: Core Services
    Tasks: 2.1, 2.2, 2.3
    
    [Subagent executes...]
    
    ✓ GROUP 2 COMPLETE
    Completed: 2.1, 2.2, 2.3
    Files created: PasswordService.ts, TokenService.ts, UserService.ts
    
    Ready for next group. Use /sdd:apply-group 3 or /sdd:apply-all
```
