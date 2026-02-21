---
name: sdd-status
description: Show the status of a spec
---

Analyze and report the status of my spec.

Check:

1. **Which changes exist** in `.specs/changes/`
2. **For each change, check artifact status:**
   - **DONE**: File exists with substantive content
   - **READY**: Dependencies complete, can be created
   - **BLOCKED**: Missing dependencies
   - **PENDING**: Placeholder only
3. **Task completion** - Count `- [x]` vs `- [ ]` in tasks.md

Show me a visual status report:

```
Change: <name>

Artifacts:
  proposal: ✓ DONE
  specs:    ✓ DONE
  design:   ✓ DONE
  tasks:    ✓ DONE

Implementation:
  Tasks: 8/12 complete (67%)
  
  Completed:
    ✓ 1.1 Setup module
    ✓ 1.2 Add dependencies
    ...
    
  Remaining:
    ○ 3.1 Create API endpoint
    ○ 3.2 Add validation
    ...

Status: Ready for /sdd-apply
```

If multiple changes, show summary for all.

Also show:
- Existing specs in `.specs/specs/` (accumulated)
- Recent archives in `.specs/archive/`
