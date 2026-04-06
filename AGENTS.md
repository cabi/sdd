# OpenCode Agent Configuration

## Project: SDD Workflow for OpenCode

This project provides a Spec-Driven Development (SDD) workflow implementation for OpenCode.

---

## Available Agents

This project includes specialized subagents for design document and task creation. These agents are configured per [OpenCode standards](https://opencode.ai/docs/agents/).

### SDD Design Agent

- **File:** `agents/sdd-design.md`
- **Usage:** `@sdd-design`
- **Purpose:** Design document creation and revision
- **Mode:** Subagent
- **Temperature:** 0.9
- **Features:**
  - Analyzes codebase to detect tech stack, patterns, and conventions
  - Creates comprehensive design documents with Mermaid diagrams
  - Documents decisions with alternatives and rationale
  - Respects prior context (decisions, preferences, Q&A)
  - Two modes: `create` (initial design) and `revise` (apply critique)
  - Reads/writes design.md and review files autonomously

**Invoke:** `@sdd-design` with CHANGE_DIR and MODE parameters

**Note:** This agent does NOT run the review loop itself. The command orchestrator (`/sdd-artefact`) alternates between this agent and `sdd-design-analyst` for 5 iterations.

### SDD Design Analyst

- **File:** `agents/sdd-design-analyst.md`
- **Usage:** `@sdd-design-analyst`
- **Purpose:** Brutally honest design critic for logical flaws, structural issues, and coverage gaps
- **Mode:** Subagent
- **Temperature:** 1.0 (higher for critical thinking)
- **Features:**
  - Analyzes designs for logical consistency
  - Checks structural completeness
  - Verifies requirement coverage
  - Identifies design anti-patterns
  - Produces structured critique reports with severity levels
  - Tracks issues across iterations

**Invoke:** Automatically during design review loop (by `/sdd-artefact` command)

**Note:** This agent is invoked by the command orchestrator during the 5-iteration design review loop.

### SDD Task Agent

- **File:** `agents/sdd-task.md`
- **Usage:** `@sdd-task`
- **Purpose:** Task breakdown creation and revision
- **Mode:** Subagent
- **Temperature:** 0.9
- **Features:**
  - Analyzes design and specs to extract components, decisions, and requirements
  - Analyzes codebase to detect project structure and existing files
  - Creates comprehensive task breakdowns with proper grouping and sizing
  - Ensures 100% requirement and design element coverage
  - Two modes: `create` (initial tasks) and `revise` (apply critique)
  - Reads/writes tasks.md and review files autonomously

**Invoke:** `@sdd-task` with CHANGE_DIR and MODE parameters

**Note:** This agent does NOT run the review loop itself. The command orchestrator (`/sdd-artefact`) alternates between this agent and `sdd-task-analyst` for 3 iterations.

### SDD Task Analyst

- **File:** `agents/sdd-task-analyst.md`
- **Usage:** `@sdd-task-analyst`
- **Purpose:** Brutally honest task critic for actionability, sizing, dependency, and coverage issues
- **Mode:** Subagent
- **Temperature:** 1.0 (higher for critical thinking)
- **Features:**
  - Analyzes tasks for actionability and clarity
  - Checks task sizing (2-4 hour chunks)
  - Verifies dependency graph correctness (no cycles, accurate edges)
  - Checks requirement coverage (every spec requirement has a task)
  - Checks design coverage (every component/decision has a task)
  - Identifies missing task types (testing, error handling, migration)
  - Produces structured critique reports with severity levels
  - Tracks issues across iterations

**Invoke:** Automatically during task review loop (by `/sdd-artefact` command)

**Note:** This agent is invoked by the command orchestrator during the 3-iteration task review loop.

### Agent Configuration

All agents have the following configuration:
- **Mode:** `subagent` (invoked via `@` mention or Task tool)
- **Tools:** Full access to glob, grep, read, write, edit, bash (creator agents); read + write (analyst agents)
- **Permissions:** Full write/edit access, unrestricted bash (analyst agents are read-only except review files)
- **Scope:** Constrained to project files (see scope constraints in agent files)
- **Modes:** Creator agents support `create` (initial) and `revise` (apply critique) modes
- **Review Loop:** Orchestrated by `/sdd-artefact` command — alternates between creator and analyst agents
- **Interface:** Agents receive CHANGE_DIR, MODE, and ITERATION parameters; they read/write files autonomously

---

## SDD Workflow

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
# New feature
/sdd-explore [name]     # Explore idea, create context-log
/sdd-propose <name>     # Create proposal from context-log

# Existing codebase (brownfield)
/sdd-reverse src/<module>/
```

### Developing Specs

```
/sdd-artefact      # Create next artifact incrementally
/sdd-ff            # Fast-forward all artifacts at once
/sdd-status        # Check current progress
```

### Implementation

```
/sdd-apply              # One task at a time
/sdd-apply-group N      # Execute group N via subagent
/sdd-apply-all          # Execute all groups via subagents
```

### Completion

```
/sdd-verify        # Verify implementation matches spec
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

## RFC2119 Compliance

All SDD artifacts, commands, and skills use RFC2119 keywords:

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
