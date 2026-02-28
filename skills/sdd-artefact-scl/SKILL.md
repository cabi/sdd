---
name: sdd-artefact-scl
description: SCL-enhanced artifact creation with memory persistence, evidential grounding, and control validation. Creates ONE artifact per invocation with full traceability.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: advanced
  author: OpenCode
  version: "1.0.0"
---

# SDD Artefact Creation (SCL-Enhanced)

Create the next artifact using the Structured Cognitive Loop approach. This skill implements all five SCL epistemic conditions: evidential grounding, memorial persistence, normative control, environmental coupling, and recursive self-reference.

## RFC2119 Requirements

### Core Requirements

1. The system **MUST** follow the Retrieve → Cognition → Control → Action → Memory Update cycle
2. The system **MUST** create exactly ONE artifact per invocation
3. The system **MUST** cite evidence for all claims within artifacts
4. The system **MUST** update memory after each artifact creation
5. The system **MUST** log control checkpoints
6. The system **MUST NOT** proceed if control validation fails

### Artifact Creation Cycle

```
┌─────────────────────────────────────────────────────────────────┐
│                    ARTEFACT CREATION LOOP                        │
│                                                                   │
│  ┌──────────────┐                                                │
│  │  1. RETRIEVE │ Load memory, read dependencies                │
│  └──────┬───────┘                                                │
│         ▼                                                        │
│  ┌──────────────┐                                                │
│  │ 2. COGNITION │ Generate artifact content with citations      │
│  └──────┬───────┘                                                │
│         ▼                                                        │
│  ┌──────────────┐                                                │
│  │ 3. CONTROL   │ Validate citations, check rules               │
│  └──────┬───────┘                                                │
│         │                                                        │
│    ┌────┴────┐                                                   │
│    ▼         ▼                                                   │
│ approve    defer/block                                           │
│    │         │                                                   │
│    ▼         └──► Return to Cognition with corrections          │
│  ┌──────────────┐                                                │
│  │  4. ACTION   │ Write artifact file                            │
│  └──────┬───────┘                                                │
│         ▼                                                        │
│  ┌──────────────┐                                                │
│  │5. MEM UPDATE │ Extract decisions, requirements, citations    │
│  └──────────────┘                                                │
└─────────────────────────────────────────────────────────────────┘
```

## Dependency Graph

```
                    proposal.md
                    (root node)
                         │
         ┌───────────────┴───────────────┐
         │                               │
         ▼                               ▼
      specs/                          design.md
   (requirements)                  (optional)
         │                               │
         └───────────────┬───────────────┘
                         │
                         ▼
                     tasks.md
```

## State Detection

The system **MUST** check artifact status:

| Status | Condition |
|--------|-----------|
| **DONE** | File exists AND has substantive content AND memory updated |
| **READY** | All dependencies are DONE |
| **BLOCKED** | Missing one or more dependencies |
| **PENDING** | Placeholder exists, no content |

## Phase 1: Retrieve

### Step 1.1: Detect Current Change

The system **MUST** scan `.specs/changes/` for active changes:

```
IF multiple changes found:
  ASK user to select one

FOR selected change:
  CHECK artifact status for: proposal.md, specs/, design.md, tasks.md
  DETERMINE next READY artifact (priority: specs > design > tasks)
```

### Step 1.2: Load Memory Context

The system **MUST** load memory before cognition:

```javascript
memory_context = {
  decisions: MEM.read({ type: "decisions" }),
  requirements: MEM.read({ type: "requirements" }),
  citations: MEM.read({ type: "citations" }),
  control_status: MEM.read({ type: "control-log", latest: true }),
  regulation: READ("regulation.md")
}
```

### Step 1.3: Read Dependencies

The system **MUST** read all dependency artifacts:

**For specs creation:**
- READ `proposal.md` for capabilities list and scope
- IF modifying existing capability: READ `.specs/specs/<module>/<capability>/spec.md`

**For design creation:**
- READ `proposal.md` for context and scope
- READ `specs/**/*.md` for requirements to design

**For tasks creation:**
- READ `proposal.md` for scope
- READ `specs/**/*.md` for requirements
- READ `design.md` for technical approach and decisions

