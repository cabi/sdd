# Spec-Driven Development: Deep Comparative Analysis

## Executive Summary

This analysis compares two implementations of Spec-Driven Development (SDD): **Kiro** (Amazon's approach) and **OpenSpec**. Both share the core principle of "clarity before code" but differ significantly in their approach to workflow enforcement, flexibility, and agent control mechanisms.

---

## 1. Core Understanding of SDD

### 1.1 Kiro's SDD Philosophy

**Core Principle:** "Clarity Before Code"

**Three Philosophical Pillars:**
1. **Clarity Before Code** - Ambiguity in requirements leads to wasted effort
2. **Iterative Refinement** - Each phase supports iteration and validation
3. **Documentation as Communication** - Specs serve as communication tools

**Cognitive Load Management:** Breaking development into distinct phases (requirements → design → tasks) allows focused thinking at each stage.

**Key Insight:** "AI systems excel when given clear, structured input"

### 1.2 OpenSpec's SDD Philosophy

**Core Principle:** "Fluid not rigid, Iterative not waterfall"

**Five Design Principles:**
1. **Fluid not rigid** - No phase gates, work on what makes sense
2. **Iterative not waterfall** - Learn as you build, refine as you go
3. **Easy not complex** - Lightweight setup, minimal ceremony
4. **Built for brownfield** - Works with existing codebases
5. **Scalable** - From personal projects to enterprises

**Key Insight:** "Work isn't linear. OPSX stops pretending it is."

### 1.3 Philosophical Comparison

| Aspect | Kiro | OpenSpec |
|--------|------|----------|
| **Primary Focus** | Document quality & structure | Workflow flexibility |
| **Phase Model** | Sequential with iteration WITHIN phases | Actions (not phases), any order |
| **Workflow View** | Phases exist but allow refinement | No phases, only dependencies |
| **Agent Role** | Consumer of structured specs | Active participant in workflow |
| **Change Handling** | Update specs when gaps found | Natural iteration, edit anytime |

---

## 2. Workflow Comparison

### 2.1 Kiro Workflow: Three-Phase Sequential

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   REQUIREMENTS  │───►│     DESIGN      │───►│     TASKS       │
│     Phase       │    │     Phase       │    │     Phase       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
        │                      │                      │
        ▼                      ▼                      ▼
   EARS Format          Architecture            Task Checklist
   User Stories         Components             Dependencies
   Acceptance Crit.     Data Models            Traceability
   Validation           Testing Strategy       Time Estimates
```

**Phase Outputs:**
- **Requirements:** `requirements.md` with EARS-formatted acceptance criteria
- **Design:** `design.md` with architecture, components, decisions
- **Tasks:** `tasks.md` with checkbox hierarchy, requirement references

### 2.2 OpenSpec Workflow: Artifact Dependency Graph

```
                    proposal
                   (root node)
                       │
         ┌─────────────┴─────────────┐
         │                           │
         ▼                           ▼
      specs                       design
   (delta specs)               (optional)
         │                           │
         └─────────────┬─────────────┘
                       │
                       ▼
                    tasks
                       │
                       ▼
                   apply
                       │
                       ▼
                  archive
```

**Artifact Outputs:**
- **proposal:** `proposal.md` - WHY, WHAT changes, capabilities
- **specs:** `specs/**/*.md` - Delta specs with ADDED/MODIFIED/REMOVED
- **design:** `design.md` (conditional) - HOW, decisions, trade-offs
- **tasks:** `tasks.md` - Implementation checklist

### 2.3 Workflow Commands Comparison

| Function | Kiro | OpenSpec |
|----------|------|----------|
| **Start** | Use skill manually | `/opsx:new` or `/opsx:explore` |
| **Plan Incrementally** | Manual phase progression | `/opsx:continue` |
| **Plan All at Once** | Not supported | `/opsx:ff` |
| **Implement** | Mark tasks manually | `/opsx:apply` |
| **Finish** | Archive manually | `/opsx:archive` |
| **Status Check** | No CLI | `openspec status --json` |

---

## 3. Spec Structure Comparison

### 3.1 Kiro Spec Structure

**Requirements Document:**
```markdown
# Requirements Document

## Introduction
[Brief overview]

## Requirements

### Requirement 1
**User Story:** As a [role], I want [feature], so that [benefit]

#### Acceptance Criteria
1. WHEN [event] THEN [system] SHALL [response]
2. IF [precondition] THEN [system] SHALL [response]
```

**Design Document:**
```markdown
# Design Document

## Overview
## Architecture
## Components and Interfaces
## Data Models
## Error Handling
## Testing Strategy
```

**Tasks Document:**
```markdown
- [ ] 1. [Epic/Major Component]
- [ ] 1.1 [Specific task]
  - [Implementation details]
  - _Requirements: [refs]_
```

### 3.2 OpenSpec Spec Structure

**Delta Spec Format (key innovation):**
```markdown
## ADDED Requirements

### Requirement: User can export data
The system SHALL allow users to export their data.

#### Scenario: Successful export
- **WHEN** user clicks "Export"
- **THEN** system downloads a CSV file

## MODIFIED Requirements

### Requirement: <name>
<full updated content - MUST be complete>

## REMOVED Requirements

### Requirement: Legacy export
**Reason**: Replaced by new system
**Migration**: Use /api/v2/export

## RENAMED Requirements

### FROM: old-name
### TO: new-name
```

### 3.3 Key Structural Differences

| Aspect | Kiro | OpenSpec |
|--------|------|----------|
| **Spec Type** | Full specs per feature | Delta specs (changes only) |
| **Accumulation** | Each feature = new spec | Specs accumulate in `openspec/specs/` |
| **Modification** | Rewrite spec | Delta operations (ADDED/MODIFIED/REMOVED) |
| **Traceability** | Manual references | Built into delta format |
| **Validation** | Checklists | Schema + parser validation |

---

## 4. Agent Compliance Techniques

### 4.1 Kiro Techniques

#### A. Structured Skills System (8 Skills)

| Skill | Purpose | Compliance Mechanism |
|-------|---------|---------------------|
| spec-driven-development | Master methodology | Phase separation rules |
| requirements-engineering | EARS format | Format patterns & examples |
| design-documentation | Technical design | Document structure template |
| task-breakdown | Implementation planning | Sequencing strategies |
| ai-prompting | AI communication | Context-first prompting |
| quality-assurance | Testing | Phase-specific validation |
| troubleshooting | Problem resolution | Issue patterns & fixes |
| create-steering-documents | Project standards | Inclusion mechanisms |

#### B. Steering Documents System

**Three Inclusion Mechanisms:**
```yaml
# Always included (default)
# No front-matter needed

---
inclusion: fileMatch
fileMatchPattern: '*.tsx|*.jsx'
---

---
inclusion: manual
---
```

**Purpose:** Inject project-specific context into every interaction.

#### C. Quality Checklists

Each phase has explicit checklists:
- Requirements: 5 validation criteria
- Design: 5 validation criteria
- Tasks: 5 validation criteria

#### D. Response Style Guidelines

```
- Be decisive, precise, and clear
- Prioritize actionable information
- Write only ABSOLUTE MINIMAL code needed
```

### 4.2 OpenSpec Techniques

#### A. Dependency Graph Engine

```typescript
// Kahn's algorithm for topological sort
getBuildOrder(): string[]

// Get ready artifacts (all dependencies completed)
getNextArtifacts(completed: CompletedSet): string[]

// Get blocked artifacts
getBlocked(completed: CompletedSet): BlockedArtifacts
```

**State Transitions:**
```
BLOCKED ──────► READY ──────► DONE
   │               │              │
Missing deps    All deps      File exists
               are DONE      on filesystem
```

#### B. Guardrails in Skill Instructions

```markdown
**Guardrails**
- Create ONE artifact per invocation
- Always read dependency artifacts before creating
- Never skip artifacts or create out of order
- If context unclear, ask the user before creating
- Verify artifact file exists after writing
- `context` and `rules` are constraints for YOU, not content
```

#### C. Context Injection via XML Tags

```xml
<artifact id="specs" change="add-auth">
  <project_context>
    <!-- Background - NOT in output -->
  </project_context>
  
  <rules>
    <!-- Constraints - NOT in output -->
  </rules>
  
  <dependencies>
    <dependency id="proposal" status="done">
      <path>openspec/changes/add-auth/proposal.md</path>
    </dependency>
  </dependencies>
  
  <output>
    Write to: openspec/changes/add-auth/specs/auth/spec.md
  </output>
  
  <unlocks>
    Completing this enables: tasks
  </unlocks>
</artifact>
```

#### D. Schema-Driven Validation

```yaml
# schema.yaml
artifacts:
  - id: proposal
    requires: []
  - id: specs
    requires: [proposal]
  - id: tasks
    requires: [specs, design]
```

Validates:
- No circular dependencies
- All artifact IDs unique
- Dependency references exist

#### E. Task Checkbox Parsing

```typescript
// Parse - [ ] and - [x] checkboxes
const checkboxMatch = line.match(/^[-*]\s*\[([ xX])\]\s*(.+)\s*$/);
```

#### F. Project Configuration Injection

```yaml
# openspec/config.yaml
context: |
  Tech stack: TypeScript, React, Node.js
  
rules:
  proposal:
    - Include rollback plan
  specs:
    - Use Given/When/Then format
```

### 4.3 Compliance Technique Comparison

| Technique | Kiro | OpenSpec | Effectiveness |
|-----------|------|----------|---------------|
| **Phase Gates** | ✅ (soft) | ❌ | Medium |
| **Dependency Graph** | ❌ | ✅ | High |
| **State Detection** | ❌ | ✅ (filesystem) | High |
| **Checklists** | ✅ | ✅ | Medium |
| **Guardrails Text** | ❌ | ✅ | Medium |
| **Context Injection** | ✅ (steering) | ✅ (config) | High |
| **Schema Validation** | ❌ | ✅ (Zod) | High |
| **CLI Tooling** | ❌ | ✅ | Very High |

---

## 5. Common Parts

### 5.1 Shared Principles

| Principle | Implementation in Both |
|-----------|----------------------|
| **Clarity Before Code** | Both emphasize upfront planning |
| **Requirements → Design → Tasks** | Same three-artifact flow |
| **Traceability** | Both link tasks to requirements |
| **EARS/WHEN-THEN Format** | Both use structured scenarios |
| **Checklist Tasks** | Both use `- [ ]` checkbox format |
| **Quality Validation** | Both have phase checklists |

### 5.2 Shared Artifacts

| Artifact | Kiro | OpenSpec |
|----------|------|----------|
| **Requirements/Specs** | ✅ requirements.md | ✅ specs/**/*.md |
| **Design** | ✅ design.md | ✅ design.md |
| **Tasks** | ✅ tasks.md | ✅ tasks.md |
| **Proposal** | ❌ (implicit) | ✅ proposal.md |

### 5.3 Shared Agent Patterns

1. **Context-First Prompting:** Provide background before requests
2. **Phased Interaction:** Work through phases sequentially
3. **Iterative Refinement:** Allow updates during implementation
4. **Validation-Oriented:** Build quality checks into prompts

---

## 6. Key Differences

### 6.1 Architecture Differences

| Aspect | Kiro | OpenSpec |
|--------|------|----------|
| **Implementation** | Documentation + Skills | CLI Tool + Skills |
| **Workflow Engine** | None (manual) | Dependency graph engine |
| **State Management** | None | Filesystem detection |
| **Spec Accumulation** | Per-feature | Global `openspec/specs/` |
| **Change Isolation** | Per spec directory | `openspec/changes/<name>/` |

### 6.2 Flexibility Differences

| Aspect | Kiro | OpenSpec |
|--------|------|----------|
| **Phase Order** | Sequential (soft) | Dependencies (enforced) |
| **Iteration** | Within phases | Anytime |
| **Schema Customization** | Manual skill editing | `schema.yaml` files |
| **Tool Integration** | Claude Code plugin | 20+ tool adapters |

### 6.3 Delta Specs: OpenSpec's Key Innovation

**Problem with Full Specs:**
- Each feature creates new spec
- Modifications require rewriting
- No change history
- Merge conflicts when teams collaborate

**Delta Spec Solution:**
```markdown
## ADDED Requirements
## MODIFIED Requirements
## REMOVED Requirements
## RENAMED Requirements
```

**Benefits:**
- Changes are explicit
- History preserved
- Atomic operations
- Validation per operation

---

## 7. Extracted Learnings

### 7.1 What Kiro Does Well

1. **Comprehensive Documentation:** Detailed philosophy, methodology, templates
2. **EARS Format Mastery:** Thorough requirements engineering guidance
3. **Cognitive Load Management:** Clear phase separation
4. **AI-Specific Guidance:** Prompting strategies optimized for LLMs
5. **Steering Documents:** Flexible context injection system

### 7.2 What OpenSpec Does Well

1. **Dependency Graph Engine:** Prevents out-of-order operations
2. **Delta Specs:** Clean change management for evolving specs
3. **CLI Tooling:** Single source of truth for state
4. **Schema Customization:** Easy workflow modification
5. **Multi-Tool Support:** 20+ AI tool adapters

### 7.3 Combined Best Practices

| Best Practice | Source | Why It Works |
|---------------|--------|--------------|
| **EARS Format** | Kiro | Unambiguous, testable requirements |
| **Dependency Graph** | OpenSpec | Enforces correct order |
| **Steering Documents** | Kiro | Project-specific context injection |
| **Delta Specs** | OpenSpec | Clean change management |
| **Quality Checklists** | Kiro | Validation at each phase |
| **State Detection** | OpenSpec | Automatic progress tracking |
| **Guardrails Text** | OpenSpec | Explicit constraints for agents |
| **Schema-Driven** | OpenSpec | Customizable workflows |

### 7.4 Anti-Patterns to Avoid

| Anti-Pattern | Why It Fails |
|--------------|--------------|
| **Phase gates without enforcement** | Agents skip phases |
| **Full specs without delta** | Merge conflicts, no history |
| **Manual state tracking** | Drifts from reality |
| **Generic templates** | Poor AI output quality |
| **No validation** | Spec-reality divergence |

---

## 8. Optimized SDD Workflow for OpenCode

Based on the analysis, here is an optimized workflow combining the best of both systems.

### 8.1 Architecture

```
~/.config/opencode/
├── AGENTS.md                      # Global agent config
├── skill/
│   ├── sdd-spec-create/           # Create new spec
│   ├── sdd-spec-artefact/          # Create next artifact incrementally
│   ├── sdd-spec-apply/            # Implement spec
│   ├── sdd-spec-archive/          # Archive completed spec
│   ├── sdd-requirements/          # Requirements engineering
│   ├── sdd-design/                # Design documentation
│   └── sdd-tasks/                 # Task breakdown
├── commands/
│   ├── sdd-new.md                 # /sdd:new command
│   ├── sdd-artefact.md            # /sdd:artefact command
│   ├── sdd-apply.md               # /sdd:apply command
│   ├── sdd-status.md              # /sdd:status command
│   └── sdd-archive.md             # /sdd:archive command
└── snippets/
    └── sdd/                       # SDD snippets
        ├── ears-requirement.md
        ├── scenario-when-then.md
        ├── task-checklist.md
        └── design-decision.md
```

### 8.2 Core Skills

#### Skill 1: sdd-spec-create

```yaml
---
name: sdd-spec-create
description: Create a new spec-driven development specification. 
  Validates that no conflicting specs exist and scaffolds the 
  spec directory structure with proposal, specs, design, and tasks templates.
license: MIT
compatibility: OpenCode, Claude Code, Cursor
metadata:
  category: methodology
  complexity: beginner
---

# SDD Spec Create

Create a new specification for spec-driven development.

## Pre-Conditions
1. Verify `.specs/` directory exists (create if not)
2. Check for existing specs that might conflict
3. Identify if this is a NEW feature or MODIFICATION

## Process

### Step 1: Gather Context
Ask the user:
- What problem are you solving? (WHY)
- What capabilities are being added/changed? (WHAT)
- What's the scope? (in/out of bounds)

### Step 2: Create Proposal
Create `.specs/<spec-name>/proposal.md`:

```markdown
# Proposal: <name>

## Why
<problem statement>

## What Changes
<capability changes>

## Capabilities
### New Capabilities
- `<name>`: <description>

### Modified Capabilities
- `<existing-name>`: <what changes>

## Impact
<affected systems>

## Status
- [ ] Requirements: pending
- [ ] Design: pending
- [ ] Tasks: pending
```

### Step 3: Initialize Artifacts
Create empty placeholder files:
- `.specs/<spec-name>/specs/.gitkeep`
- `.specs/<spec-name>/design.md` (with template)
- `.specs/<spec-name>/tasks.md` (with template)

## Output
- Created spec directory at `.specs/<spec-name>/`
- Proposal document created
- Ready for `/sdd-artefact`
```

#### Skill 2: sdd-spec-artefact

```yaml
---
name: sdd-spec-artefact
description: Create the next artifact in a spec development process.
  Uses dependency detection to determine what's next. Creates ONE artifact 
  per invocation to maintain quality.
license: MIT
compatibility: OpenCode, Claude Code, Cursor
metadata:
  category: methodology
  complexity: intermediate
---

# SDD Spec Continue

Create the next artifact in the spec development process.

## Dependency Graph

```
proposal ──┬──► specs ──┬──► tasks
           │            │
           └──► design ─┘
```

## State Detection

Check filesystem for artifact status:
- **DONE**: File exists and has content
- **READY**: All dependencies are DONE
- **BLOCKED**: Missing dependencies

## Guardrails

1. Create ONE artifact per invocation
2. Always read dependency artifacts first
3. Never skip artifacts
4. If unclear, ask the user

## Process

### Step 1: Detect State
Read the spec directory and determine:
- Which artifacts are DONE
- Which artifacts are READY
- Which artifacts are BLOCKED

### Step 2: Select Next Artifact
If multiple READY:
1. specs (before design if both ready)
2. design
3. tasks

### Step 3: Create Artifact

#### For specs:
Create `.specs/<name>/specs/<capability>/spec.md`:

```markdown
## ADDED Requirements

### Requirement: <name>
The system SHALL <behavior>.

#### Scenario: <name>
- **WHEN** <condition>
- **THEN** <expected outcome>
```

#### For design:
Create/update `.specs/<name>/design.md`:

```markdown
# Design: <name>

## Context
## Goals / Non-Goals
## Decisions
## Risks / Trade-offs
## Migration Plan
```

#### For tasks:
Create/update `.specs/<name>/tasks.md`:

```markdown
## 1. <Section>

- [ ] 1.1 <task>
  - _Requirements: <refs>_
```

### Step 4: Update Proposal Status
Mark the artifact as complete in proposal.md.
```

#### Skill 3: sdd-spec-apply

```yaml
---
name: sdd-spec-apply
description: Implement a spec by working through tasks. Reads specs and design
  for context, executes tasks one at a time, marks them complete. Supports
  updating specs during implementation if gaps are found.
license: MIT
compatibility: OpenCode, Claude Code, Cursor
metadata:
  category: methodology
  complexity: intermediate
---

# SDD Spec Apply

Implement a specification by working through tasks.

## Pre-Conditions
1. Spec exists at `.specs/<name>/`
2. Tasks artifact is DONE (has tasks)

## Process

### Step 1: Load Context
Read in order:
1. `.specs/<name>/proposal.md` - WHY
2. `.specs/<name>/specs/**/*.md` - WHAT
3. `.specs/<name>/design.md` - HOW
4. `.specs/<name>/tasks.md` - STEPS

### Step 2: Find Next Task
Parse tasks.md for first unchecked task:
```markdown
- [ ] 1.1 Create module structure  ←-- This one
- [x] 1.2 Add dependencies         ←-- Already done
```

### Step 3: Implement Task
1. Understand task requirements
2. Identify affected files
3. Make minimal changes
4. Verify changes

### Step 4: Mark Complete
Update tasks.md:
```markdown
- [x] 1.1 Create module structure
```

### Step 5: Handle Gaps
If implementation reveals spec gaps:
1. UPDATE the spec (don't work around)
2. Document the change
3. Continue implementation

## Guardrails
- One task at a time
- Minimal changes
- Always reference requirements
- Update specs when gaps found
```

#### Skill 4: sdd-requirements

```yaml
---
name: sdd-requirements
description: Requirements engineering using EARS format. Creates unambiguous,
  testable requirements with WHEN/THEN/SHALL patterns. Includes validation
  checklists for completeness and consistency.
license: MIT
compatibility: OpenCode, Claude Code, Cursor
metadata:
  category: methodology
  complexity: beginner
---

# SDD Requirements Engineering

## EARS Format Patterns

### Ubiquitous Requirements
```
The system SHALL <behavior>
```

### Event-Driven Requirements
```
WHEN <event> THEN the system SHALL <response>
```

### State-Driven Requirements
```
WHILE <state> the system SHALL <behavior>
```

### Optional Requirements
```
WHERE <feature> is enabled, the system SHALL <behavior>
```

### Exception Requirements
```
WHEN <event> IF <condition> THEN the system SHALL <response>
```

## Validation Checklist

- [ ] All user roles identified
- [ ] Normal cases covered
- [ ] Edge cases covered
- [ ] Error cases covered
- [ ] No ambiguous terms (fast, good, user-friendly)
- [ ] Each requirement is testable
- [ ] No conflicting requirements
- [ ] SHALL/MUST used for mandatory behavior
```

### 8.3 Commands

#### /sdd:new

```markdown
---
name: sdd:new
description: Start a new spec-driven development specification
---

Create a new SDD spec for the feature or change I want to implement.

Follow the sdd-spec-create skill:
1. Ask me what I want to build
2. Check for existing/conflicting specs
3. Create the proposal document
4. Initialize the spec structure

After creating, remind me to use `/sdd:continue` to develop the spec.
```

#### /sdd:continue

```markdown
---
name: sdd:continue
description: Create the next artifact in a spec
---

Continue developing my current spec by creating the next ready artifact.

Follow the sdd-spec-artefact skill:
1. Detect which artifacts are DONE vs READY vs BLOCKED
2. Select the next ready artifact
3. Read dependencies for context
4. Create ONE artifact
5. Update status

Show me what you're creating and why it's next.
```

#### /sdd:apply

```markdown
---
name: sdd:apply
description: Implement tasks from a spec
---

Implement the next task from my spec.

Follow the sdd-spec-apply skill:
1. Load spec context (proposal, specs, design)
2. Find the next unchecked task
3. Implement it with minimal changes
4. Mark the task complete
5. Show progress

If I need to update the spec during implementation, help me do that.
```

#### /sdd:status

```markdown
---
name: sdd:status
description: Show the status of a spec
---

Analyze and report the status of my spec.

Check:
1. Which artifacts are DONE (exist with content)
2. Which artifacts are READY (dependencies complete)
3. Which artifacts are BLOCKED (missing dependencies)
4. Task completion percentage

Show me a visual status report.
```

#### /sdd:archive

```markdown
---
name: sdd:archive
description: Archive a completed spec
---

Archive my completed spec.

1. Verify all tasks are complete
2. Move spec to `.specs/archive/<name>/`
3. Create summary of what was delivered

Ask me to confirm before archiving.
```

### 8.4 Snippets

#### ears-requirement.md

```markdown
# EARS Requirement Template

### Requirement: <name>
The system SHALL <behavior>.

#### Scenario: <name>
- **WHEN** <condition>
- **THEN** <expected outcome>
```

#### scenario-when-then.md

```markdown
#### Scenario: <name>
- **WHEN** <event or condition>
- **THEN** <expected system behavior>
```

#### task-checklist.md

```markdown
## <section-number>. <section-name>

- [ ] <section-number>.1 <task description>
  - _Requirements: <requirement-name>_
```

#### design-decision.md

```markdown
### Decision: <title>

**Context:** <situation requiring decision>

**Options Considered:**
1. <option-1> - Pros: <benefits> / Cons: <drawbacks>
2. <option-2> - Pros: <benefits> / Cons: <drawbacks>

**Decision:** <chosen option>

**Rationale:** <why this was selected>
```

### 8.5 Directory Structure

```
<project>/
├── .specs/
│   ├── active/
│   │   └── <spec-name>/
│   │       ├── proposal.md
│   │       ├── specs/
│   │       │   └── <capability>/
│   │       │       └── spec.md
│   │       ├── design.md
│   │       └── tasks.md
│   └── archive/
│       └── <completed-spec>/
└── openspec/
    └── specs/
        └── <capability>/
            └── spec.md    # Accumulated specs
```

### 8.6 Integration with OpenCode

#### AGENTS.md Additions

```markdown
## SDD Workflow Preferences

### When Starting New Features
1. Use `/sdd:new` for features > 1 day effort
2. Use micro-spec template for < 1 day changes

### Spec-Driven Development Rules
- Never skip phases (requirements → design → tasks)
- Always use EARS format for requirements
- Maintain traceability (tasks → requirements)
- Update specs when implementation reveals gaps

### Steering Documents
Store in `.specs/steering/`:
- `project-standards.md` (always included)
- `tech-stack.md` (always included)
- `testing-strategy.md` (fileMatch: *.test.*)
```

---

## 9. Summary

### Key Insights

1. **Kiro excels at** comprehensive documentation, EARS format guidance, cognitive load management, and AI-specific prompting strategies.

2. **OpenSpec excels at** workflow enforcement via dependency graphs, delta specs for change management, CLI tooling, and schema customization.

3. **The optimal combination** uses:
   - OpenSpec's dependency graph and state detection for enforcement
   - Kiro's EARS format and quality checklists for content quality
   - Delta specs for change management
   - Steering documents for context injection

### Recommended Implementation Priority

1. **Phase 1:** Core skills (sdd-spec-create, sdd-spec-artefact, sdd-spec-apply)
2. **Phase 2:** Commands (/sdd-new, /sdd-artefact, /sdd-apply)
3. **Phase 3:** Snippets (EARS, scenarios, tasks)
4. **Phase 4:** Steering document integration
5. **Phase 5:** Delta spec validation

### Success Metrics

| Metric | Target |
|--------|--------|
| Specs completed without major rework | > 80% |
| Tasks completed per spec session | 3-5 |
| Spec-reality divergence rate | < 10% |
| Time from proposal to implementation | < 2 days |
