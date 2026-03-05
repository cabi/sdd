---
name: sdd-init-memory
description: Initialize SCL memory structure and harvest knowledge from proposal
---

Initialize the SCL memory structure and harvest knowledge from the proposal.

**Usage:** `/sdd-init-memory [change-name]`

**Process:**

1. **Determine target**:
   - If change-name provided: Use it
   - If not: Use current active change or ask

2. **Check for proposal**:
   - Read `.specs/changes/<change-name>/proposal.md`
   - If not found: ERROR "Run /sdd-propose first"

3. **Check if memory exists**:
   - If `.memory/` exists: This is a re-harvest (see Re-harvesting below)
   - If not: Initial creation

4. **Create memory structure** (if not exists):
   ```
   .specs/changes/<change-name>/.memory/
   ├── decisions.json
   ├── requirements.json
   ├── citations.json
   ├── control-log.json
   └── episodes.json
   ```

5. **Initialize empty files** (if not exists):
   ```json
   {
     "version": "1.0",
     "change_name": "<change-name>",
     "created_at": "<timestamp>",
     "decisions": []
   }
   ```

6. **Create regulation.md** from template (skip if exists):
   - READ `skills/sdd-memory/templates/regulation.md`
   - REPLACE `{{CHANGE_NAME}}` with the actual change name
   - REPLACE `{{TIMESTAMP}}` with current ISO 8601 timestamp
   - WRITE to `.specs/changes/<change-name>/regulation.md`

7. **Harvest from proposal**:

   **7a. Extract Goals → requirements.json**
   - Read "## Goals" section from proposal.md
   - For each goal bullet:
     - MEM.write({
         type: "requirement",
         data: {
           id: "REQ-FUNC-NNN",
           type: "functional",
           title: <goal text>,
           description: <full description>,
           source: "proposal.md#L<line>",
           status: "pending",
           created_at: <timestamp>
         }
       })

   **7b. Extract Constraints → requirements.json**
   - Read "## Constraints" section from proposal.md
   - For each constraint (categorized by subsection):
     - MEM.write({
         type: "requirement",
         data: {
           id: "REQ-CONST-NNN",
           type: "constraint",
           title: <constraint text>,
           description: <full description with reason>,
           source: "proposal.md#L<line>",
           status: "pending",
           created_at: <timestamp>
         }
       })

   **7c. Extract Context Log → episodes.json**
   - Read "## Context Log" section from proposal.md
   - Parse Q&A pairs
   - For each Q&A:
     - MEM.write({
         type: "episode",
         data: {
           cycle: N,
           phase: "exploration",
           timestamp: <now>,
           observations: {
             files_read: ["proposal.md"],
             questions: [<question>],
             answers: [<answer>],
             insights: [<insight if present>]
           }
         }
       })

   **7d. Extract Exploration Notes → episodes.json**
   - Read "## Exploration Notes" section from proposal.md
   - For each option/risk/knowledge item:
     - MEM.write({
         type: "episode",
         data: {
           cycle: N,
           phase: "exploration",
           judgments: [{
             proposition: <option/risk description>,
             evidence: <reasoning/impact>,
             confidence: "medium"
           }]
         }
       })

8. **Report harvested content**

**Re-harvesting:**

If `/sdd-init-memory` is run when `.memory/` already exists:

1. Read current memory state
2. Re-parse proposal.md
3. For requirements:
   - Match by source location (proposal.md#L<N>)
   - Update existing if source matches
   - Add new if not found
   - Remove stale (in proposal before, not now)
4. For episodes:
   - Add new episodes from updated Context Log
   - Preserve existing exploration episodes
5. PRESERVE decisions.json (design phase owns this)
6. Log re-harvest in control-log.json:
   ```json
   {
     "id": "CHK-NNN",
     "timestamp": "<ISO 8601>",
     "phase": "re-harvest",
     "checks": [
       {
         "name": "proposal_reparse",
         "status": "pass",
         "message": "Updated requirements and episodes from proposal changes"
       }
     ],
     "overall": "pass",
     "blocked": false
   }
   ```

**Output (initial creation):**
```
✓ Initialized memory for: <change-name>

Created:
  .specs/changes/<change-name>/.memory/
  ├── decisions.json      (0 decisions - ready for design phase)
  ├── requirements.json   (X harvested)
  ├── citations.json      (0 citations)
  ├── control-log.json    (0 checkpoints)
  └── episodes.json       (Y exploration episodes)

Created from template:
  .specs/changes/<change-name>/regulation.md

Harvested from proposal.md:
  Goals → X functional requirements (REQ-FUNC-001, ...)
  Constraints → Y constraint requirements (REQ-CONST-001, ...)
  Context Log → Z exploration episodes
  Exploration Notes → N episodes with options/risks

Memory is ready for SCL-enhanced artifact creation.
The design agent will receive full context from exploration.
Use /sdd-artefact-scl to create artifacts with memory tracking.
```

**Output (re-harvest):**
```
✓ Re-harvested memory for: <change-name>

Updated:
  requirements.json   (+X new, Y updated, Z removed)
  episodes.json       (+N new exploration episodes)

Preserved:
  decisions.json      (M decisions from design phase)

Memory synced with latest proposal changes.
```

**Semantic Extraction:**

If proposal sections are not perfectly structured, use pattern matching:

| Pattern | Type | Example |
|---------|------|---------|
| "MUST/MUST NOT/SHALL" | constraint | "System MUST use HTTPS" |
| "Goal/Objective/Success" | functional | "Goal: Enable user login" |
| "Q:/A:" or "Question/Answer" | exploration episode | Q&A pairs |
| "Considered/Option/Tried" | options_not_decided | "Considered using Redis" |

---

## Valid Next Commands

**After memory initialization:**
- `/sdd-artefact-scl` - Create requirements (specs) with memory tracking
- `/sdd-memory-status` - View harvested knowledge

**Do NOT use these as commands (they are skills):**
- ❌ `/sdd-memory` (skill, loaded by this command)
- ❌ `/sdd-requirements` (skill, loaded by /sdd-artefact-scl)

**Loads skills:** `sdd-memory`
