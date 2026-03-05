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

## Design Document Creation

When creating design.md, follow this enhanced workflow:

### Step 1: Gather Prior Context

Before launching the design agent, collect context from earlier phases:

**From proposal.md:**
- Parse `_Context Log` section if present
- Extract Q&A responses and user preferences
- Note constraints mentioned during proposal creation
- Capture any remarks or decisions made

**From specs/**/*.md:**
- Extract requirements with IDs
- Note priority markers (critical/high/medium/low)
- Identify dependencies between requirements
- Mark design hints or implementation preferences

**From interactive session:**
- User clarifications from any Q&A
- Design preferences expressed
- Constraint refinements

### Step 2: Build Context Package

Compile the gathered context into a structured package:

```
Context Package:
- User Preferences: [list of preferences]
- Constraints: [technical/business/external]
- Prior Decisions: [decisions from proposal/specs]
- Priority Requirements: [critical/high items]
- Similar Features: [existing code to reference]
- Q&A Responses: [relevant answers]
```

### Step 3: Launch Design Agent

**Agent:** `sdd-design`

Launch the design agent with:
- Context package (from Step 2)
- File paths to read (proposal.md, specs/**/*.md)
- Expected output location (.specs/changes/<name>/design.md)

The design agent will:
1. Read all source documents
2. Analyze codebase (detect tech stack, patterns, conventions)
3. Find similar existing features
4. Generate design.md with:
   - Problem Statement
   - Context (with detected constraints)
   - Goals / Non-Goals
   - Existing Solution (if modification)
   - Architecture (with Mermaid diagrams)
   - Decisions (with alternatives)
   - Components
   - Data Models
   - API Changes
   - Testability, Monitoring & Alerting
   - Risks / Trade-offs
   - Migration Plan
   - Open Questions
5. Respect prior context (decisions, preferences, constraints)

### Step 4: Verify Design Document

After agent completion:
- Verify design.md exists with substantive content
- Check all sections are populated
- Confirm Mermaid diagrams are present
- Verify prior context was incorporated

### Step 5: Update Proposal Status

**NOTE:** This step runs AFTER the design agent returns (which is AFTER the 3-iteration review loop completes). The agent does not update proposal status - this command handles it.

Update the Status section in `proposal.md`:

```markdown
## Status
- [x] Requirements: done (specs/ created)
- [x] Design: done (design.md created)
- [ ] Tasks: pending
```

After creating, report status. Then output ONLY the relevant next step based on what was just completed:

**If specs were just created:**
```
---
Next: Use /sdd-artefact to create design
---
```

**If design was just created:**
```
---
Next: Use /sdd-artefact to create tasks
---
```

**If tasks were just created:**
```
---
Next Steps:
  /sdd-apply        - Execute one task at a time
  /sdd-apply-group N - Execute group N
  /sdd-apply-all     - Execute all groups
---
```

DO NOT show options that don't apply to the current state.
DO NOT suggest commands not listed above.

---

## Valid Next Commands

**After creating specs:**
- `/sdd-artefact` - Create design document

**After creating design:**
- `/sdd-artefact` - Create tasks document

**After creating tasks:**
- `/sdd-apply` - Execute one task at a time
- `/sdd-apply-group N` - Execute all tasks in group N
- `/sdd-apply-all` - Execute all remaining groups
- `/sdd-status` - Check current progress

**Do NOT use these as commands (they are skills/agents):**
- ❌ `/sdd-requirements` (skill, loaded by this command)
- ❌ `/sdd-design` (agent, invoked by this command)
- ❌ `/sdd-tasks` (skill, loaded by this command)

**Loads skills:** `sdd-spec-artefact`, `sdd-requirements`, `sdd-design`, `sdd-tasks`
**Loads agents:** `sdd-design-agent` (for design phase)
