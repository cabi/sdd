# Standard SDD Workflow

How to use the Spec-Driven Development workflow for systematic feature development.

## When to Use

Use the standard SDD workflow for:

- Features requiring > 1 day of work
- Multiple components or integrations
- High-stakes changes where rework is costly
- Complex features with unclear requirements

For simpler changes (< 1 day, single component), consider using a micro-spec approach.

---

## Workflow Overview

```
CREATE → DEVELOP → IMPLEMENT → VERIFY → ARCHIVE
```

| Phase | Commands | Output |
|-------|----------|--------|
| Create | `/sdd-new` | proposal.md |
| Develop | `/sdd-artefact` or `/sdd-ff` | specs/, design.md, tasks.md |
| Implement | `/sdd-apply`, `/sdd-apply-group`, `/sdd-apply-all` | Code |
| Verify | `/sdd-verify` | Verification report |
| Archive | `/sdd-archive` | SUMMARY.md |

---

## Phase 1: Create Spec

Start a new specification:

```
/sdd-new
```

This creates `.specs/changes/<name>/proposal.md` containing:

- **Why** - The motivation for this change
- **What Changes** - Description of the changes
- **Capabilities** - New or modified capabilities
- **Scope** - In scope / out of scope items

### Proposal Structure

```markdown
# Proposal: <change-name>

## Why
<Motivation and problem statement>

## What Changes
<Description of changes>

## Capabilities

### New Capabilities
- `<capability-name>`: <description>

### Modified Capabilities
- `<capability-name>`: <description>
  - Existing: .specs/specs/<capability>/spec.md

## Scope

### In Scope
- <items>

### Out of Scope
- <items>
```

---

## Phase 2: Develop Artifacts

### Incremental Approach

Create artifacts one at a time:

```
/sdd-artefact
```

This creates the next ready artifact in order:
1. **specs/** - Requirements in EARS format
2. **design.md** - Technical design decisions
3. **tasks.md** - Implementation task breakdown

### Fast-Forward Approach

Create all artifacts at once:

```
/sdd-ff
```

Use this when you have a clear understanding of the feature.

### Optional: Explore First

Think through the idea before committing:

```
/sdd-explore
```

This helps clarify requirements and identify gaps.

### Check Progress

```
/sdd-status
```

Shows which artifacts are complete, ready, or blocked.

---

## Phase 3: Implement

Three execution modes:

### Single Task (High Control)

```
/sdd-apply
```

Implements one task at a time. Best for:
- Learning the workflow
- Risky changes
- When you want full control

### Group Execution (Batched)

```
/sdd-apply-group 2
```

Executes all tasks in a specific group. Tasks are organized into groups:

```markdown
## 1. Setup
_Meta: sequential, foundation_

## 2. Core Services
_Meta: parallel-safe, depends on: 1_

## 3. API Routes
_Meta: sequential, depends on: 2_
```

### Parallel Execution (Fastest)

```
/sdd-apply-all
```

Executes all groups via subagents. Groups run in dependency order.

### During Implementation

- **Reference requirements** in code comments when helpful
- **Update specs** if gaps found (don't workaround)
- **Mark tasks complete** immediately after finishing

---

## Phase 4: Verify

Before archiving, verify implementation matches spec:

```
/sdd-verify
```

Output:

```
VERIFICATION REPORT
───────────────────────────────────────
Requirements: 4 IMPLEMENTED, 1 PARTIAL, 0 MISSING
Scenarios: 10 COVERED, 2 MISSING

✓ IMPLEMENTED: user-login, token-generation, logout
⚠ PARTIAL: rate-limiting (missing IP blocking)

Tests: 8/10 scenarios tested
───────────────────────────────────────
Overall: 85% implemented
```

### Verification Options

```
/sdd-verify                # Verify current change
/sdd-verify --spec auth    # Verify specific spec
/sdd-verify --all          # Check all specs for drift
```

---

## Phase 5: Archive

Complete the change and archive it:

```
/sdd-archive
```

This:
1. Verifies all tasks are complete
2. Creates SUMMARY.md
3. Moves to `.specs/archive/YYYY-MM-DD-<name>/`
4. Merges deltas into `.specs/specs/`

---

## Brownfield: Existing Code

For projects without specs, extract specs from existing code:

```
/sdd-reverse src/auth/
```

This:
1. Scans the directory
2. Detects capabilities
3. Creates specs in `.specs/specs/`

Then use the standard workflow to create changes that reference existing capabilities.

---

## Quick Reference

```bash
# Start
/sdd-new

# Develop (choose one)
/sdd-artefact          # Incremental
/sdd-ff                # All at once
/sdd-status            # Check progress

# Implement (choose one)
/sdd-apply             # One task
/sdd-apply-group 2     # Group 2
/sdd-apply-all         # All groups

# Complete
/sdd-verify
/sdd-archive
```
