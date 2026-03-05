---
name: sdd-ff
description: Fast-forward - create all planning artifacts at once
---

Create all planning artifacts for my spec in one go.

Use when I have a clear picture of what I want to build and want to skip the incremental artifact creation.

**Process:**

1. **Confirm the spec** - Which spec to fast-forward
2. **Load proposal** - Read the context
3. **Create ALL artifacts in sequence:**
   - specs/ (requirements)
   - design.md (technical approach)
   - tasks.md (implementation checklist)
4. **Update proposal status** - Mark all complete
5. **Report what was created**

**Warning:** This creates everything at once. For complex features, use `/sdd-artefact` to allow review between artifacts.

---

## Valid Next Commands

**After fast-forward (all artifacts created):**
- `/sdd-apply` - Execute one task at a time
- `/sdd-apply-group N` - Execute group N
- `/sdd-apply-all` - Execute all groups
- `/sdd-status` - Review what was created

**Do NOT suggest:**
- ❌ `/sdd-artefact` (fast-forward already created all artifacts)
- ❌ `/sdd-explore` (already done)
- ❌ `/sdd-propose` (already done)
- ❌ `/sdd-continue` (does not exist - use `/sdd-artefact` instead)
