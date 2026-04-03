---
name: sdd-artefact
description: Create the next artifact in a spec
---

Create the next artifact in my current spec development process.

## Artifact Creation Workflow

```mermaid
flowchart TD
    Start([/sdd-artefact]) --> Detect{Detect State}
    
    Detect --> CheckProposal{proposal.md<br/>exists?}
    CheckProposal -->|No| BlockProposal[BLOCKED<br/>Create proposal first<br/>/sdd-propose name]
    
    CheckProposal -->|Yes| CheckSpecs{specs/*.md<br/>exists?}
    CheckSpecs -->|No| BlockSpecs[BLOCKED<br/>Create specs first<br/>/sdd-artefact]
    
    CheckSpecs -->|Yes| CheckDesign{design.md<br/>exists?}
    CheckDesign -->|No| CreateDesign[Create design<br/>REQUIRES: specs/]
    
    CheckDesign -->|Yes| CheckTasks{tasks.md<br/>exists?}
    CheckTasks -->|No| CreateTasks[Create tasks<br/>REQUIRES: specs/ + design.md]
    
    CheckTasks -->|Yes| AllDone[All artifacts complete<br/>Ready for /sdd-apply]
    
    style BlockProposal fill:#ff6b6b,stroke:#333,stroke-width:2px
    style BlockSpecs fill:#ffd93d,stroke:#333,stroke-width:2px
    style CreateDesign fill:#6bcf7f,stroke:#333
    style CreateTasks fill:#4d96ff,stroke:#333
    style AllDone fill:#95e1d3,stroke:#333,stroke-width:2px
```

**Strict Sequential Order:**
1. `proposal.md` (root)
2. `specs/*.md` (BLOCKED until proposal exists)
3. `design.md` (BLOCKED until specs exist)
4. `tasks.md` (BLOCKED until specs + design exist)

Follow the sdd-spec-artefact skill:

1. **Detect which change to continue** - Scan `.specs/changes/`, if multiple ask me to choose
2. **Check artifact status** - DONE, READY, BLOCKED (with BLOCKED messages)
3. **Enforce sequential order** - specs MUST exist before design can be created
   - If design requested but specs missing: Output **"BLOCKED: Create specs first (required)"**
   - Never skip or create out of order
4. **Select the next ready artifact** - specs → design → tasks (sequential only)
5. **Read dependencies** - Load context from completed artifacts
   - For design: specs/**/*.md is REQUIRED input
   - For specs modifying existing: Read `.specs/specs/<module>/<capability>/spec.md`
6. **Create ONE artifact** with proper structure
   - Verify dependencies exist before creating
   - For NEW capabilities: Use ADDED format
   - For MODIFIED capabilities: Use delta format (MODIFIED/ADDED/REMOVED)
7. **Update proposal status**
8. **Report what was created and what's next**

**BLOCKED Message Templates:**

```
⚠️  BLOCKED: Cannot create [artifact]

REASON: [dependency] does not exist
REQUIRED: [explanation]

ACTION REQUIRED:
  [correct command]

Current Status:
  proposal: [status]
  specs: [status]
  design: [status]
```

Show me:
- What artifact you're creating
- Why it's next (dependency status)
- A preview before writing
- Any BLOCKED states with clear next steps

## Design Document Creation

**PREREQUISITE:** `specs/**/*.md` MUST exist before creating design.md

When creating design.md, follow this enhanced workflow:

### Step 0: Verify Prerequisites (BLOCKS if missing)

Before gathering context, verify specs exist:
- Check `specs/` directory has ≥1 `.md` file
- If missing: **HALT** and output BLOCKED message
- If empty: **HALT** - specs must be created first

**BLOCKED Output:**
```
⚠️  BLOCKED: Cannot create design.md

REASON: No specs/*.md files found
REQUIRED: Specifications MUST exist before design
           (design agent requires specs as input)

ACTION REQUIRED:
  /sdd-artefact     - Create specs first (required)
  
Current Status:
  proposal: DONE
  specs: BLOCKED (missing)
  design: BLOCKED (needs specs)
```

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
6. Run a **mandatory 5-iteration refinement loop** with `sdd-design-analyst`
   - Write initial draft first
   - Run iterations 1 through 5 (no skipping)
   - Save every critique report (`review-iteration-1.md` ... `review-iteration-5.md`)
   - Revise design.md after each iteration
   - Continue all 5 iterations even if earlier iterations approve
   - Reclassify any MINOR issue that impacts security/compliance/data integrity/requirement coverage to MAJOR or CRITICAL
   - Reach final quality gate in iteration 5 before completion

### Step 4b: Verify Design Document

After agent completion:
- Verify design.md exists with substantive content
- Check all sections are populated
- Confirm Mermaid diagrams are present
- Verify prior context was incorporated
- Verify review artifacts exist:
  - `review-iteration-1.md`
  - `review-iteration-2.md`
  - `review-iteration-3.md`
  - `review-iteration-4.md`
  - `review-iteration-5.md`
- Verify iteration 5 verdict is APPROVE
- Verify 0 critical and 0 major unresolved issues in final design
- Minor findings SHOULD be fixed during refinement iterations
- Verify unresolved minor findings (if any) are documented in design.md with rationale and follow-up
- Verify all requirements from `specs/**/*.md` are covered in design.md

**BLOCKED Output (if review loop incomplete):**
```
⚠️  BLOCKED: Cannot mark design as complete

REASON: Mandatory 5-iteration refinement loop not fully completed

REQUIRED:
  - review-iteration-1.md ... review-iteration-5.md must exist
  - iteration 5 verdict must be APPROVE
  - final design must have 0 critical and 0 major unresolved issues
  - unresolved minor findings (if any) must be documented with rationale and follow-up

ACTION REQUIRED:
  /sdd-artefact     - Re-run design phase refinement

Current Status:
  design: BLOCKED (refinement loop incomplete)
```

