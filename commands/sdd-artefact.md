---
name: sdd-artefact
description: Create the next artifact in a spec
---

Create the next artifact in my current spec development process.

Follow the sdd-spec-artefact skill:

1. **Detect which change to continue** - Scan `.specs/changes/`, if multiple ask me to choose
2. **Check artifact status** - DONE, READY, BLOCKED
3. **Select the next ready artifact** - specs > design > tasks
4. **Read dependencies** - Load context from completed artifacts
   - For specs modifying existing: Read `.specs/specs/<module>/<capability>/spec.md`
5. **Create ONE artifact** with proper structure
   - For NEW capabilities: Use ADDED format
   - For MODIFIED capabilities: Use delta format (MODIFIED/ADDED/REMOVED)
6. **Update proposal status**
7. **Report what was created and what's next**

Show me:
- What artifact you're creating
- Why it's next (dependency status)
- A preview before writing

After creating, report status and output EXACTLY this Next Steps section:

---
## Next Steps

- If specs created: Use `/sdd-artefact` to create design
- If design created: Use `/sdd-artefact` to create tasks
- If tasks created:
  - `/sdd-apply` - Execute one task at a time
  - `/sdd-apply-group N` - Execute group N
  - `/sdd-apply-all` - Execute all groups
- Use `/sdd-status` to check progress at any time
---

DO NOT suggest commands not listed above.