## Phase 2: Cognition

### Step 2.1: Generate with Evidence

The system **MUST** generate artifact content with evidential grounding:

**Evidential Grounding Requirements:**
1. Every requirement **MUST** cite its source
2. Every decision **MUST** cite alternatives considered
3. Every task **MUST** reference at least one requirement
4. Every claim **MUST** be traceable to evidence

### Step 2.2: Create Specs (Requirements)

For each capability, the system **MUST** create `.specs/changes/<name>/specs/<capability>/spec.md`:

**For NEW capabilities:**

```markdown
# Specification: <capability-name>

> Memory ID: requirements.json#<CAPABILITY-ID>
> Created: <ISO 8601 timestamp>

## ADDED Requirements

### Requirement: <REQ-ID>
> Source: <proposal.md#L<N> | user-request | derived-from:REQ-XXX>
> Type: functional | non-functional | constraint

The system SHALL <specific behavior>.

#### Scenario: <SCENARIO-ID>
> Validates: <REQ-ID>
- **GIVEN** <precondition>
- **WHEN** <event or condition>
- **THEN** <expected outcome>

#### Scenario: <SCENARIO-ID-ERROR>
> Validates: <REQ-ID> error handling
- **GIVEN** <precondition>
- **WHEN** <error condition>
- **THEN** <expected error handling>
```

**For MODIFIED capabilities (delta specs):**

```markdown
# Specification: <capability-name>

> Existing spec: specs/<module>/<capability>/spec.md
> This is a DELTA spec showing changes from existing.
> Memory ID: requirements.json#<CAPABILITY-ID>

## MODIFIED Requirements

### Requirement: <REQ-ID>
> Source: modified from existing
> Original: specs/<module>/<capability>/spec.md#L<N>
> Change reason: <why this is being modified>

The system SHALL <updated behavior>.

#### Scenario: <SCENARIO-ID>
> Status: MODIFIED
- **WHEN** <condition>
- **THEN** <outcome>

## ADDED Requirements

### Requirement: <REQ-ID>
> Source: <where this requirement originated>
> Type: functional

The system SHALL <behavior>.

#### Scenario: <SCENARIO-ID>
- **WHEN** <condition>
- **THEN** <outcome>

## REMOVED Requirements

### Requirement: <REQ-ID>
> Original: specs/<module>/<capability>/spec.md#L<N>
> Removed because: <reason>
> Migration: <how existing users migrate>
```

### Step 2.3: Create Design

The system **MUST** create `.specs/changes/<name>/design.md`:

```markdown
# Design: <spec-name>

> Memory ID: decisions.json#<DESIGN-ID>
> Created: <ISO 8601 timestamp>
> Depends on: requirements.json#<REQ-IDs>

## Context

> Evidence: proposal.md#L<N>

<Background, current state, constraints>

**Constraints:**
> Source: proposal.md#constraints
- Technical: <constraints>
- Business: <constraints>
- External: <constraints>

## Goals / Non-Goals

### Goals
> Evidence: requirements.json#<REQ-IDs>
- <What this design achieves>

### Non-Goals
> Evidence: proposal.md#scope
- <Explicitly excluded>

## Architecture

> Evidence: requirements.json#architectural-constraints

<High-level system design>

### Flow
> Validates: <REQ-IDs related to flow>
1. <Step 1>
2. <Step 2>

## Decisions

### Decision: DEC-NNN - <title>

> Evidence: requirements.json#<REQ-IDs influencing this decision>
> Created: <timestamp>

**Context:** <situation requiring decision>

**Options Considered:**
1. **<Option 1>**
   - Pros: <benefits>
   - Cons: <drawbacks>
   - Evidence: <any supporting evidence>
2. **<Option 2>**
   - Pros: <benefits>
   - Cons: <drawbacks>
   - Evidence: <any supporting evidence>

**Decision:** <chosen option>

**Rationale:** <why this was selected>

**Implications:**
- Affects: <what this decision impacts>
- Enables: <what this makes possible>
- Constrains: <what this limits>

## Components

> Evidence: decisions.json#<DEC-IDs>

### <ComponentName>
**Responsibility:** <what it does>
**Interface:** <public API>
**Dependencies:** <other components>
**Evidence:** <requirements or decisions defining this>

## Risks / Trade-offs

| Risk | Impact | Probability | Mitigation | Evidence |
|------|--------|-------------|------------|----------|
| <risk> | High/Med/Low | High/Med/Low | <mitigation> | <REQ-ID> |

## Migration Plan

> Evidence: requirements.json#migration-requirements

### Phase 1: <name>
> Validates: <REQ-IDs>
1. <Step>

### Rollback Plan
1. <Rollback step>

## Open Questions

- [ ] <Question> - **Resolution needed by:** <date>
  - **Impact if unresolved:** <what breaks>
  - **Evidence:** <related REQ-IDs or DEC-IDs>
```

