---
name: sdd-propose
description: Create formal proposal from exploration context
---

Create the formal proposal document from the exploration context.

**Usage:** `/sdd-propose <change-name>`

**Process:**

1. **Check for context-log**:
   - Read `.specs/changes/<change-name>/context-log.md`
   - If not found: ERROR - see error handling below

2. **Check for existing specs**:
   - Scan `.specs/specs/` for related capabilities
   - If found: Ask if this modifies existing or is new

 3. **Extract from context-log**:
    - Goals → Goals section
    - Constraints → Constraints section
    - Q&A → Context Log section
    - Scope → Scope section
    - Options → Exploration Notes section
    - Change Type → Change Type section
    - Breaking + Migration Required → Change Type section
    - Removed capabilities (if any) → Removed Capabilities section

5. **Extract change type from context-log**:
    - Read `Change Type:` from context-log.md
    - Read `Breaking:` from context-log.md
    - Read `Migration Required:` from context-log.md
    - If not set (e.g. older context-log): derive from "What Changes" content
      - If only "New Capabilities" → `addition`
      - If "Modified Capabilities" present → `modification`
      - Default: `modification`

6. **Create proposal.md** with structured sections:

   ```markdown
   # Proposal: <change-name>
   
   ## Context Log
   <!-- Transferred from context-log.md -->
   <Full Q&A history from exploration>
   
   ## Goals
   <!-- What we're trying to achieve -->
   - Goal 1: <from context-log>
   - Goal 2: <from context-log>
   
   ## Constraints
   <!-- What limits our design choices -->
   
   ### Technical Constraints
   - <from context-log>
   
   ### Business Constraints
   - <from context-log>
   
   ### External Constraints
   - <from context-log>
   
    ## What Changes
    <!-- Capabilities being added/modified/removed -->
    <Derived from goals>

    ## Change Type

    **Type:** <addition|modification|refactor|removal|rebuild>
    **Breaking:** <yes|no>
    **Migration Required:** <yes|no>
    **Migration Path:** <description or "N/A">

    ## Capabilities
   
   ### New Capabilities
   - `<name>`: <description>
   
    ### Modified Capabilities
    - `<name>`: <what's changing>
      - Existing spec: <path>

    ### Removed Capabilities
    <!-- Only for change_type: removal or rebuild -->
    - `<name>`: <what's being removed>
      - Existing spec: <path>
      - Migration: <how consumers should adapt>
   
   ## Impact
   <!-- Affected systems, APIs, dependencies -->
   
   ## Scope
   
   ### In Scope
   <from context-log>
   
   ### Out of Scope
   <from context-log>
   
   ## Exploration Notes
   <!-- Options considered, domain knowledge, risks -->
   <from context-log>
   
   ## Status
   - [ ] Requirements: pending
   - [ ] Design: pending
   - [ ] Tasks: pending
   
   ---
   Created: <date>
   Source: context-log.md
   ```

5. **Initialize artifact placeholders**:
   - `specs/.gitkeep` - Delta specs will be created here
   - `design.md` - Template with placeholder
   - `tasks.md` - Template with placeholder

6. **Report completion**

**Error Handling:**

If `context-log.md` not found:
```
✗ No exploration context found for: <change-name>

Required: Run /sdd-explore first to create context-log.md

Options:
  /sdd-explore <change-name>   # Create exploration context for this change
  /sdd-explore                 # Start fresh exploration
```

**Output:**
```
✓ Created proposal for: <change-name>

Created:
  .specs/changes/<change-name>/proposal.md
  .specs/changes/<change-name>/specs/.gitkeep
  .specs/changes/<change-name>/design.md (placeholder)
  .specs/changes/<change-name>/tasks.md (placeholder)

Transferred from context-log.md:
  - X goals → Goals section
  - Y constraints → Constraints section
  - Z Q&A pairs → Context Log section
  - N options → Exploration Notes

Context log preserved at: context-log.md
```

---

## Valid Next Commands

**After creating a proposal:**
- `/sdd-artefact` - Create requirements (specs) from proposal
- `/sdd-status` - Check current progress

**Do NOT use these as commands (they are skills/agents):**
- ❌ `/sdd-requirements` (skill, loaded by /sdd-artefact)
- ❌ `/sdd-design` (agent, invoked by /sdd-artefact)

**Recommended path:**
```
/sdd-artefact
```

**Loads skills:** `sdd-spec-create`
