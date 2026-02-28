# SDD Workflow for OpenCode

Spec-Driven Development (SDD) workflow implementation with optional SCL (Structured Cognitive Loop) enhancements for reliable multi-agent task execution.

## Overview

### What is SDD?

Spec-Driven Development emphasizes **clarity before code**. By writing specifications before implementation, you:

- Reduce ambiguity and rework
- Create better AI collaboration context
- Build traceable, maintainable software
- Document decisions as you go

### What is SCL?

SCL (Structured Cognitive Loop) addresses a fundamental limitation: **subagents operate in isolated contexts** and cannot access decisions made in prior groups. SCL provides:

- **Memory persistence** across artifact creation and task execution
- **Evidential grounding** - all claims cite sources (RFC2119)
- **Normative control** - explicit validation before action
- **Scope enforcement** - subagents constrained to allowed files

### When to Use Which

| Aspect | Standard SDD | SCL-Enhanced |
|--------|--------------|--------------|
| Task execution | Single agent | Multi-agent/parallel |
| Complexity | Low to medium | High |
| Traceability | Basic | Full audit trail |
| Overhead | Minimal | Memory management |

---

## Installation

### Prerequisites

- OpenCode CLI installed and working

Vanilla OpenCode includes all required tools (Read, Write, Edit, Bash, Glob, Grep, Task, WebFetch, Question).

### Install SDD Workflow

```bash
# Create directories if needed
mkdir -p ~/.config/opencode/skill ~/.config/opencode/commands

# Copy skills
cp -r skills/sdd-* ~/.config/opencode/skill/

# Copy commands
cp commands/sdd-*.md ~/.config/opencode/commands/

# Copy templates (for SCL)
cp -r templates/ ~/.config/opencode/templates/
```

### Verify Installation

```
> /sdd-new
```

If the command is recognized, installation was successful.

### Project Setup

```bash
# In your project root
mkdir -p .specs/specs .specs/changes .specs/archive
```

---

## Quick Reference

### Commands (19)

**Create Phase:**

| Command | Purpose | SCL |
|---------|---------|-----|
| `/sdd-new` | Start new spec | No |
| `/sdd-init` | Initialize project structure | No |
| `/sdd-init-scl` | Initialize with SCL structure | Yes |
| `/sdd-init-memory <name>` | Initialize memory for change | Yes |

**Develop Phase:**

| Command | Purpose | SCL |
|---------|---------|-----|
| `/sdd-artefact` | Create next artifact | No |
| `/sdd-artefact-scl` | Create with memory tracking | Yes |
| `/sdd-ff` | Fast-forward all artifacts | No |
| `/sdd-explore` | Think before committing | No |
| `/sdd-status` | Check progress | No |
| `/sdd-reverse` | Extract specs from code | No |

**Implement Phase:**

| Command | Purpose | SCL |
|---------|---------|-----|
| `/sdd-apply` | Implement one task | No |
| `/sdd-apply-group N` | Execute group N | No |
| `/sdd-apply-all` | Execute all groups | No |
| `/sdd-apply-group-scl N` | Execute with memory context | Yes |
| `/sdd-apply-all-scl` | All groups with persistence | Yes |

**Verify & Complete:**

| Command | Purpose | SCL |
|---------|---------|-----|
| `/sdd-verify` | Verify implementation | No |
| `/sdd-verify-scl` | Verify with memory tracing | Yes |
| `/sdd-memory-status <name>` | Inspect memory state | Yes |
| `/sdd-archive` | Complete and archive | No |

### Skills (13)

| Skill | Purpose |
|-------|---------|
| `sdd-spec-create` | Create proposal.md |
| `sdd-spec-artefact` | Create specs/design/tasks |
| `sdd-spec-apply` | Implement tasks from spec |
| `sdd-spec-archive` | Archive completed specs |
| `sdd-requirements` | EARS format requirements guide |
| `sdd-design` | Technical design documentation |
| `sdd-tasks` | Task breakdown and sequencing |
| `sdd-reverse` | Extract specs from existing code |
| `sdd-verify` | Verify implementation matches specs |
| `sdd-memory` | Memory JSON schemas for SCL |
| `sdd-control` | Control/validation module for SCL |
| `sdd-artefact-scl` | SCL-enhanced artifact creation |
| `sdd-tasks-scl` | SCL-enhanced task breakdown |

---

## Directory Structure

### Standard SDD

```
.specs/
├── specs/                      # Accumulated specs (single source of truth)
│   └── <capability>/
│       ├── proposal.md
│       └── specs/<sub-capability>/spec.md
├── changes/                    # Active changes in progress
│   └── <change-name>/
│       ├── proposal.md
│       ├── specs/<capability>/spec.md
│       ├── design.md
│       └── tasks.md
└── archive/                    # Completed changes
    └── YYYY-MM-DD-<change-name>/
        └── SUMMARY.md
```

### SCL-Enhanced

```
.specs/changes/<change-name>/
├── proposal.md
├── specs/<capability>/spec.md
├── design.md
├── tasks.md
├── .memory/                    # SCL Memory Module
│   ├── decisions.json          # All decisions with evidence
│   ├── requirements.json       # Requirement index
│   ├── citations.json          # Citation graph
│   ├── control-log.json        # Validation checkpoints
│   └── episodes.json           # Cycle-by-cycle history
└── regulation.md               # Epistemic Constitution
```

---

## Next Steps

- **[Standard SDD Workflow](docs/howto/sdd-workflow.md)** - How to use the standard workflow
- **[SCL-Enhanced Workflow](docs/howto/scl-workflow.md)** - How to use the SCL workflow
- **[SDD Methodology](docs/concepts/sdd-methodology.md)** - Understanding SDD principles
- **[SCL Architecture](docs/concepts/scl-architecture.md)** - Understanding SCL design
- **[Commands Reference](docs/reference/commands.md)** - Detailed command documentation
- **[Skills Reference](docs/reference/skills.md)** - Detailed skill documentation
