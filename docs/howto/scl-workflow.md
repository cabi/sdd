# SCL-Enhanced Workflow

How to use the SCL-enhanced workflow for reliable multi-agent task execution.

## When to Use

Use the SCL-enhanced workflow for:

- Complex features requiring high reliability
- Multi-agent task execution (parallel groups)
- Projects requiring full traceability
- When subagent context isolation is a problem

---

## What is SCL?

SCL (Structured Cognitive Loop) addresses a fundamental limitation: **subagents operate in isolated contexts** and cannot access decisions made in prior groups.

### The Problem

When using `/sdd-apply-all`, subagents:
- Cannot access decisions made in prior groups
- Don't know what files were created previously
- Lack reasoning behind design choices

### The Solution

SCL provides:
1. **Memory Persistence** - External `.memory/` directory stores state across cycles
2. **Evidential Grounding** - Every claim MUST cite a source (RFC2119)
3. **Normative Control** - Control module validates before execution
4. **Scope Enforcement** - Subagents constrained to allowed files

---

## Workflow Overview

```
EXPLORE → PROPOSE → INIT MEMORY → DEVELOP → IMPLEMENT → VERIFY → ARCHIVE
                       (with knowledge harvesting)
```

| Phase | Commands | Output |
|-------|----------|--------|
| Explore | `/sdd-explore` | context-log.md |
| Propose | `/sdd-propose` | proposal.md with Context Log |
| Init Memory | `/sdd-init-memory` | .memory/ + harvested knowledge |
| Develop | `/sdd-artefact-scl` | specs/, design.md, tasks.md + memory |
| Implement | `/sdd-apply-group-scl`, `/sdd-apply-all-scl` | Code + memory updates |
| Verify | `/sdd-verify-scl` | Verification with memory tracing |
| Archive | `/sdd-archive` | SUMMARY.md + preserved memory |

---

## Phase 1: Explore

Think through the idea and capture exploration context:

```
/sdd-explore [name]
```

Creates `context-log.md` with:
- **Clarifying Questions** - Q&A from exploration interview
- **Goals Identified** - What we're trying to achieve
- **Constraints Discovered** - What limits our choices
- **Scope Boundaries** - In scope / out of scope
- **Options Considered** - Alternative approaches discussed

---

## Phase 2: Propose

Create the formal proposal from exploration context:

```
/sdd-propose <name>
```

Creates `proposal.md` with:
- **Context Log** - Full Q&A history (MANDATORY section)
- **Goals** - What we're trying to achieve (not HOW)
- **Constraints** - Technical, business, external limitations
- **Exploration Notes** - Options, risks, domain knowledge

---

## Phase 3: Initialize SCL Memory

After creating the proposal, initialize memory (with automatic harvesting):

```
/sdd-init-memory <change-name>
```

This creates:

```
.specs/changes/<change-name>/
├── .memory/
│   ├── decisions.json          # All decisions with evidence
│   ├── requirements.json       # Requirement index
│   ├── citations.json          # Citation graph
│   ├── control-log.json        # Validation checkpoints
│   └── episodes.json           # Cycle-by-cycle history
└── regulation.md               # Epistemic Constitution
```

### Regulation.md

Every SCL-enhanced change includes a `regulation.md` defining rules:

```markdown
# Epistemic Constitution: <change-name>

## 1. Evidential Rules
1. Every requirement **MUST** cite its source
2. Every decision **MUST** document alternatives considered
3. Every task **MUST** reference at least one requirement

## 2. Scope Rules
1. Tasks in Group N **MAY ONLY** modify files from Groups 1..N
2. Files outside allowed paths **MUST NOT** be modified

## 3. Validation Rules
1. Tasks **MUST** be verified before marked complete
2. Files **MUST** have header comments citing requirements

## 4. Memory Rules
1. After each group, memory **MUST** be updated
2. Citations **MUST** use format: `filename#location`
```

### Knowledge Harvesting

`/sdd-init-memory` automatically harvests knowledge from proposal.md:

**Goals → requirements.json (type: functional)**
- Extracts "Goal X" bullets
- Creates functional requirements with status "pending"

**Constraints → requirements.json (type: constraint)**
- Extracts technical/business/external constraints
- Creates constraint requirements

**Context Log → episodes.json (exploration)**
- Parses Q&A pairs
- Creates exploration episodes with insights

**Exploration Notes → episodes.json (judgments)**
- Extracts options considered
- Creates judgments with confidence "medium"

**Preserves decisions.json** - Left empty for design phase to populate

### Output

```
✓ Initialized memory for: user-authentication

Created:
  .specs/changes/user-authentication/.memory/
  ├── decisions.json      (0 decisions - ready for design phase)
  ├── requirements.json   (5 harvested from proposal)
  ├── citations.json      (0 citations)
  ├── control-log.json    (0 checkpoints)
  └── episodes.json       (4 exploration episodes)

Harvested from proposal.md:
  Goals → 3 functional requirements (REQ-FUNC-001, REQ-FUNC-002, REQ-FUNC-003)
  Constraints → 2 constraint requirements (REQ-CONST-001, REQ-CONST-002)
  Context Log → 3 exploration episodes
  Exploration Notes → 1 episode with options considered

