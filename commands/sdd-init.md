---
name: sdd-init
description: Initialize project with SDD agent behavior rules in AGENTS.md
---

Initialize this project with Standard SDD (Spec-Driven Development) workflow rules.

**Process:**

1. **Check for AGENTS.md** in project root:
   - If exists: Read current content
   - If not exists: Create new file with header

2. **Add SDD Agent Behavior Rules section** if not already present:

   ```markdown
   ## Agent Behavior Rules (SDD Workflow)
   
   This project uses Spec-Driven Development (SDD). Agents **MUST** follow these rules:
   
   ### Before Starting Work
   
   1. **Check for existing specs** - Look in `.specs/specs/` for related capabilities before proposing solutions
   2. **Ask about scope** - Determine if this is a new capability or modifying existing
   3. **Choose appropriate workflow**:
       - Full spec (use `/sdd-explore` + `/sdd-propose`) for features > 1 day
      - Micro-spec for changes < 1 day
      - Skip SDD for trivial changes only
   
   ### During Spec Creation
   
   - **Use EARS format** for requirements:
     ```
     WHEN <event> THEN system SHALL <response>
     IF <condition> THEN system SHALL <response>
     ```
   - **Reference existing specs** when modifying capabilities
   - **Create delta specs** with ADDED/MODIFIED/REMOVED sections
   
   ### During Implementation
   
   - **One task at a time** using `/sdd-apply` by default
   - **Reference requirements** in code comments
   - **Update specs if gaps found** - DO NOT work around spec issues
   - **Mark tasks complete** immediately after finishing
   
   ### Before Archiving
   
   - **Run verification** with `/sdd-verify`
   - **Check all tasks** complete or intentionally skipped
   - **Create summary** documenting deliverables
   
   ### Available Commands
   
   ```
    /sdd-explore    # Explore idea, create context-log
    /sdd-propose    # Create proposal from context-log
    /sdd-artefact   # Develop spec incrementally
   /sdd-ff          # Fast-forward all artifacts
   /sdd-status      # Check progress
   /sdd-apply       # Execute one task
   /sdd-apply-group # Execute task group
   /sdd-verify      # Verify implementation
   /sdd-archive     # Complete and archive
   ```
   ```

3. **Ensure .specs directory structure**:
   ```
   mkdir -p .specs/specs .specs/changes .specs/archive
   ```

4. **Report what was done**:
   - Sections added/updated in AGENTS.md
   - Directories created
   - Next steps for the user

**Output example:**
```
✓ Initialized SDD workflow in this project

Updated: AGENTS.md
  + Added "Agent Behavior Rules (SDD Workflow)" section

Created directories:
  .specs/specs/
  .specs/changes/
  .specs/archive/

Next steps:
  - Review AGENTS.md for the new workflow rules
  - Use /sdd-explore to explore your first feature
  - Use /sdd-propose to create your first specification
```