### Step 2.4: Create Tasks

The system **MUST** create `.specs/changes/<name>/tasks.md`:

```markdown
# Tasks: <spec-name>

> Memory ID: decisions.json#<TASKS-ID>
> Created: <ISO 8601 timestamp>
> Depends on: requirements.json#<REQ-IDs>, decisions.json#<DEC-IDs>
> Regulation: regulation.md

## Execution Metadata

| Group | Tasks | Depends On | Parallel-Safe | Files Created |
|-------|-------|------------|---------------|---------------|
| 1. Setup | 3 | - | no | <files> |
| 2. Core | 5 | 1 | yes | <files> |

## 1. Setup

_Meta: sequential, foundation_

### Preconditions
> Verified by: CONTROL.check_preconditions()
- [ ] `design.md` exists
- [ ] `requirements.json` has relevant requirements

### Memory Context for Subagent
```json
{
  "decisions": ["DEC-001", "DEC-002"],
  "requirements": ["AUTH-001", "AUTH-002"],
  "constraints": {
    "allowed_files": ["src/auth/**/*"],
    "must_cite": ["design.md#*", "specs/**/spec.md#*"]
  }
}
```

### Tasks

- [ ] 1.1 Create auth module directory structure
  - _Requirements: AUTH-SETUP-001 (per specs/auth/spec.md#L23)_
  - _Evidence: design.md#L45-52 (auth module layout)_
  - _Creates: src/auth/, src/auth/service/, src/auth/api/_
  - _Validation: All directories exist, index.ts in each_
  - _Memory Write: decisions.json ← "Auth structure per design.md#L45"_
  
- [ ] 1.2 Add auth dependencies
  - _Requirements: AUTH-SETUP-002_
  - _Evidence: design.md#decision-dependencies_
  - _Modifies: package.json_
  - _Validation: Dependencies installed successfully_
  - _Memory Write: requirements.json#AUTH-SETUP-002.status ← "implemented"_

## 2. Core Implementation

_Meta: parallel-safe, depends on: 1_

### Preconditions
- [ ] Group 1 complete (CONTROL.verify_group(1))
- [ ] Directories exist: src/auth/, src/auth/service/

### Tasks

- [ ] 2.1 Implement password hashing utility
  - _Requirements: AUTH-001, AUTH-002 (per specs/auth/spec.md#L45-67)_
  - _Evidence: design.md#decision-password-hashing (DEC-003)_
  - _Precondition: Task 1.1 complete_
  - _Creates: src/auth/utils/hash.ts_
  - _Validation:_
    - Unit tests pass
    - Bcrypt cost factor = 12 (per DEC-003)
    - Export signature matches interface
  - _Memory Write:_
    - `requirements.json#AUTH-001.status ← "implemented"`
    - `citations.json ← hash.ts implements AUTH-001`
```

## Phase 3: Control

### Step 3.1: Validate Citations

The system **MUST** verify all citations resolve:

```
FOR each citation in artifact:
  result = CONTROL.verify_citation(citation)
  IF NOT result.valid:
    ADD error: "Citation {{citation}} does not resolve: {{result.error}}"
    BLOCK artifact creation
```

### Step 3.2: Check Regulation Compliance

The system **MUST** validate against regulation rules:

```
rules = PARSE("regulation.md")
evaluation = CONTROL.evaluate_rules(artifact_content, rules)

