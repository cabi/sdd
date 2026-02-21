---
name: sdd-archive
description: Archive a completed spec, merging deltas into main specs
---

Archive my completed spec by merging deltas into the accumulated specs.

Follow the sdd-spec-archive skill:

1. **Verify completion** - Check all tasks are done
2. **Analyze delta operations** - Identify ADDED/MODIFIED/REMOVED for each capability
3. **Show merge plan:**
   ```
   Will merge:
   - authentication: MODIFIED → specs/auth/authentication/spec.md
   - sso: ADDED → (new) specs/auth/sso/spec.md
   ```
4. **Ask to confirm** - Proceed with merge?
5. **Merge deltas into main specs:**
   - ADDED → Create new spec files in `.specs/specs/`
   - MODIFIED → Update existing specs
   - REMOVED → Remove from existing specs
6. **Create SUMMARY.md** with what was delivered
7. **Move to archive** - `.specs/changes/<name>/` → `.specs/archive/YYYY-MM-DD-<name>/`
8. **Report completion**

Key principle: `.specs/specs/` becomes the single source of truth after merge.
