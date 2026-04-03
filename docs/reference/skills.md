# Skills Reference

All 11 SDD skills organized by purpose.

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

## Skill Summary Table

| Skill | Purpose | Used By |
|-------|---------|---------|
| `sdd-interview` | Clarify requirements | `/sdd-explore` |
| `sdd-spec-create` | Create proposal.md | `/sdd-propose` |
| `sdd-spec-artefact` | Create artifacts | `/sdd-artefact` |
| `sdd-spec-archive` | Archive completed | `/sdd-archive` |
| `sdd-requirements` | EARS format guide | Referenced |
| `sdd-design` | Design doc guide | Referenced |
| `sdd-tasks` | Task breakdown guide | Referenced |
| `sdd-spec-apply` | Implement tasks | `/sdd-apply` |
| `sdd-verify` | Verify implementation | `/sdd-verify` |
| `sdd-reverse` | Extract specs from code | `/sdd-reverse` |
