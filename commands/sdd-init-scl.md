---
name: sdd-init-scl
description: Initialize project with SCL-enhanced SDD agent behavior rules in AGENTS.md
---

Initialize this project with SCL-enhanced SDD (Structured Cognitive Loop + Spec-Driven Development) workflow rules.

**Process:**

1. **Check for AGENTS.md** in project root:
   - If exists: Read current content
   - If not exists: Create new file with header

2. **Add SCL-Enhanced SDD Agent Behavior Rules section** if not already present:

   ```markdown
   ## Agent Behavior Rules (SCL-Enhanced SDD Workflow)
   
   This project uses SCL-enhanced Spec-Driven Development. Agents **MUST** follow these rules:
   
   ### SCL Core Principles (RFC2119)
   
   1. The system **MUST** maintain memory state in `.memory/` directory
   2. Every requirement **MUST** cite its source
   3. Every decision **MUST** document alternatives considered
   4. Every task **MUST** reference at least one requirement
   5. The system **MUST** validate before executing actions
   
   ### Before Starting Work
   
   1. **Check for existing specs** - Look in `.specs/specs/` for related capabilities
   2. **Check for memory state** - Look in `.specs/changes/<active>/.memory/` for context
   3. **Ask about scope** - New capability or modifying existing?
   4. **Choose workflow**:
      - SCL-enhanced (use `/sdd-artefact-scl`) for features > 1 day
      - Standard SDD for changes < 1 day
      - Skip SDD only for trivial changes
   
   ### During Spec Creation
   
   - **Use EARS format** with RFC2119 keywords:
     ```
     WHEN <event> THEN system SHALL **MUST** <response>
     IF <condition> THEN system **SHALL** <response>
     ```
   - **Cite sources** for every requirement in memory
   - **Document alternatives** for every design decision
   - **Reference existing specs** when modifying
   
   ### During Implementation
   
   - **Load memory context** before starting tasks
   - **One task group at a time** via `/sdd-apply-group-scl`
   - **Reference requirements with citations** in code:
     ```typescript
     // Implements: REQ-001 (per specs/auth/spec.md#L42)
     // Evidence: design.md#decision-password-hashing
     ```
   - **Update memory** after completing tasks
   - **Validate scope** before modifying files
   
   ### Subagent Dispatch Rules
   
   When dispatching subagents for task groups:
   
   1. **Inject memory context** - Decisions, requirements, prior outcomes
   2. **Set explicit constraints**:
      - Allowed files: `src/auth/**/*`
      - Blocked files: `src/core/*`
   3. **Require citations** - Every file MUST reference source spec
   4. **Completion signal** - Must output "GROUP N COMPLETE"
   
   ### Before Archiving
   
   - **Run SCL verification** with `/sdd-verify-scl`
   - **Check memory integrity** - All requirements traced
   - **Validate citations** - Every claim has source
   - **Document outcomes** in memory episodes
   
   ### Memory Structure
   
   ```
   .specs/changes/<change-name>/.memory/
   ├── decisions.json      # Decisions with evidence
   ├── requirements.json   # Requirement index
   ├── citations.json      # Citation graph
   ├── control-log.json    # Validation checkpoints
   └── episodes.json       # Cycle-by-cycle history
   ```
   
   ### Available Commands
   
   ```
   # Initialization
   /sdd-explore          # Explore idea, create context-log
   /sdd-propose          # Create proposal from context-log
   /sdd-init-memory      # Initialize SCL memory
   
   # Artifact Creation
   /sdd-artefact-scl     # Create artifacts with memory tracking
   
   # Implementation
   /sdd-apply-group-scl  # Execute group with memory context
   /sdd-apply-all-scl    # Execute all groups
   
   # Verification & Status
   /sdd-verify-scl       # Verify with memory tracing
   /sdd-memory-status    # Inspect memory state
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
✓ Initialized SCL-enhanced SDD workflow in this project

Updated: AGENTS.md
  + Added "Agent Behavior Rules (SCL-Enhanced SDD Workflow)" section

Created directories:
  .specs/specs/
  .specs/changes/
  .specs/archive/

---

## Valid Next Commands

**After initializing project:**
- `/sdd-reverse src/` - Extract specs from existing code (brownfield)
- `/sdd-explore` - Start exploring a new change (greenfield)

**Do NOT suggest:**
- ❌ `/sdd-artefact-scl` (no change exists yet)
- ❌ `/sdd-apply-group-scl` (no change exists yet)

