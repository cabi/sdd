---
name: sdd-apply
description: Implement tasks from a spec
---

Implement the next task from my spec.

Follow the sdd-spec-apply skill:

1. **Load spec context** - Read proposal, specs, design, tasks
2. **Find the next unchecked task** - First `- [ ]` in tasks.md
3. **Understand the requirement** - Read the referenced spec
4. **Implement minimally** - Only what's needed for this task
5. **Mark the task complete** - Update `- [ ]` to `- [x]`
6. **Report progress** - Show what was done and what's next

If implementation reveals a gap in the spec:
- STOP and update the spec first
- Document why the change was needed
- Continue implementation

Show me progress after each task.

---

## Valid Next Commands

**After completing a task:**
- `/sdd-apply` - Execute next task
- `/sdd-apply-group N` - Execute all tasks in group N
- `/sdd-apply-all` - Execute all remaining tasks
- `/sdd-status` - Check progress
- `/sdd-verify` - Verify implementation (after all tasks done)

**Do NOT suggest:**
- ❌ `/sdd-artefact` (already completed - specs/design/tasks exist)
- ❌ `/sdd-requirements` (skill, not command)

**Loads skill:** `sdd-spec-apply`
