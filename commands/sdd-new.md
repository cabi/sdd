---
name: sdd-new
description: Start a new spec-driven development specification
---

Create a new SDD spec for the feature or change I want to implement.

Follow the sdd-spec-create skill:

1. **Ask me what I want to build** - Gather WHY, WHAT, and scope
2. **Check for existing specs** - Look in `.specs/specs/` for related capabilities
3. **If related specs found** - Ask if this modifies existing or is completely new
4. **Create the change directory** at `.specs/changes/<change-name>/`
5. **Create proposal.md** - Reference existing specs if modifying
6. **Initialize artifact placeholders** (specs/, design.md, tasks.md)

After creating, output EXACTLY this Next Steps section:

---
## Next Steps

1. Review `.specs/changes/<change-name>/proposal.md` - confirm scope
2. Choose workflow:
   - **Standard**: `/sdd-artefact` → `/sdd-apply` → `/sdd-verify` → `/sdd-archive`
   - **SCL-enhanced**: `/sdd-init-memory` → `/sdd-artefact-scl` → `/sdd-apply-group-scl` → `/sdd-verify-scl`
3. Use `/sdd-status` to check progress at any time
---

DO NOT suggest commands not listed above.
