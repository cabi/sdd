---
name: sdd-spec-artefact
description: Create the next artifact in a spec development process. Uses dependency detection to determine what's next. Creates ONE artifact per invocation to maintain quality and allows the user to review before proceeding.
---

# SDD Spec Artefact

Create the next artifact in the spec development process.

## Dependency Graph

```
                    proposal
                   (root node)
                       │
         ┌─────────────┴─────────────┐
         │                           │
         ▼                           ▼
      specs                       design
   (requirements)               (optional)
         │                           │
         └─────────────┬─────────────┘
                       │
                       ▼
                    tasks
```

## State Detection

Check filesystem for artifact status:

| Status | Condition |
|--------|-----------|
| **DONE** | File exists AND has substantive content |
| **READY** | All dependencies are DONE |
| **BLOCKED** | Missing one or more dependencies |
| **PENDING** | Placeholder exists, no content |

## Guardrails

**CRITICAL - Follow these strictly:**

1. Create ONE artifact per invocation
2. Always read dependency artifacts before creating new ones
3. Never skip artifacts or create out of order
4. If context is unclear, ask the user before creating
5. Verify the artifact file exists after writing
6. `context` and `rules` are constraints for YOU, not content for the file

## Process

### Step 1: Detect Current State

Scan `.specs/changes/` for all active changes. If multiple found, ask user which one to continue.

For the selected change, check each artifact:

```
proposal.md  → DONE (exists with content)
specs/       → READY (proposal is done)
design.md    → READY (proposal is done)  
tasks.md     → BLOCKED (needs specs AND design)
```

### Step 2: Select Next Artifact

Priority order when multiple are READY:
1. **specs** (requirements) - before design if both ready
2. **design** - can be done in parallel with specs
3. **tasks** - only after specs AND design are done

If all DONE, inform user and suggest `/sdd:apply`.

### Step 3: Read Dependencies

Before creating, read all dependency artifacts:

**For specs:**
- Read `proposal.md` for capabilities list and scope
- If modifying existing capability, read `.specs/specs/<module>/<capability>/spec.md`

**For design:**
- Read `proposal.md` for context and scope

**For tasks:**
- Read `proposal.md` for scope
- Read `specs/**/*.md` for requirements to implement
- Read `design.md` for technical approach

### Step 4: Create Artifact

#### Creating specs (Requirements)

For each capability in proposal, create `.specs/changes/<name>/specs/<capability>/spec.md`:

**For NEW capabilities:**

```markdown
# Specification: <capability-name>

## ADDED Requirements

### Requirement: <requirement-name>
The system SHALL <specific behavior>.

#### Scenario: <scenario-name>
- **WHEN** <condition>
- **THEN** <expected system behavior>

#### Scenario: <another-scenario>
- **WHEN** <event>
- **AND** <condition>
- **THEN** <expected behavior>
```

**For MODIFIED capabilities (delta specs):**

```markdown
# Specification: <capability-name>

> Existing spec: specs/<module>/<capability>/spec.md
> This is a DELTA spec showing changes from existing.

## MODIFIED Requirements

### Requirement: <requirement-name>
<Full updated requirement content including all scenarios>

#### Scenario: <scenario-name>
- **WHEN** <condition>
- **THEN** <outcome>

## ADDED Requirements

### Requirement: <new-requirement>
The system SHALL <behavior>.

#### Scenario: <scenario-name>
- **WHEN** <condition>
- **THEN** <outcome>

## REMOVED Requirements

### Requirement: <deprecated-requirement>
**Reason**: <why removed>
**Migration**: <how to migrate>
```

**EARS Format Requirements:**
- Use SHALL or MUST for normative requirements
- Each requirement MUST have at least one scenario
- Scenarios use WHEN/THEN format
- Cover normal, edge, and error cases

#### Creating design

**Require skill:** `sdd-design`
Create `.specs/changes/<name>/design.md`:

```markdown
# Design: <spec-name>

## Context
<Background, current state, constraints, stakeholders>

## Goals / Non-Goals

### Goals
- <What this design achieves>

### Non-Goals
- <Explicitly excluded from this design>

## Architecture
<High-level system design. Include diagrams if helpful.>

## Decisions

### Decision: <title>
**Context:** <situation requiring decision>
**Options Considered:**
1. <Option 1> - Pros: <benefits> / Cons: <drawbacks>
2. <Option 2> - Pros: <benefits> / Cons: <drawbacks>
**Decision:** <chosen option>
**Rationale:** <why this was selected>

## Components
<Description of key components and their responsibilities>

## Data Models
<Schema changes, new models, migrations needed>

## Risks / Trade-offs
| Risk | Mitigation |
|------|------------|
| <risk> | <mitigation> |

## Migration Plan
<Steps to deploy, rollback strategy>

## Open Questions
- <Outstanding decisions to resolve>
```

**When to include design.md:**
- Cross-cutting change (multiple services/modules)
- New external dependency
- Significant data model changes
- Security or performance complexity
- Ambiguity that benefits from upfront decisions

For simple changes, design can be minimal.

#### Creating tasks

**Require skill:** `sdd-tasks`
Create `.specs/changes/<name>/tasks.md`:

```markdown
# Tasks: <spec-name>

## 1. Setup

- [ ] 1.1 <setup task>
  - _Requirements: <requirement-ref>_

## 2. Core Implementation

- [ ] 2.1 <implementation task>
  - _Requirements: <requirement-ref>_
- [ ] 2.2 <implementation task>
  - _Requirements: <requirement-ref>_

## 3. Testing

- [ ] 3.1 <testing task>
  - _Requirements: <requirement-ref>_

## 4. Documentation & Cleanup

- [ ] 4.1 <documentation task>
```

**Task Guidelines:**
- Each task MUST be a checkbox: `- [ ] X.Y Task description`
- Tasks should complete in one session (2-4 hours)
- Order by dependency (what must be done first?)
- Reference specific requirements for traceability

### Step 5: Update Proposal Status

Update the Status section in `proposal.md`:

```markdown
## Status
- [x] Requirements: done
- [ ] Design: pending
- [ ] Tasks: pending
```

### Step 6: Report Progress

```
✓ Created: specs/<capability>/spec.md
✓ Updated proposal status

Artifact Status:
  proposal: DONE
  specs: DONE
  design: READY
  tasks: BLOCKED (waiting for design)

Next: Use /sdd-artefact to create design
```

## Quality Checklists

### Requirements Checklist
- [ ] All user roles identified
- [ ] Normal cases covered
- [ ] Edge cases covered
- [ ] Error cases covered
- [ ] Each requirement is testable
- [ ] EARS format used consistently

### Design Checklist
- [ ] All requirements addressed
- [ ] Key decisions documented
- [ ] Alternatives considered
- [ ] Risks identified

### Tasks Checklist
- [ ] All design elements have tasks
- [ ] Tasks properly sequenced
- [ ] Each task is actionable
- [ ] Requirement references included

**Loads skill:** `sdd-design`
