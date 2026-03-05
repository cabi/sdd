---
name: sdd-spec-create
description: Create a new spec-driven development specification. Validates that no conflicting specs exist and scaffolds the spec directory structure with proposal, specs, design, and tasks templates.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: beginner
  author: OpenCode
  version: "1.0.0"
---

# SDD Spec Create

Create a new specification for spec-driven development.

## When to Use

- Starting a new feature requiring > 1 day of work
- Multiple components or integrations needed
- High-stakes changes where rework is costly
- Team collaboration requiring shared understanding

## When NOT to Use

- Simple bug fixes with obvious solutions
- Experimental prototypes
- Time-critical hotfixes
- Well-established patterns with minimal ambiguity (use micro-spec instead)

## Pre-Conditions

1. Verify `.specs/` directory exists (create if not)
2. Check for existing specs that might conflict
3. Identify if this is a NEW feature or MODIFICATION

## Process

### Step 1: Gather Context

Ask the user these questions:

1. **What problem are you solving?** (WHY)
   - What's the pain point or opportunity?
   - Why is this needed now?

2. **What capabilities are being added/changed?** (WHAT)
   - New functionality being introduced?
   - Existing behavior being modified?
   - Anything being removed?

3. **What's the scope?** (BOUNDARIES)
   - What's explicitly IN scope?
   - What's explicitly OUT of scope?
   - Any constraints or dependencies?

### Step 2: Check for Existing Specs

Before creating, scan `.specs/specs/` for:
- Similar capability names that already exist
- Related specs that this change might modify

If related specs found:
```
Found existing specs that may be relevant:
┌─────────────────┬─────────────────────────────┐
│ Capability      │ Location                    │
├─────────────────┼─────────────────────────────┤
│ authentication  │ specs/auth/authentication/  │
│ session-mgmt    │ specs/auth/session-mgmt/    │
└─────────────────┴─────────────────────────────┘

Is this change:
[1] Modifying existing capability (creates delta spec)
[2] Completely new capability
[3] Let me describe first
```

### Step 3: Create Change Directory

Create: `.specs/changes/<spec-name>/`

Naming convention: Use kebab-case describing the change (e.g., `add-two-factor`, `fix-login-bug`, `add-sso`)

### Step 4: Create Proposal

Create `.specs/changes/<spec-name>/proposal.md` with structured sections for harvesting:

```markdown
# Proposal: <spec-name>

## Context Log
<!-- Transferred from context-log.md or created during interview -->
<Full Q&A history from exploration>

## Goals
<!-- What we're trying to achieve (not HOW) -->
- Goal 1: <description>
- Goal 2: <description>

## Constraints
<!-- What limits our design choices -->

### Technical Constraints
- <constraint>: <reason>

### Business Constraints
- <constraint>: <reason>

### External Constraints
- <constraint>: <reason>

## What Changes
<Bullet list of changes. Be specific about new capabilities, modifications, or removals. Mark breaking changes with **BREAKING**.>

## Capabilities

### New Capabilities
<List capabilities being introduced. Each becomes a specs/<name>/spec.md. Use kebab-case names.>
- `<capability-name>`: <brief description>

### Modified Capabilities
<Existing capabilities whose REQUIREMENTS are changing. Reference existing spec location.>
- `<existing-name>`: <what requirement is changing>
  - Existing spec: specs/<module>/<capability>/spec.md

## Impact
<Affected code, APIs, dependencies, or systems. Who needs to know about this change?>

## Scope

### In Scope
- <item>

### Out of Scope
- <item>

## Exploration Notes
<!-- Optional: Options considered but not decided, domain knowledge, risks -->
- Option: <description>
  _Pros: <...>_
  _Cons: <...>_

## Status
- [ ] Requirements: pending
- [ ] Design: pending
- [ ] Tasks: pending

---
Created: <date>
Source: context-log.md (if applicable)
```

### Step 5: Initialize Artifacts

Create placeholder files:

**specs/.gitkeep**
```
# Delta specs will be created here
```

**design.md**
```markdown
# Design: <spec-name>

> This document will be created during the design phase.
> Use `/sdd-artefact` to proceed.

## Context
_To be filled_

## Goals / Non-Goals
_To be filled_

## Decisions
_To be filled_

## Risks / Trade-offs
_To be filled_
```

**tasks.md**
```markdown
# Tasks: <spec-name>

> This document will be created during the tasks phase.
> Use `/sdd-artefact` to proceed.

## 1. Setup
_To be filled_
```

## Output

After completion, inform the user:

```
✓ Created change at .specs/changes/<spec-name>/
✓ Proposal document created
✓ Artifact placeholders initialized

Next steps:
1. Review the proposal
2. Use `/sdd-init-memory` to create memory structure and harvest knowledge
3. Use `/sdd-artefact-scl` to create requirements (specs)
4. Use /sdd-status to check progress anytime
```

## Harvesting from Proposal

The proposal template is structured for easy knowledge extraction by `/sdd-init-memory`:

| Section | Target | Type | Extraction |
|---------|--------|------|-----------|
| Goals | requirements.json | functional | "Goal X" → REQ-FUNC-NNN |
| Constraints | requirements.json | constraint | "Constraint X" → REQ-CONST-NNN |
| Context Log | episodes.json | exploration | Q&A pairs → exploration episodes |
| Exploration Notes | episodes.json | options | Options/risks → judgments |

**Why This Structure Matters:**

- **Goals** define WHAT we're achieving (design decides HOW)
- **Constraints** limit design choices (must be respected)
- **Context Log** preserves exploration reasoning (why we're doing this)
- **Exploration Notes** capture options considered (not decided yet)

The design phase will populate `decisions.json` with technical choices made within these constraints.

## Directory Structure

```
.specs/
├── specs/                     # Accumulated specs (single source of truth)
│   └── auth/
│       ├── authentication/spec.md
│       └── registration/spec.md
├── changes/                   # Active changes in progress
│   └── <change-name>/
│       ├── proposal.md
│       ├── specs/
│       ├── design.md
│       └── tasks.md
└── archive/                   # Completed changes (history)
    └── 2026-02-21-<change-name>/
        └── SUMMARY.md
```