---

## Task Document Creation

**PREREQUISITE:** `specs/**/*.md` AND `design.md` MUST exist before creating tasks.md

When creating tasks.md, follow this enhanced workflow:

### Step 0: Verify Prerequisites (BLOCKS if missing)

Before gathering context, verify specs and design exist:
- Check `specs/` directory has ≥1 `.md` file
- Check `design.md` exists with substantive content
- If either missing: **HALT** and output BLOCKED message

**BLOCKED Output:**
```
⚠️  BLOCKED: Cannot create tasks.md

REASON: <specs/design.md> not found
REQUIRED: Specifications AND design MUST exist before tasks
          (task agent requires specs and design as input)

ACTION REQUIRED:
  /sdd-artefact     - Create missing artifacts first

Current Status:
  proposal: DONE
  specs: <status>
  design: <status>
  tasks: BLOCKED (needs specs + design)
```

### Step 1: Gather Prior Context

Before launching the task agent, collect context from earlier phases:

**From proposal.md:**
- Parse scope and goals
- Extract constraints
- Note priority indicators

**From specs/**/*.md:**
- Extract all requirements with IDs
- Note priority markers
- Identify dependencies between requirements

**From design.md:**
- Extract components and interfaces
- Extract decisions and implementation implications
- Extract data models and schema changes
- Extract API changes
- Extract migration plan
- Extract testing strategy

### Step 2: Launch Task Agent

**Agent:** `sdd-task`

Launch the task agent with:
- File paths to read (proposal.md, specs/**/*.md, design.md)
- Expected output location (.specs/changes/<name>/tasks.md)

The task agent will:
1. Read all source documents
2. Analyze codebase (detect structure, existing files, patterns)
3. Generate tasks with proper grouping, sizing, and sequencing
4. Write initial tasks.md to disk
5. Run a **mandatory 3-iteration refinement loop** with `sdd-task-analyst`
   - Write initial draft first
   - Run iterations 1 through 3 (no skipping)
   - Save every critique report (`task-review-iteration-1.md` ... `task-review-iteration-3.md`)
   - Revise tasks.md after each iteration
   - Continue all 3 iterations even if earlier iterations approve
   - Reclassify any MINOR issue that impacts implementation correctness or parallel safety to MAJOR or CRITICAL
   - Reach final quality gate in iteration 3 before completion

### Step 3: Verify Tasks Document

After agent completion:
- Verify tasks.md exists with substantive content
- Check all groups have _Meta fields
- Verify requirement coverage (100%)
- Verify design element coverage (100%)
- Verify dependency graph is valid (no cycles)
- Verify review artifacts exist:
  - `task-review-iteration-1.md`
  - `task-review-iteration-2.md`
  - `task-review-iteration-3.md`
- Verify iteration 3 verdict is APPROVE
- Verify 0 critical and 0 major unresolved issues in final tasks
- Minor findings SHOULD be fixed during refinement iterations
- Verify unresolved minor findings (if any) are documented in tasks.md with rationale and follow-up
- Verify Task Iteration History section exists in tasks.md

**BLOCKED Output (if review loop incomplete):**
```
⚠️  BLOCKED: Cannot mark tasks as complete

REASON: Mandatory 3-iteration refinement loop not fully completed

REQUIRED:
  - task-review-iteration-1.md ... task-review-iteration-3.md must exist
  - iteration 3 verdict must be APPROVE
  - final tasks must have 0 critical and 0 major unresolved issues
  - unresolved minor findings (if any) must be documented with rationale and follow-up
  - Task Iteration History must be present in tasks.md

ACTION REQUIRED:
  /sdd-artefact     - Re-run task phase refinement

Current Status:
  tasks: BLOCKED (refinement loop incomplete)
```

### Step 5: Update Proposal Status

**NOTE:** This step runs AFTER the design agent returns (which is AFTER the mandatory 5-iteration review loop completes). The agent does not update proposal status - this command handles it.

Update the Status section in `proposal.md`:

```markdown
## Status
- [x] Requirements: done (specs/ created)
- [x] Design: done (design.md created, 5 review iterations)
- [ ] Tasks: pending
```

**After design creation:**
```markdown
## Status
- [x] Requirements: done (specs/ created)
- [x] Design: done (design.md created, 5 review iterations)
- [x] Tasks: done (tasks.md created, 3 review iterations)
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

**Sequential Workflow - No Skipping:**

**After creating proposal:**
- `/sdd-artefact` - Create specs (REQUIRED before design)

**After creating specs:**
- `/sdd-artefact` - Create design document (specs now REQUIRED dependency)

**If design requested without specs:**
```
⚠️  BLOCKED: Create specs first
```

**After creating design:**
- `/sdd-artefact` - Create tasks document (requires both specs + design)

**After creating tasks:**
- `/sdd-apply` - Execute one task at a time
- `/sdd-apply-group N` - Execute all tasks in group N
- `/sdd-apply-all` - Execute all remaining groups
- `/sdd-status` - Check current progress

**Do NOT use these as commands (they are skills/agents):**
- ❌ `/sdd-requirements` (skill, loaded by this command)
- ❌ `/sdd-design` (agent, invoked by this command)
- ❌ `/sdd-tasks` (skill, loaded by this command)
- ❌ `/sdd-task-review` (skill, loaded by task agent)

**Loads skills:** `sdd-spec-artefact`, `sdd-requirements`, `sdd-design`, `sdd-tasks`, `sdd-task-review`
**Loads agents:** `sdd-design` (for design phase), `sdd-task` (for task phase)
