---
name: sdd-tasks
description: Task breakdown for SDD. Converts design into actionable implementation tasks with proper sequencing, dependencies, and requirement traceability.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "1.0.0"
---

# SDD Task Breakdown

Convert design into actionable, sequenced implementation tasks.

## Task Document Structure

```markdown
# Tasks: <spec-name>

## 1. <Section Name>
_Meta: sequential|parallel-safe, depends on: <groups>_

- [ ] 1.1 <Task description>
  - _Requirements: <ref>_
  - _Creates: <path>_ or _Modifies: <path>_

## 2. <Section Name>
_Meta: parallel-safe, depends on: 1_

- [ ] 2.1 <Task description>
  - _Requirements: <ref>_
  - _Creates: <path>_
```

## Task Format

**Required elements:**

```markdown
- [ ] X.Y <Task description>
  - _Requirements: <requirement-id>_
  - _Creates: <path>_ or _Modifies: <path>_
  - _Tests: <task-ref>_ (for implementation tasks with test counterparts)
```

**Test traceability field:**

| Field | Direction | When to Use |
|-------|-----------|-------------|
| `_Tests: N.M` on implementation task | Impl → Test | Every implementation task that adds/changes behavior |
| `_Tests: N.M` on test task | Test → Impl | Every test task references what it tests |

**Group metadata (for batch execution):**

```markdown
## 1. Setup
_Meta: sequential, foundation for all groups_
```

**Group metadata fields:**

| Field | Values | Purpose |
|-------|--------|---------|
| `sequential` | - | Must run alone, no parallelism |
| `parallel-safe` | - | Can run alongside other parallel-safe groups |
| `depends on: N` | Group number(s) | Requires group N to complete first |
| `foundation` | - | Other groups depend on this |

**Per-task file hints:**

| Field | Purpose |
|-------|---------|
| `_Creates: path_` | New file created (safe for parallel) |
| `_Modifies: path_` | Existing file modified (check for conflicts) |
| `_Tests: task-ref_` | Bidirectional link between implementation and test tasks |

These fields enable:
- Dependency-aware execution ordering
- Parallel group dispatch where safe
- Scope constraints for subagents
- Test traceability (implementation ↔ test bidirectional mapping)
- File modification verification

## Section Organization

### Common Section Patterns

**Pattern 1: By Phase**
```markdown
## 1. Setup
## 2. Core Implementation  
## 3. Integration
## 4. Testing
## 5. Documentation & Cleanup
```

**Pattern 2: By Component**
```markdown
## 1. Data Layer
## 2. Service Layer
## 3. API Layer
## 4. UI Layer
## 5. Testing (per-component unit + integration)
```

**Pattern 3: By Feature Slice**
```markdown
## 1. Authentication (End-to-End)
## 2. Session Management (End-to-End)
## 3. Password Reset (End-to-End)
```

### Testing Group Requirements

Every task breakdown **MUST** include test tasks. Use one of these approaches:

**Dedicated Testing Group** (recommended default):
```markdown
## 4. Testing
_Meta: sequential, depends on: 1, 2, 3_

- [ ] 4.1 Unit tests for AuthService
  - _Requirements: AUTH-001, AUTH-002_
  - _Creates: tests/auth/service/AuthService.test.ts_
  - _Tests: 2.1_
```

**Inline Test Tasks** (feature-slice organization):
```markdown
## 2. Core Implementation
- [ ] 2.1 Implement AuthService
  - _Tests: 2.2_
- [ ] 2.2 Test AuthService
  - _Tests: 2.1_
```

**Hybrid** (unit tests inline, integration tests separate):
```markdown
## 2. Core Implementation
- [ ] 2.1 Implement AuthService
  - _Tests: 2.2_
- [ ] 2.2 Test AuthService (unit)
  - _Tests: 2.1_

## 5. Integration Testing
_Meta: depends on: 2, 3_
- [ ] 5.1 Test full auth flow (integration)
```

## Sequencing Strategies

### Foundation-First
Build core infrastructure before dependent features.

```
1. Setup
   - Database schema
   - Base types/interfaces
2. Core
   - Authentication logic
3. Features
   - Login UI
   - Password reset
```

**Best for:** New systems, major architectural changes

### Feature-Slice
Complete vertical slices for early validation.

```
1. Login Feature
   - API endpoint
   - Service logic
   - UI component
   - Tests
2. Password Reset Feature
   - API endpoint
   - Email service
   - UI component
   - Tests
```

**Best for:** Parallel team work, early demos

### Risk-First
Tackle uncertain areas early.

```
1. Risky Integration
   - Third-party auth API
   - Validate assumptions
2. Core Features
   - Build on validated integration
3. Polish
   - UI refinements
```

**Best for:** New technologies, external dependencies

### Hybrid
Combine strategies as needed.