IF evaluation.violations contains severity="block":
  BLOCK artifact creation
  REPORT violated rules
ELIF evaluation.violations contains severity="warn":
  WARN user but allow creation
  LOG warnings to control-log.json
```

### Step 3.3: Check Consistency

The system **MUST** verify no contradictions:

```
existing_decisions = MEM.read({ type: "decisions" })
FOR each new_decision in artifact:
  FOR each existing_decision in existing_decisions:
    IF new_decision.contradicts(existing_decision):
      WARN "New decision {{new_decision.id}} contradicts {{existing_decision.id}}"
      REQUIRE explicit resolution before proceeding
```

## Phase 4: Action

### Step 4.1: Write Artifact

The system **MUST** write the artifact file:

```
IF control.approved:
  WRITE(artifact_path, artifact_content)
  VERIFY file exists and has correct content
ELSE:
  ABORT with control.reason
```

### Step 4.2: Update Proposal Status

The system **MUST** update the Status section in `proposal.md`:

```markdown
## Status
- [x] Requirements: done (specs/ created)
- [ ] Design: pending
- [ ] Tasks: pending
- [x] Memory: updated (see .memory/)
```

## Phase 5: Memory Update

### Step 5.1: Extract Decisions

The system **MUST** extract decisions from the created artifact:

```
IF artifact_type == "design":
  FOR each decision_section in artifact:
    MEM.write({
      type: "decision",
      data: {
        id: "DEC-NNN",
        title: decision_section.title,
        source: "{{artifact_path}}#{{location}}",
        context: decision_section.context,
        options_considered: decision_section.options,
        chosen: decision_section.decision,
        rationale: decision_section.rationale,
        evidence: decision_section.evidence
      }
    })
```

### Step 5.2: Extract Requirements

The system **MUST** extract requirements from specs:

```
IF artifact_type == "specs":
  FOR each requirement in artifact:
    MEM.write({
      type: "requirement",
      data: {
        id: requirement.id,
        title: requirement.title,
        description: requirement.description,
        source: "{{artifact_path}}#{{location}}",
        type: requirement.type,
        status: "pending",
        scenarios: requirement.scenarios
      }
    })
```

### Step 5.3: Record Citations

The system **MUST** record all citations:

```
FOR each citation in artifact:
  MEM.write({
    type: "citation",
    data: {
      from: "{{artifact_path}}#{{location}}",
      to: citation.target,
      relationship: citation.relationship,
      verified: true // verified in Phase 3
    }
  })
```

### Step 5.4: Log Control Checkpoint

The system **MUST** record the control checkpoint:

```
MEM.write({
  type: "checkpoint",
  data: {
    phase: "artefact-creation",
    artifact: artifact_path,
    checks: control.check_results,
    decision: control.decision,
    overall: "pass" | "fail" | "warn"
  }
})
```

## Quality Checklist

### Before Completion

The system **MUST** verify:

- [ ] Artifact file exists with content
- [ ] All citations verified
- [ ] Memory updated with extractions
- [ ] Control checkpoint logged
- [ ] Proposal status updated

### For Specs Specifically

- [ ] All requirements have IDs
- [ ] Each requirement has at least one scenario
- [ ] Scenarios use GIVEN/WHEN/THEN format
- [ ] All requirements cite their source

### For Design Specifically

- [ ] All decisions have at least two options
- [ ] All decisions have rationale
- [ ] All decisions cite evidence
- [ ] Risks identified and mitigated

### For Tasks Specifically

- [ ] All tasks reference requirements
- [ ] All tasks have evidence citations
- [ ] Group metadata is complete
- [ ] Preconditions are specified

## Error Handling

### Citation Failures

IF citations do not resolve:
1. LIST all failed citations with error messages
2. OFFER to create placeholder targets
3. BLOCK until resolved or explicitly overridden

### Regulation Violations

IF regulation rules are violated:
1. LIST violated rules with severity
2. FOR block violations: HALT and require fix
3. FOR warn violations: WARN and offer to proceed

### Memory Write Failures

IF memory update fails:
1. DO NOT mark artifact as complete
2. ROLLBACK artifact write if possible
3. REPORT error with recovery steps
