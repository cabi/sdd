---
name: sdd-spec-archive
description: Archive a completed spec. Merges delta specs into accumulated specs, creating a single source of truth. Moves completed change to archive for history.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "2.0.0"
---

# SDD Spec Archive

Archive a completed specification by merging deltas into accumulated specs.

## When to Use

- All tasks in a change are complete
- Implementation has been verified
- Ready to merge changes into main specs

## Pre-Conditions

1. Change exists at `.specs/changes/<name>/`
2. All tasks are marked complete (or intentionally skipped)

## Directory Structure

```
.specs/
├── specs/                     # Accumulated specs (single source of truth)
│   └── auth/
│       ├── authentication/spec.md
│       └── registration/spec.md
├── changes/                   # Active changes in progress
│   └── add-sso/
│       ├── proposal.md
│       ├── specs/
│       │   └── authentication/spec.md  # Delta spec
│       └── tasks.md
└── archive/                   # Completed changes (history)
    └── 2026-02-21-add-sso/
        └── SUMMARY.md
```

## Process

### Step 1: Verify Completion

Check task completion status:

```bash
# Parse tasks.md for completion percentage
grep -c "^\- \[x\]" tasks.md  # Completed
grep -c "^\- \[ \]" tasks.md  # Remaining
```

If incomplete tasks remain:
- Ask user to confirm archiving with incomplete tasks
- List the incomplete tasks
- Offer to continue with `/sdd-apply` instead

### Step 2: Analyze Delta Operations

Parse the change's specs to identify delta operations:

```
Change: add-sso

Delta operations:
┌─────────────────┬──────────┬─────────────────────────────┐
│ Capability      │ Operation │ Target                      │
├─────────────────┼──────────┼─────────────────────────────┤
│ authentication  │ MODIFIED │ specs/auth/authentication/  │
│ sso             │ ADDED    │ (new) specs/auth/sso/       │
└─────────────────┴──────────┴─────────────────────────────┘

This will:
1. Update specs/auth/authentication/spec.md with MODIFIED sections
2. Create specs/auth/sso/spec.md with ADDED sections
```

### Step 3: Merge Deltas into Main Specs

For each delta spec in the change:

#### ADDED Requirements

Create new spec file in `.specs/specs/<module>/<capability>/spec.md`:

```
.specs/changes/add-sso/specs/sso/spec.md
    → .specs/specs/auth/sso/spec.md (new file)
```

#### MODIFIED Requirements

1. Read existing spec: `.specs/specs/auth/authentication/spec.md`
2. Read delta spec: `.specs/changes/add-sso/specs/authentication/spec.md`
3. Apply delta operations:
   - Replace MODIFIED requirement blocks
   - Append ADDED requirements
   - Remove REMOVED requirements
4. Write updated spec back to `.specs/specs/auth/authentication/spec.md`

**Delta Application Order:**
1. RENAMED → Update requirement names
2. REMOVED → Delete requirement blocks
3. MODIFIED → Replace requirement content
4. ADDED → Append new requirements

#### REMOVED Requirements

Remove the requirement block from existing spec.

### Step 4: Create Summary

Create `SUMMARY.md` in the archive:

```markdown
# Summary: <change-name>

## What Was Delivered
<Brief description of what was implemented>

## Specs Updated

| Capability | Operation | Result |
|------------|-----------|--------|
| authentication | MODIFIED | Updated with SSO support |
| sso | ADDED | New capability created |

## Requirements Implemented
<List each requirement and its implementation status>

| Requirement | Status | Notes |
|-------------|--------|-------|
| sso-001 | ✓ Done | SSO enrollment flow |
| auth-012 | ✓ Done | Modified for SSO |

## Key Decisions
<Notable decisions made during implementation>

## Files Changed
<Summary of files created/modified>

---
Archived: <date>
Merged to specs: <date>
Total Tasks: X
Completed: Y
```

### Step 5: Archive the Change

Move change to archive with timestamp:

```
.specs/changes/add-sso/  →  .specs/archive/2026-02-21-add-sso/
```

### Step 6: Report Completion

```
✓ Deltas merged into main specs
  - MODIFIED: specs/auth/authentication/spec.md
  - ADDED: specs/auth/sso/spec.md

✓ Change archived: .specs/archive/2026-02-21-add-sso/
✓ Summary created

Main specs are now up to date.
.specs/specs/ is the single source of truth.
```

---

## Merge Examples

### Example 1: Adding New Capability

```
Before:
specs/
└── auth/
    └── authentication/spec.md

Change: add-sso
└── specs/sso/spec.md (ADDED only)

After merge:
specs/
└── auth/
    ├── authentication/spec.md
    └── sso/spec.md  ← NEW
```

### Example 2: Modifying Existing

```
Before:
specs/auth/authentication/spec.md contains:
### Requirement: User Login
The system SHALL authenticate via email/password.

Change: add-sso
└── specs/authentication/spec.md contains:
## MODIFIED Requirements
### Requirement: User Login
The system SHALL authenticate via email/password OR SSO.

After merge:
specs/auth/authentication/spec.md contains:
### Requirement: User Login
The system SHALL authenticate via email/password OR SSO.
```

### Example 3: Removing Requirements

```
Before:
specs/auth/authentication/spec.md contains:
### Requirement: Legacy Login
...

Change: remove-legacy
└── specs/authentication/spec.md contains:
## REMOVED Requirements
### Requirement: Legacy Login
**Reason**: Deprecated, use SSO instead

After merge:
specs/auth/authentication/spec.md no longer contains Legacy Login
```

---

## Handling Conflicts

### Spec File Doesn't Exist

If MODIFIED references a spec that doesn't exist:

```
Warning: MODIFIED capability 'authentication' but 
specs/auth/authentication/spec.md doesn't exist.

Options:
[1] Create new spec (treat as ADDED)
[2] Skip this capability
[3] Abort and investigate
```

### Merge Conflicts

If same requirement is modified by multiple concurrent changes:

```
Warning: Multiple changes modify 'authentication/login'.

Change A: add-sso (currently archiving)
Change B: add-biometric (still active)

This may cause conflicts. Options:
[1] Archive anyway (Change B may need adjustment)
[2] Wait for Change B to complete first
[3] Manual merge required
```

---

## Handling Incomplete Specs

If user confirms archiving with incomplete tasks:

1. Mark incomplete tasks as skipped in SUMMARY.md
2. Document why each was skipped
3. Note any follow-up work needed
4. Still merge any completed deltas

```markdown
## Incomplete Tasks

| Task | Reason Skipped |
|------|----------------|
| 3.2 Performance optimization | Moved to v2 |
```

---

## Reopening Archived Changes

If work needs to resume:

```bash
# Move back to changes
mv .specs/archive/2026-02-21-add-sso .specs/changes/add-sso-continued
```

Note: This creates a new change. Deltas already merged remain in main specs.

---

## Guardrails

1. **Always merge deltas** - Main specs must stay current
2. **Always create summary** - Future reference depends on it
3. **Verify before archiving** - Don't archive broken implementations
4. **Keep archive organized** - Use date-prefixed directories
5. **Document deviations** - Help future maintainers

## Key Principle

> `.specs/specs/` is the single source of truth.
> `.specs/changes/` is temporary work.
> `.specs/archive/` is history.
>
> Archive = Merge into specs + Move to history.