```
1. Setup (Foundation-First)
2. Auth Core (Risk-First - external API)
3. Login Feature (Feature-Slice)
4. Session Management (Feature-Slice)
5. Testing & Polish
```

## Task Sizing

**Good task size:** 2-4 hours of focused work

| Too Small | Good | Too Large |
|-----------|------|-----------|
| "Add import" | "Create auth service module" | "Implement authentication" |
| "Update variable" | "Add password validation" | "Build user management system" |

**Signs a task is too large:**
- Multiple files in different layers
- More than one requirement
- Takes more than half a day
- "and" in the description

**Signs a task is too small:**
- Single line change
- No testable outcome
- Just a setup step

## Task Examples

### Setup Tasks

```markdown
## 1. Setup

- [ ] 1.1 Create auth module directory structure
  - Create src/auth/, src/auth/service/, src/auth/api/
  - _Requirements: setup_

- [ ] 1.2 Add auth dependencies to package.json
  - bcrypt, jsonwebtoken
  - _Requirements: setup_

- [ ] 1.3 Create database migration for user table
  - Add email, passwordHash columns
  - _Requirements: user-storage-001_
```

### Implementation Tasks

```markdown
## 2. Core Implementation

- [ ] 2.1 Implement password hashing utility
  - Create src/auth/utils/hash.ts
  - Use bcrypt with cost factor 12
  - _Requirements: password-security-001_
  - _Tests: 4.1_

- [ ] 2.2 Implement token generation service
  - Create src/auth/service/TokenService.ts
  - JWT with 1-hour expiry
  - _Requirements: session-management-001_
  - _Tests: 4.2_

- [ ] 2.3 Create authentication service
  - Create src/auth/service/AuthService.ts
  - Implement login, logout, validate methods
  - _Requirements: authentication-001, authentication-002_
  - _Tests: 4.3_
```

### API Tasks

```markdown
## 3. API Layer

- [ ] 3.1 Create login endpoint
  - POST /auth/login
  - Return token on success
  - _Requirements: authentication-001_

- [ ] 3.2 Create authentication middleware
  - Validate token from Authorization header
  - Attach user to request
  - _Requirements: session-management-002_

- [ ] 3.3 Create logout endpoint
  - POST /auth/logout
  - Invalidate session
  - _Requirements: session-management-003_
```

### Testing Tasks

```markdown
## 4. Testing
_Meta: sequential, depends on: 2, 3_

- [ ] 4.1 Unit tests for password hashing
  - Test hash, verify, compare
  - _Requirements: password-security-001_
  - _Creates: tests/auth/utils/hash.test.ts_
  - _Tests: 2.1_

- [ ] 4.2 Unit tests for token service
  - Test generation, validation, expiry
  - _Requirements: session-management-001_
  - _Creates: tests/auth/service/TokenService.test.ts_
  - _Tests: 2.2_

- [ ] 4.3 Integration tests for auth flow
  - Test login -> access protected route -> logout
  - _Requirements: authentication-001_
  - _Creates: tests/auth/integration/login-flow.test.ts_
  - _Tests: 3.1, 3.3_
```

### Documentation Tasks

```markdown
## 5. Documentation & Cleanup

- [ ] 5.1 Update API documentation
  - Document auth endpoints
  - Include request/response examples
  - _Requirements: documentation_

- [ ] 5.2 Add code comments for complex logic
  - Comment token validation flow
  - _Requirements: documentation_
```

## Requirement Traceability

Every task must reference at least one requirement:

```markdown
- [ ] 2.1 Implement password hashing
  - _Requirements: password-security-001_
```

**Multiple requirements:**
```markdown
- [ ] 2.3 Create authentication service
  - _Requirements: authentication-001, authentication-002_
```

**Setup tasks:**
```markdown
- [ ] 1.1 Create directory structure
  - _Requirements: setup_
```

## Validation Checklist

Before finalizing tasks:

- [ ] All design components have implementation tasks
- [ ] Tasks are properly sequenced by dependency
- [ ] Each task is actionable (you know what to do)
- [ ] Each task has a testable outcome
- [ ] Task sizes are appropriate (2-4 hours)
- [ ] All requirements have at least one task reference
- [ ] No tasks reference non-existent requirements
- [ ] Every requirement scenario has at least one test task
- [ ] Implementation tasks have `_Tests:` references to their test tasks
- [ ] Test tasks have `_Tests:` references back to their implementation tasks
- [ ] Error and edge case scenarios have dedicated test tasks
- [ ] Multi-component changes have integration test tasks

## Common Mistakes

### Missing Dependencies

❌ Bad:
```markdown
- [ ] 1.1 Create login endpoint
- [ ] 1.2 Create auth service (used by 1.1)
```

✓ Good:
```markdown
- [ ] 1.1 Create auth service
- [ ] 1.2 Create login endpoint (depends on 1.1)
```

### Vague Tasks

❌ Bad:
```markdown
- [ ] 1.1 Do the auth stuff
```