Memory is ready for SCL-enhanced artifact creation.
The design agent will receive full context from exploration.
Use /sdd-artefact-scl to create artifacts with memory tracking.
```

### Re-harvesting

If you edit `proposal.md` after initialization, run `/sdd-init-memory` again:
- Updates requirements and episodes from changed proposal
- Preserves decisions.json (design phase owns these)
- Logs re-harvest in control-log.json

---

## Phase 4: Develop Artifacts with Memory

Create artifacts with memory tracking:

```
/sdd-artefact-scl
```

Each artifact creation follows the SCL 5-phase loop:

1. **Retrieve** - Load memory context
2. **Cognition** - Generate artifact content
3. **Control** - Validate citations, check regulation
4. **Action** - Write files
5. **Memory Write** - Update decisions, requirements, citations

### Output

```
✓ Retrieved: Memory context loaded (0 decisions, 0 requirements)
✓ Cognition: Generated specs/user-auth/spec.md with 5 requirements
✓ Control: All 12 citations verified, regulation compliant
✓ Action: Written to specs/user-auth/spec.md
✓ Memory: Updated decisions.json, requirements.json, citations.json

Memory State:
  decisions: 0
  requirements: 5 (extracted)
  citations: 12 (recorded)
```

---

## Phase 5: Implement with Memory Context

### Group Execution with Memory

```
/sdd-apply-group-scl 2
```

**Before dispatch:**
- Load memory context (decisions, requirements)
- Verify preconditions
- Generate scope constraints

**Subagent receives:**
- Full memory context from prior groups
- Allowed/blocked file lists
- Required citations

**After completion:**
- Verify scope compliance
- Update memory (status, citations)
- Log episode

### Parallel Execution with Persistence

```
/sdd-apply-all-scl
```

Executes all groups with memory persistence between groups.

### Subagent Context Injection

When dispatching subagents, the following context is injected:

```
You are executing Group N: <Group Name> of <spec-name>.

## Memory Context (from prior work)

### Decisions You MUST Follow
<list with sources>

### Requirements You MUST Satisfy
<list with sources>

### Prior Work Outcomes
<what was done>

## Constraints (YOU MUST NOT VIOLATE)

### Allowed Files
You MAY only create/modify: <list>

### Blocked Files
You MUST NOT touch: <list>

### Required Citations
Every file MUST include:
// Implements: REQ-ID (per specs/.../spec.md#L<N>)

## Your Tasks
<task list>

## Completion Criteria
You MUST:
1. Complete ALL tasks
2. Verify all files exist
3. Ensure all tests pass
4. Output "GROUP N COMPLETE" as final line
```

---

## Phase 6: Verify with Memory Tracing

Verify with full memory tracing:

```
/sdd-verify-scl
```

Output:

```
═══════════════════════════════════════════════════════════
SCL Verification Report: user-authentication
═══════════════════════════════════════════════════════════

## Summary

Goal Fidelity: 0.92 (EXCELLENT)
- Requirements: 11/12 implemented (92%)
- Tests: 18/20 passing (90%)
- Citations: 45/47 valid (96%)
- Memory: Consistent

## Citation Integrity

Valid: 45/47 (96%)
Broken:
  - CIT-012: design.md#L999 (line does not exist)
  - CIT-027: specs/auth/spec.md#L200 (section removed)

## Recommendation

✓ READY FOR ARCHIVE

═══════════════════════════════════════════════════════════
```

---

## Phase 7: Archive

Same as standard workflow:

```
/sdd-archive
```

Memory is preserved in the archive for future reference.

---

## SCL-Enhanced Task Format

Tasks in SCL mode include additional metadata:

```markdown
- [ ] 2.1 Implement password hashing utility
  - _Requirements: AUTH-001 (per specs/auth/spec.md#L23)_
  - _Evidence: design.md#decision-password-hashing (DEC-003)_
  - _Precondition: Task 1.2 complete (bcrypt installed)_
  - _Creates: src/auth/utils/hash.ts_
  - _Validation:_
    - Unit tests pass
    - Bcrypt cost factor = 12 (per DEC-003)
    - Export signature matches interface
  - _Memory Write:_
    - `requirements.json#AUTH-001.status ← "implemented"`
    - `citations.json ← hash.ts implements AUTH-001`
```

---

## Inspect Memory State

Debug memory state at any time:

```
/sdd-memory-status <change-name>
```

Shows:
- Decision count and sources
- Requirement status
- Citation graph
- Episode history

---

## Expected Benefits

| Metric | Standard | SCL-Enhanced |
|--------|----------|--------------|
| Task Success Rate | ~70% | ~86% |
| Redundant Actions | High | ~50% reduction |
| Memory Fidelity | Low | High (persistent) |
| Hallucination Rate | Moderate | ~3x reduction |
| Error Localization | Poor | Good (cycle-level logs) |

---

## Quick Reference

```bash
# Explore (optional but recommended)
/sdd-explore [name]

# Propose
/sdd-propose <name>

# Initialize SCL memory (harvests from proposal)
/sdd-init-memory <name>

# Develop with memory
/sdd-artefact-scl

# Implement with memory (choose one)
/sdd-apply-group-scl 2    # Group 2
/sdd-apply-all-scl        # All groups

# Verify with tracing
/sdd-verify-scl

# Debug memory
/sdd-memory-status <name>

# Complete
/sdd-archive
```
