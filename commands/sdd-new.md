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

After creating, remind me:
- Review the proposal
- Use `/sdd-artefact` to develop the spec
- Use `/sdd-status` to check progress
