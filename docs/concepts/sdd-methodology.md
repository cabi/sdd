# SDD Methodology

Understanding Spec-Driven Development principles and practices.

## Core Philosophy

### Clarity Before Code

> Ambiguity in requirements leads to wasted implementation effort. Write specs first, implement second.

By writing specifications before implementation:

- You understand the problem before solving it
- AI has better context for assistance
- Future maintainers understand decisions
- Rework is reduced significantly

### Single Source of Truth

> `.specs/specs/` is always current. Archive merges deltas; specs never drift.

The accumulated specs directory represents the current state of the system. Changes create delta specs that are merged upon archive.

### Iterative Refinement

Specs aren't perfect on first pass. The workflow encourages:

- Refinement at each phase
- Updates when implementation reveals gaps
- Living documentation that evolves

---

## Key Concepts

### EARS Requirements Format

Requirements use EARS (Easy Approach to Requirements Syntax):

```
WHEN <event> THEN system SHALL <response>
IF <condition> THEN system SHALL <response>
```

Example:

```markdown
### Requirement: User Login

The system SHALL authenticate users with email and password.

#### Scenario: Successful login
- **WHEN** user submits correct email and password
- **THEN** system SHALL return JWT token and refresh token

#### Scenario: Invalid credentials
- **WHEN** user submits incorrect email or password
- **THEN** system SHALL return error "Invalid credentials"
```

### Delta Specs

Changes use delta format instead of complete rewrites:

```markdown
## ADDED Requirements
<new requirements>

## MODIFIED Requirements
<changed requirements>

## REMOVED Requirements
<removed requirements>
```

This enables:
- Clear change tracking
- Impact assessment
- Merge without conflicts

### Traceability

Every task links to requirements:

```markdown
- [ ] 1.1 Create registration route
  - _Requirements: user-registration_
  - _Creates: src/routes/register.ts_
```

This means:
- You know why code exists
- Changes can assess impact
- Documentation stays current

---

## When to Use SDD

### Use SDD For:

- Features requiring > 1 day of work
- Multiple components or integrations
- High-stakes changes where rework is costly
- Complex features with unclear requirements
- Team collaboration on specifications

### Use Micro-Spec For:

- Changes < 1 day of work
- Single component modifications
- Bug fixes with known solution
- Well-understood patterns

### Skip SDD For:

- Trivial changes (typo fixes, config updates)
- Well-established patterns with minimal ambiguity
- Time-critical hotfixes

---

## Workflow Phases

```
┌─────────────┐
│   EXPLORE   │  /sdd-explore → context-log.md
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   PROPOSE   │  /sdd-propose → proposal.md
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   DEVELOP   │  /sdd-artefact → specs/, design.md, tasks.md
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  IMPLEMENT  │  /sdd-apply → Code
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   VERIFY    │  /sdd-verify → Verification report
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   ARCHIVE   │  /sdd-archive → SUMMARY.md, merge to specs/
└─────────────┘
```

---

## Directory Structure

```
.specs/
├── specs/                      # Accumulated specs (single source of truth)
│   └── <capability>/
│       ├── proposal.md
│       └── specs/<sub-capability>/spec.md
├── changes/                    # Active changes in progress
│   └── <change-name>/
│       ├── proposal.md         # Why, What, Scope
│       ├── specs/              # Delta specs
│       ├── design.md           # Technical design
│       └── tasks.md            # Implementation tasks
└── archive/                    # Completed changes
    └── YYYY-MM-DD-<change-name>/
        └── SUMMARY.md
```

### Key Principle

`.specs/specs/` always reflects the current system. Changes go through `.specs/changes/` and merge back upon archive.

---

## Spec-Reality Divergence

If implementation reveals spec gaps:

1. **STOP** - Don't workaround
2. **UPDATE** the spec with new understanding
3. **DOCUMENT** why change was needed
4. **CONTINUE** with implementation

This prevents specs from drifting from reality.

---

## Verification

Before archiving, verify:

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
