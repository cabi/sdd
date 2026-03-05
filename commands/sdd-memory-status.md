---
name: sdd-memory-status
description: View the current memory state for debugging and inspection
---

View the current memory state for a change specification. Useful for debugging and understanding what has been recorded.

**Usage:** `/sdd-memory-status [change-name] [options]`

**Options:**
- `--decisions`: Show decisions only
- `--requirements`: Show requirements only
- `--citations`: Show citations only
- `--episodes`: Show episode history
- `--all`: Show everything (default)
- `--json`: Output raw JSON

**Process:**

1. **Detect change** from `.specs/changes/` or use provided name
2. **Load memory files** from `.memory/`
3. **Format and display** requested sections

**Output:**
```
═══════════════════════════════════════════════════════════════
Memory Status: user-authentication
═══════════════════════════════════════════════════════════════

## Overview

| File | Entries | Last Updated |
|------|---------|--------------|
| decisions.json | 8 | 2026-02-21 14:32:00 |
| requirements.json | 12 | 2026-02-21 15:45:00 |
| citations.json | 34 | 2026-02-21 15:45:00 |
| control-log.json | 6 | 2026-02-21 15:46:00 |
| episodes.json | 4 | 2026-02-21 15:45:00 |

## Decisions (8)

| ID | Title | Status | Source |
|----|-------|--------|--------|
| DEC-001 | Dependencies | active | design.md#L45 |
| DEC-002 | Password hashing | superseded | design.md#L78 |
| DEC-003 | Password cost factor | active | design.md#L92 |
| DEC-004 | Session storage | active | design.md#L120 |
| ... | | | |

## Requirements (12)

| ID | Title | Status | Tasks |
|----|-------|--------|-------|
| AUTH-001 | Password hashing | implemented | 2.1 |
| AUTH-002 | Password strength | implemented | 2.1 |
| AUTH-003 | Token generation | implemented | 2.2 |
| AUTH-004 | Token expiry | implemented | 2.2 |
| AUTH-005 | Login endpoint | pending | 3.1 |
| ... | | | |

Status Summary:
  - implemented: 8 (67%)
  - pending: 3 (25%)
  - in_progress: 1 (8%)

## Citations (34)

| From | To | Relationship | Verified |
|------|-------|--------------|----------|
| tasks.md#L45 | DEC-002 | references | ✓ |
| hash.ts | AUTH-001 | satisfies | ✓ |
| TokenService.ts | DEC-004 | implements | ✓ |
| ... | | | |

## Control Log (6 checkpoints)

| Time | Phase | Status | Checks |
|------|-------|--------|--------|
| 14:30 | artefact-creation | pass | 5/5 |
| 14:32 | artefact-creation | pass | 5/5 |
| 15:00 | task-execution | pass | 8/8 |
| 15:20 | task-execution | warn | 7/8 |
| 15:45 | verification | pass | 12/12 |
| 15:46 | verification | pass | 8/8 |

## Episodes (4)

### Episode 1: Setup (2026-02-21 15:00)
- Files created: src/auth/, src/auth/service/
- Decisions: Auth structure per design.md
- Requirements: AUTH-SETUP-001, AUTH-SETUP-002

### Episode 2: Core Implementation (2026-02-21 15:20)
- Files created: hash.ts, TokenService.ts, AuthService.ts
- Decisions: Integrated hash and token services
- Requirements: AUTH-001 through AUTH-007

...

## Inconsistencies

⚠ Warning: DEC-002 is superseded but still referenced by:
  - tasks.md#L45
  - hash.ts:L2

Suggestion: Update references to DEC-003 (superseding decision)

═══════════════════════════════════════════════════════════════
```

**With --json:**
```json
{
  "decisions": [...],
  "requirements": {...},
  "citations": [...],
  "control_log": [...],
  "episodes": [...]
}
```

---

## Valid Next Commands

**Memory status is informational - use it anytime in SCL workflow:**
- Any SCL command appropriate to current stage
- No restrictions (this is a read-only utility)

**Only available in SCL workflow** (requires .memory/ directory)

**Loads skills:** `sdd-memory`