✓ Good:
```markdown
- [ ] 1.1 Implement AuthService.login() method
  - Validate credentials against database
  - Generate JWT token
  - _Requirements: authentication-001_
```

### Missing Requirement References

❌ Bad:
```markdown
- [ ] 1.1 Add password validation
```

✓ Good:
```markdown
- [ ] 1.1 Add password validation
  - _Requirements: password-security-001_
```

### Tasks Too Large

❌ Bad:
```markdown
- [ ] 1.1 Implement authentication
```

✓ Good:
```markdown
- [ ] 1.1 Create auth service module
- [ ] 1.2 Implement password hashing
- [ ] 1.3 Implement token generation
- [ ] 1.4 Create login endpoint
```

### Missing Test Tasks

❌ Bad:
```markdown
## 2. Core Implementation
- [ ] 2.1 Implement AuthService
- [ ] 2.2 Implement TokenService
## 3. API Layer
- [ ] 3.1 Create login endpoint
## 4. Documentation
- [ ] 4.1 Update API docs
```

✓ Good:
```markdown
## 2. Core Implementation
- [ ] 2.1 Implement AuthService
  - _Tests: 4.1_
- [ ] 2.2 Implement TokenService
  - _Tests: 4.2_
## 3. API Layer
- [ ] 3.1 Create login endpoint
  - _Tests: 4.3_
## 4. Testing
_Meta: depends on: 2, 3_
- [ ] 4.1 Test AuthService (unit)
  - _Tests: 2.1_
- [ ] 4.2 Test TokenService (unit)
  - _Tests: 2.2_
- [ ] 4.3 Test login flow (integration)
  - _Tests: 3.1_
```

### Missing `_Tests:` Bidirectional References

❌ Bad:
```markdown
- [ ] 2.1 Implement AuthService
- [ ] 4.1 Test AuthService
```

✓ Good:
```markdown
- [ ] 2.1 Implement AuthService
  - _Tests: 4.1_
- [ ] 4.1 Test AuthService
  - _Tests: 2.1_
```

## Progress Tracking

Tasks are tracked via checkboxes:

```markdown
- [ ] 1.1 Pending task
- [x] 1.2 Completed task
- [~] 1.3 Skipped task (optional notation)
```

**Completion percentage:**
```
Tasks: 4/12 complete (33%)
```

## Mandatory Review Process

After creating the initial task breakdown, tasks **MUST** go through a 3-iteration review loop.

The review loop is orchestrated by the `/sdd-artefact` command (or `/sdd-ff`), which alternates between:
- `@sdd-task-analyst` (critique) — produces `task-review-iteration-N.md`
- `@sdd-task` with MODE="revise" (fix) — applies critique feedback to `tasks.md`

The task agent operates in two modes:
- **MODE="create"**: Generates the initial tasks.md from specs, design, and codebase analysis
- **MODE="revise"**: Reads a critique report and applies fixes to tasks.md

See `sdd-task-review` skill for full review protocol details.

**Review loop summary:**
1. Task agent creates initial tasks.md (MODE="create")
2. Command runs 3 iterations:
   - `@sdd-task-analyst` → `task-review-iteration-N.md` → `@sdd-task` MODE="revise" → revised tasks.md
3. Final gate: APPROVE, 0 critical, 0 major unresolved, 100% requirement and design coverage

## Task Iteration History

After the review loop, tasks.md **MUST** include a change log section:

```markdown
## Task Iteration History

### Iteration 3 → Final (Current)
**Issues Addressed:** X minor, 0 critical, 0 major
- MIN-001: <brief description of what was addressed>

### Iteration 2 → 3
**Issues Addressed:** X major, 0 critical
- MAJ-001: <brief description of what was addressed>

### Iteration 1 → 2
**Issues Addressed:** X critical, Y major
- CRIT-001: <brief description of what was addressed>
```

## Process

1. **Analyze design** - Extract components, decisions, flows
2. **Group work** - Organize into logical sections (groups)
3. **Add group metadata** - Dependencies, parallel-safety
4. **Sequence tasks** - Order by dependency within groups
5. **Size tasks** - Break into 2-4 hour chunks
6. **Add traceability** - Link to requirements, file hints
7. **Validate** - Check against checklist
8. **Review loop** - 3 iterations with sdd-task-analyst (mandatory)
9. **Document history** - Add Task Iteration History section

## Execution Modes

Task groups support three execution modes:

| Mode | Command | Use When |
|------|---------|----------|
| Single | `/sdd-apply` | High control, learning, risky changes |
| Group | `/sdd-apply-group N` | Batch related tasks with clear scope |
| All | `/sdd-apply-all` | Large projects, well-defined boundaries |

**Group metadata enables:**
- Dependency ordering (Group 3 waits for Group 2)
- Parallel dispatch (parallel-safe groups can run simultaneously)
- Scope constraints (subagents stay within their group)
- Verification (check files modified vs allowed)
