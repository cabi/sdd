# SDD Workflow for OpenCode

Spec-Driven Development (SDD) workflow implementation for reliable multi-agent task execution.

## Overview

### What is SDD?

Spec-Driven Development emphasizes **clarity before code**. By writing specifications before implementation, you:

- Reduce ambiguity and rework
- Create better AI collaboration context
- Build traceable, maintainable software
- Document decisions as you go

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

# Copy templates
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

### Commands (12)

**Create Phase:**

| Command | Purpose |
|---------|---------|
| `/sdd-new` | Start new spec |
| `/sdd-init` | Initialize project structure |

**Develop Phase:**

| Command | Purpose |
|---------|---------|
| `/sdd-artefact` | Create next artifact |
| `/sdd-ff` | Fast-forward all artifacts |
| `/sdd-explore` | Think before committing | No |
| `/sdd-status` | Check progress | No |
| `/sdd-reverse` | Extract specs from code | No |

**Implement Phase:**

| Command | Purpose |
|---------|---------|
| `/sdd-apply` | Implement one task |
| `/sdd-apply-group N` | Execute group N |
| `/sdd-apply-all` | Execute all groups |

**Verify & Complete:**

| Command | Purpose |
|---------|---------|
| `/sdd-verify` | Verify implementation |
| `/sdd-archive` | Complete and archive |

### Skills (14)

| Skill | Purpose |
|-------|---------|
| `sdd-spec-create` | Create proposal.md |
| `sdd-spec-artefact` | Create specs/design/tasks |
| `sdd-spec-apply` | Implement tasks from spec |
| `sdd-spec-archive` | Archive completed specs |
| `sdd-requirements` | EARS format requirements guide |
| `sdd-design` | Technical design documentation |
| `sdd-design-review` | 5-iteration design review loop |
| `sdd-tasks` | Task breakdown and sequencing |
| `sdd-task-review` | 3-iteration task review loop |
| `sdd-testing` | Test strategy & traceability |
| `sdd-reverse` | Extract specs from existing code |
| `sdd-verify` | Verify implementation matches specs |
| `sdd-interview` | Structured exploration Q&A |

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

---

## Next Steps

- **[Standard SDD Workflow](docs/howto/sdd-workflow.md)** - How to use the standard workflow
- **[SDD Methodology](docs/concepts/sdd-methodology.md)** - Understanding SDD principles
- **[Commands Reference](docs/reference/commands.md)** - Detailed command documentation
- **[Skills Reference](docs/reference/skills.md)** - Detailed skill documentation
