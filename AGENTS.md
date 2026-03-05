# OpenCode Agent Configuration

## Project: SDD Workflow for OpenCode

This project provides a Spec-Driven Development (SDD) workflow implementation for OpenCode with optional SCL (Structured Cognitive Loop) enhancements.

---

## Available Agents

This project includes specialized subagents for design document creation. These agents are configured per [OpenCode standards](https://opencode.ai/docs/agents/).

### SDD Design Agent

- **File:** `agents/sdd-design.md`
- **Usage:** `@sdd-design`
- **Purpose:** Standard design document creation with 3-iteration review loop
- **Mode:** Subagent
- **Temperature:** 0.3
- **Features:**
  - Analyzes codebase to detect tech stack, patterns, and conventions
  - Creates comprehensive design documents with Mermaid diagrams
  - Documents decisions with alternatives and rationale
  - Respects prior context (decisions, preferences, Q&A)
  - 3-iteration review loop with sdd-design-analyst
  - Critique reports saved for traceability

**Invoke:** `@sdd-design <context>`

**Note:** This agent automatically invokes `sdd-design-analyst` for 3 review iterations before finalizing design.

### SDD Design Analyst

- **File:** `agents/sdd-design-analyst.md`
- **Usage:** `@sdd-design-analyst`
- **Purpose:** Brutally honest design critic for logical flaws, structural issues, and coverage gaps
- **Mode:** Subagent
- **Temperature:** 0.7 (higher for critical thinking)
- **Features:**
  - Analyzes designs for logical consistency
  - Checks structural completeness
  - Verifies requirement coverage
  - Identifies design anti-patterns
  - Produces structured critique reports with severity levels
  - Tracks issues across iterations

**Invoke:** Automatically during sdd-design review loop

**Note:** This agent is automatically invoked by the design agents during the 3-iteration review loop.

### SDD Design Agent (SCL-Enhanced)

- **File:** `agents/sdd-design-scl.md`
- **Usage:** `@sdd-design-scl`
- **Purpose:** Memory-integrated design with evidence tracking, 3-iteration review loop, citation validation, and control checkpoints
- **Mode:** Subagent
- **Temperature:** 0.2
- **Features:**
  - 6-phase SCL workflow (Retrieve → Cognition → Control → Review Loop → Action → Memory Update)
  - 3-iteration review loop with analyst critique
  - Memory persistence across artifact creation
  - Evidential grounding - all claims cite sources
  - Citation validation and consistency checks
  - RFC2119 compliance (MUST/SHOULD/MAY)
  - Automatic memory state updates

**Invoke:** `@sdd-design-scl <context>`

**Note:** This agent automatically invokes `sdd-design-analyst` for 3 review iterations before finalizing design.

### Agent Configuration

All design agents have the following configuration:
- **Mode:** `subagent` (invoked via `@` mention or Task tool)
- **Tools:** Full access to glob, grep, read, write, edit, bash, task
- **Permissions:** Full write/edit access, unrestricted bash
- **Scope:** Constrained to project files (see scope constraints in agent files)
- **Review Loop:** All designs go through 3-iteration review with analyst

---

## SCL-Enhanced Workflow (RECOMMENDED)

The SCL-enhanced workflow provides superior reliability through:
- **Memory persistence** across artifact creation and task execution
- **Evidential grounding** - all claims MUST cite sources
- **Normative control** - explicit validation before action
- **Scope enforcement** - subagents constrained to allowed files

### SCL Core Principles (RFC2119)

1. The system **MUST** maintain memory state in `.memory/` directory
2. Every requirement **MUST** cite its source
3. Every decision **MUST** document alternatives considered
4. Every task **MUST** reference at least one requirement
5. The system **MUST** validate before executing actions

### SCL Commands

The SCL-enhanced workflow starts with exploration, then creates a proposal with context preservation:

```
# Exploration & Planning
/sdd-explore [name]          # Explore idea, create context-log
/sdd-propose <name>          # Create proposal from context-log

# Memory Initialization
/sdd-init-memory             # Initialize memory + harvest from proposal

# Create artifacts with memory tracking
/sdd-artefact-scl

# Execute tasks with memory context
/sdd-apply-group-scl N
/sdd-apply-all-scl

# Verify with memory tracing
/sdd-verify-scl

# Inspect memory state
/sdd-memory-status [change-name]
```

### SCL Directory Structure

```
.specs/changes/<change-name>/
├── context-log.md            # Exploration context (Q&A, goals, constraints)
├── proposal.md               # Formal proposal with Context Log section
├── specs/<capability>/spec.md
├── design.md
├── tasks.md
├── .memory/                    # SCL Memory Module
│   ├── decisions.json          # All decisions with evidence
│   ├── requirements.json       # Requirement index (harvested from proposal)
│   ├── citations.json          # Citation graph
│   ├── control-log.json        # Validation checkpoints
│   └── episodes.json           # Cycle-by-cycle history
└── regulation.md               # Epistemic Constitution
```

### Regulation.md (Epistemic Constitution)

Every SCL-enhanced change **MUST** include a `regulation.md` defining:
- Evidential rules (how to cite sources)
- Scope rules (what files may be modified)
- Validation rules (how completion is verified)
- Memory rules (how state is maintained)

---

## Standard SDD Workflow (Legacy)

### When to Use SDD

**Use SDD for:**
- Features requiring > 1 day of work
- Multiple components or integrations
- High-stakes changes where rework is costly
- Complex features with unclear requirements

**Use micro-spec for:**
- Changes < 1 day of work
- Simple bug fixes with obvious solutions
- Single component modifications

**Skip SDD for:**
- Trivial changes (typo fixes, config updates)
- Well-established patterns with minimal ambiguity
- Time-critical hotfixes

### Core SDD Principles

1. **Clarity Before Code** - Write specs before implementation
2. **Single Source of Truth** - `.specs/specs/` contains current state
3. **Delta Specs** - Changes use ADDED/MODIFIED/REMOVED format
4. **Verification** - Verify implementation matches spec before archiving

---

## Directory Structure

```
.specs/
├── specs/            # Accumulated specs (single source of truth)
│   └── <module>/
│       ├── proposal.md
│       └── specs/<capability>/spec.md
├── changes/          # Active changes in progress
│   └── <change-name>/
│       ├── proposal.md
│       ├── specs/<capability>/spec.md
│       ├── design.md
│       └── tasks.md
└── archive/          # Completed changes (history)
    └── YYYY-MM-DD-<change-name>/
        └── SUMMARY.md
```

**Key principle:** `.specs/specs/` is always current. Archive merges deltas into it.

---

## Workflow Commands

### Starting New Work

```
# New feature (SCL-enhanced - RECOMMENDED)
/sdd-explore [name]     # Explore idea, create context-log
/sdd-propose <name>     # Create proposal from context-log
/sdd-init-memory        # Initialize memory + harvest knowledge
/sdd-artefact-scl       # Create artifacts with memory tracking

# Existing codebase (brownfield)
/sdd-reverse src/<module>/
```

### Developing Specs

```
/sdd-artefact      # Create next artifact incrementally
/sdd-artefact-scl  # Create with memory tracking (SCL)
/sdd-ff            # Fast-forward all artifacts at once
/sdd-status        # Check current progress
```

### Implementation

```
/sdd-apply              # One task at a time
/sdd-apply-group N      # Execute group N via subagent
/sdd-apply-group-scl N  # Execute with memory context (SCL)
/sdd-apply-all          # Execute all groups via subagents
/sdd-apply-all-scl      # Execute all with memory context (SCL)
```

### Completion

```
/sdd-verify        # Verify implementation matches spec
/sdd-verify-scl    # Verify with memory tracing (SCL)
/sdd-archive       # Merge deltas and archive
```

---

## Agent Behavior Rules

### Before Starting Work

1. **Check for existing specs** - Look in `.specs/specs/` for related capabilities
2. **Ask about scope** - Is this a new capability or modifying existing?
3. **Choose appropriate workflow** - Full spec, micro-spec, or skip

### During Spec Creation

1. **Use EARS format** for requirements:
   ```
   WHEN <event> THEN system SHALL <response>
   IF <condition> THEN system SHALL <response>
   ```

2. **Reference existing specs** when modifying:
   ```markdown
   ### Modified Capabilities
   - `authentication`: Adding 2FA
     - Existing: specs/auth/authentication/spec.md
   ```

3. **Create delta specs** for changes:
   ```markdown
   ## MODIFIED Requirements
   ## ADDED Requirements
   ## REMOVED Requirements
   ```

### During Implementation

1. **One task at a time** by default (use `/sdd-apply`)
2. **Reference requirements** in code comments when helpful
3. **Update specs** if gaps found (don't workaround)
4. **Mark tasks complete** immediately after finishing

### Before Archiving

1. **Run verification** - Ensure implementation matches spec
2. **Check all tasks** complete or intentionally skipped
3. **Create summary** documenting what was delivered

---

## Task Group Execution

When using `/sdd-apply-group` or `/sdd-apply-all`:

### Group Metadata Format

```markdown
## 1. Setup
_Meta: sequential, foundation_

## 2. Core Services
_Meta: parallel-safe, depends on: 1_

## 3. API Routes
_Meta: sequential, depends on: 2_
```

### Subagent Scope Constraints

When dispatching subagents for task groups:

1. **Explicit task list** - "Your tasks: 2.1, 2.2, 2.3 ONLY"
2. **File permissions** - "Only modify: src/auth/services/*"
3. **Required completion signal** - Must output "GROUP N COMPLETE"
4. **Stop conditions** - "DO NOT start next group"

---

## Spec Format Standards

### Requirements

```markdown
### Requirement: <name>
The system SHALL <specific behavior>.

#### Scenario: <name>
- **WHEN** <condition>
- **THEN** <expected outcome>
```

### Design Decisions

```markdown
### Decision: <title>
**Context:** <situation>
**Options Considered:**
1. <Option> - Pros: <...> / Cons: <...>
**Decision:** <chosen>
**Rationale:** <why>
```

### Tasks

```markdown
## 1. Setup
_Meta: sequential, foundation_

- [ ] 1.1 <Task description>
  - _Requirements: <ref>_
  - _Creates: <path>_
```

---

## Verification Rules

### Before Archive

Always verify:
- [ ] All requirements have corresponding code
- [ ] All scenarios are handled
- [ ] Tests exist for critical paths
- [ ] No critical gaps remain

### Critical vs Non-Critical Gaps

**Critical (must fix):**
- Core requirement not implemented
- Breaking behavior changes unspecified
- Security-related requirements missing

**Non-Critical (can acknowledge):**
- Nice-to-have features
- Performance optimizations
- Future enhancements

---

## Error Handling

### Spec-Reality Divergence

If implementation reveals spec gaps:
1. **STOP** - Don't workaround
2. **UPDATE** the spec with new understanding
3. **DOCUMENT** why change was needed
4. **CONTINUE** with implementation

### Verification Failures

If verification fails before archive:
1. Show specific gaps
2. Offer to implement missing pieces
3. Allow acknowledge-and-proceed for non-critical
4. Block archive for critical gaps

---

## Integration with OpenCode

### Installation

```bash
cp -r skill/sdd-* ~/.config/opencode/skill/
cp commands/sdd-*.md ~/.config/opencode/commands/
```

### Project Setup

```bash
mkdir -p .specs/specs .specs/changes .specs/archive
```

### First Time on Brownfield

```bash
/sdd-reverse src/ --depth=medium
# Creates .specs/specs/ with extracted capabilities
```

---

## Quick Reference

**Standard SDD:**
| Phase | Command | Output |
|-------|---------|--------|
| Start | `/sdd-reverse` | Baseline specs from code |
| Develop | `/sdd-artefact` | specs, design, tasks |
| Implement | `/sdd-apply` | Code + completed tasks |
| Verify | `/sdd-verify` | Verification report |
| Archive | `/sdd-archive` | Merged to `.specs/specs/` |

**SCL-Enhanced (Recommended):**
| Phase | Command | Output |
|-------|---------|--------|
| Explore | `/sdd-explore` | context-log.md |
| Plan | `/sdd-propose` | proposal.md with Context Log |
| Init Memory | `/sdd-init-memory` | .memory/ + harvested knowledge |
| Develop | `/sdd-artefact-scl` | specs, design, tasks with memory |
| Implement | `/sdd-apply-group-scl` | Code + memory updates |
| Verify | `/sdd-verify-scl` | Verification with memory tracing |
| Archive | `/sdd-archive` | Merged to `.specs/specs/` |

---

## Philosophy

### Clarity Before Code

> Ambiguity in requirements leads to wasted implementation effort.
> Write specs first, implement second.

### Single Source of Truth

> `.specs/specs/` is always current.
> Archive merges deltas; specs never drift.

### Verification Matters

> A spec not verified is just a wish.
> Check implementation matches spec before claiming done.

---

## SCL-Enhanced Task Format

When using SCL-enhanced workflow, tasks **MUST** include:

```markdown
- [ ] N.M <Task description>
  - _Requirements: REQ-ID (per specs/capability/spec.md#L<N>)_
  - _Evidence: design.md#decision-name_
  - _Creates: path/to/file.ts_ | _Modifies: path/to/file.ts_
  - _Validation: <testable criteria>_
  - _Memory Write: requirements.json#REQ-ID.status ← "implemented"_
```

### Task Group with SCL Context

```markdown
## 2. Core Implementation
_Meta: parallel-safe, depends on: 1_

### Preconditions
- [ ] Group 1 complete
- [ ] Required files exist

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
- [ ] 2.1 Implement password hashing
  ...
```

---

## Subagent Context Injection (SCL)

When dispatching subagents in SCL mode, the following context **MUST** be injected:

### Required Context Components

1. **Decisions** - Relevant design decisions with sources
2. **Requirements** - Requirements for the task group
3. **Prior Outcomes** - What was done in previous groups
4. **Constraints** - Allowed/blocked files, required citations
5. **Regulation** - Applicable rules from regulation.md

### Subagent Prompt Template

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

## Mitigation of Separate Context Limitations

SCL addresses the fundamental limitation of subagent context isolation:

### Problem: Context Isolation

Subagents operate in isolated contexts and cannot:
- Access decisions made in prior groups
- Know what files were created previously
- Understand the reasoning behind design choices

### SCL Solution: External Memory

1. **Before dispatch**: Load memory state, inject into prompt
2. **During execution**: Subagent has full context from memory
3. **After completion**: Write outcomes back to memory

### Memory Operations

| Operation | Purpose | Timing |
|-----------|---------|--------|
| `MEM.read()` | Load prior decisions, requirements | Before subagent dispatch |
| `MEM.write()` | Record new decisions, citations | After artifact creation |
| `CONTROL.evaluate()` | Validate proposals | Before action execution |
| `CONTROL.verify_scope()` | Check file boundaries | After subagent completion |

### Example Memory Context

```json
{
  "group_id": 2,
  "memory": {
    "decisions": [
      {"id": "DEC-001", "chosen": "JWT", "source": "design.md#L78"}
    ],
    "requirements": [
      {"id": "AUTH-001", "description": "Passwords SHALL be hashed"}
    ],
    "prior_outcomes": {
      "files_created": ["src/models/User.ts"],
      "decisions_made": ["Use interface over class"]
    }
  },
  "constraints": {
    "allowed_files": ["src/auth/**/*.ts"],
    "blocked_files": ["src/core/*"]
  }
}
```

---

## RFC2119 Compliance

All SCL-enhanced artifacts, commands, and skills use RFC2119 keywords:

| Keyword | Meaning |
|---------|---------|
| **MUST** / **REQUIRED** / **SHALL** | Absolute requirement |
| **MUST NOT** / **SHALL NOT** | Absolute prohibition |
| **SHOULD** / **RECOMMENDED** | Recommended but exceptions may exist |
| **SHOULD NOT** / **NOT RECOMMENDED** | Not recommended but exceptions may exist |
| **MAY** / **OPTIONAL** | Truly optional |

### When to Use Each Keyword

- **MUST**: For requirements critical to correctness, traceability, memory integrity
- **SHOULD**: For best practices that improve quality but have valid exceptions
- **MAY**: For optional features or alternative approaches

### Examples in Artifacts

```markdown
### Requirement: AUTH-001
The system **MUST** hash passwords using bcrypt with cost factor >= 10.

### Design Decision: DEC-002
The system **SHOULD** use JWT for session management.
Alternatives: Redis sessions, database sessions.

### Task: 2.1
The implementation **MAY** include additional password strength checks
beyond the minimum requirements.
```
