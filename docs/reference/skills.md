# Skills Reference

All 15 SDD skills organized by purpose.

## Exploration & Spec Creation Skills

### `sdd-interview`

Clarify requirements through structured questioning.

**Used by:** `/sdd-explore`

**Purpose:**
Ask clarifying questions to gather complete context during exploration phase. Captures:
- Problem being solved
- Success criteria
- Constraints
- Scope boundaries
- Options considered

---

### `sdd-spec-create`

Create proposal.md for a new change.

**Used by:** `/sdd-propose`

**Creates:**
- `.specs/changes/<name>/proposal.md` (with Context Log, Goals, Constraints sections)

**Purpose:**
Generates the formal proposal document from context-log.md. Transfers Q&A, goals, constraints, and exploration notes into structured sections for harvesting.

---

### `sdd-spec-artefact`

Create specification artifacts incrementally.

**Used by:** `/sdd-artefact`

**Creates:**
- `specs/<capability>/spec.md`
- `design.md`
- `tasks.md`

**Purpose:**
Generates the next ready artifact based on proposal context. Creates in order: specs → design → tasks.

---

### `sdd-spec-archive`

Archive completed specifications.

**Used by:** `/sdd-archive`

**Creates:**
- `SUMMARY.md`
- Moves to `.specs/archive/`

**Purpose:**
Verifies completion, creates summary document, moves change to archive, merges deltas into accumulated specs.

---

## Artifact Skills

### `sdd-requirements`

Guide for writing EARS format requirements.

**Used by:** Referenced during artifact creation

**Purpose:**
Provides patterns and examples for writing requirements in EARS (Easy Approach to Requirements Syntax) format:

```
WHEN <event> THEN system SHALL <response>
IF <condition> THEN system SHALL <response>
```

---

### `sdd-design`

Technical design documentation guide.

**Used by:** Referenced during artifact creation

**Purpose:**
Provides structure for design documents including:
- Context and goals
- Architecture decisions
- Component breakdown
- Data models
- Risks and trade-offs

---

### `sdd-tasks`

Task breakdown and sequencing guide.

**Used by:** Referenced during artifact creation

**Purpose:**
Provides structure for task documents including:
- Group organization with metadata
- Task format with requirements references
- Dependency tracking

**Task Format:**
```markdown
## 1. Setup
_Meta: sequential, foundation_

- [ ] 1.1 <Task description>
  - _Requirements: <ref>_
  - _Creates: <path>_
```

---

## Implementation Skills

### `sdd-spec-apply`

Implement tasks from specification.

**Used by:** `/sdd-apply`, `/sdd-apply-group`, `/sdd-apply-all`

**Purpose:**
Reads spec context (proposal, specs, design, tasks), finds next task, implements it, and marks complete.

---

### `sdd-verify`

Verify implementation matches specification.

**Used by:** `/sdd-verify`

**Purpose:**
Checks:
- Requirements coverage
- Scenario coverage
- Test status
- Gap identification

---

## Reverse Engineering Skills

### `sdd-reverse`

Extract specifications from existing code.

**Used by:** `/sdd-reverse`

**Creates:**
- `.specs/specs/<capability>/spec.md`

**Purpose:**
Scans existing codebase to detect capabilities and generate specification files. Use for brownfield projects.

---

## SCL Skills

### `sdd-memory`

Memory module with JSON schemas and harvesting operations.

**Used by:** SCL commands

**Provides:**
- `decisions.json` schema
- `requirements.json` schema
- `citations.json` schema
- `control-log.json` schema
- `episodes.json` schema
- `MEM.harvest_from_proposal()` operation

**Purpose:**
Defines the structure for SCL memory persistence and provides harvesting from proposal.md:

**Harvesting Rules:**
- Goals → requirements.json (type: functional)
- Constraints → requirements.json (type: constraint)
- Context Log → episodes.json (phase: exploration)
- Exploration Notes → episodes.json (judgments)
- Preserves decisions.json (design phase owns this)

Each schema tracks specific aspects:
- Decisions: choices made with alternatives and rationale
- Requirements: harvested from proposal + extracted from specs with status
- Citations: links between code and requirements
- Control-log: validation checkpoints
- Episodes: exploration history + cycle-by-cycle execution

---

### `sdd-control`

Control and validation module.

**Used by:** SCL commands

**Functions:**
- `precondition_check()` - Verify conditions before action
- `scope_verify()` - Check file boundaries
- `citation_validate()` - Verify citation integrity

**Purpose:**
Implements normative control for SCL workflow. Validates before executing actions.

---

### `sdd-artefact-scl`

SCL-enhanced artifact creation.

**Used by:** `/sdd-artefact-scl`

**Purpose:**
Creates artifacts with SCL 5-phase loop:

1. **Retrieve** - Load memory context
2. **Cognition** - Generate artifact content
3. **Control** - Validate citations, check regulation
4. **Action** - Write files
5. **Memory Write** - Update decisions, requirements, citations

---

### `sdd-tasks-scl`

SCL-enhanced task breakdown.

**Used by:** `/sdd-artefact-scl`

**Purpose:**
Generates tasks with SCL-specific metadata:

```markdown
- [ ] 2.1 <Task description>
  - _Requirements: REQ-ID (per specs/capability/spec.md#L<N>)_
  - _Evidence: design.md#decision-name_
  - _Creates: path/to/file.ts_
  - _Validation: <testable criteria>_
  - _Memory Write: requirements.json#REQ-ID.status ← "implemented"_
```

---

## Skill Summary Table

| Skill | Purpose | SCL | Used By |
|-------|---------|-----|---------|
| `sdd-interview` | Clarify requirements | No | `/sdd-explore` |
| `sdd-spec-create` | Create proposal.md | No | `/sdd-propose` |
| `sdd-spec-artefact` | Create artifacts | No | `/sdd-artefact` |
| `sdd-spec-archive` | Archive completed | No | `/sdd-archive` |
| `sdd-requirements` | EARS format guide | No | Referenced |
| `sdd-design` | Design doc guide | No | Referenced |
| `sdd-tasks` | Task breakdown guide | No | Referenced |
| `sdd-spec-apply` | Implement tasks | No | `/sdd-apply` |
| `sdd-verify` | Verify implementation | No | `/sdd-verify` |
| `sdd-reverse` | Extract specs from code | No | `/sdd-reverse` |
| `sdd-memory` | Memory schemas + harvesting | Yes | SCL commands |
| `sdd-control` | Control/validation | Yes | SCL commands |
| `sdd-artefact-scl` | SCL artifact creation | Yes | `/sdd-artefact-scl` |
| `sdd-tasks-scl` | SCL task breakdown | Yes | `/sdd-artefact-scl` |
