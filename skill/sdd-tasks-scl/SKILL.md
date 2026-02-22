---
name: sdd-tasks-scl
description: SCL-enhanced task breakdown with evidential grounding, memory context, and control validation. Creates implementation tasks with full traceability to requirements and design decisions.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: advanced
  author: OpenCode
  version: "1.0.0"
---

# SDD Task Breakdown (SCL-Enhanced)

Convert design into actionable, sequenced implementation tasks with full SCL compliance. This skill implements evidential grounding, memory context injection, and control validation for task groups.

## RFC2119 Requirements

### Core Requirements

1. Every task **MUST** reference at least one requirement
2. Every task **MUST** cite evidence from design or specs
3. Every task group **MUST** specify preconditions
4. Every task **MUST** specify validation criteria
5. Every task **MUST** specify memory write operations
6. Task groups **MUST** be sequenced by dependency
7. The system **MUST** generate subagent context for each group

## Task Document Structure

```markdown
# Tasks: <spec-name>

> Memory ID: decisions.json#TASKS-<ID>
> Created: <ISO 8601 timestamp>
> Depends on: requirements.json#<REQ-IDs>, decisions.json#<DEC-IDs>
> Regulation: regulation.md

## Execution Metadata

| Group | Tasks | Depends On | Parallel-Safe | Est. Hours |
|-------|-------|------------|---------------|------------|

## N. <Group Name>

_Meta: <execution-mode>, depends on: <groups>_

### Preconditions
> Verified by: CONTROL.check_preconditions()

### Memory Context for Subagent
```json
{ ... }
```

### Tasks

- [ ] N.M <Task description>
  - _Requirements: <REQ-IDs> (per <source>)_
  - _Evidence: <source>#<location>_
  - _Creates: <path>_ | _Modifies: <path>_
  - _Validation: <criteria>_
  - _Memory Write: <operations>_
```

## Task Format Specification

### Required Elements

Every task **MUST** contain:

```markdown
- [ ] X.Y <Task description>
  - _Requirements: <REQ-ID> (per specs/<capability>/spec.md#L<N>)_
  - _Evidence: design.md#<section>_
  - _Creates: <path>_ | _Modifies: <path>_
  - _Validation: <how to verify completion>_
  - _Memory Write: <what to record in memory>_
```

### Group Metadata Format

Every task group **MUST** include:

```markdown
## N. <Group Name>

_Meta: <mode>, depends on: <group-nums>_

### Preconditions
- [ ] <precondition 1>
- [ ] <precondition 2>

### Memory Context for Subagent
```json
{
  "decisions": ["DEC-001"],
  "requirements": ["AUTH-001"],
  "constraints": {
    "allowed_files": ["src/auth/**/*"],
    "must_cite": ["design.md#*"]
  }
}
```
```

### Metadata Fields

| Field | Values | Requirement |
|-------|--------|-------------|
| `sequential` | - | MUST run alone, no parallelism |
| `parallel-safe` | - | MAY run alongside other parallel-safe groups |
| `depends on: N` | Group numbers | MUST complete group N first |
| `foundation` | - | Other groups depend on this |

### Per-Task Fields

| Field | Purpose | Requirement |
|-------|---------|-------------|
| `_Requirements: <ref>_` | Link to spec requirements | MUST include source citation |
| `_Evidence: <ref>_` | Link to design decision | MUST reference specific location |
| `_Creates: <path>_` | New file created | MUST specify exact path |
| `_Modifies: <path>_` | Existing file modified | MUST specify exact path |
| `_Validation: <criteria>_` | How to verify completion | MUST be testable |
| `_Memory Write: <ops>_` | What to record | MUST update relevant memory files |

## Section Organization

### Recommended Section Pattern

```markdown
## 1. Setup
_Meta: sequential, foundation_

## 2. Data Layer
_Meta: parallel-safe, depends on: 1_

## 3. Service Layer
_Meta: parallel-safe, depends on: 2_

## 4. API Layer
_Meta: sequential, depends on: 3_

## 5. Integration
_Meta: sequential, depends on: 4_

## 6. Testing
_Meta: parallel-safe, depends on: 5_

## 7. Documentation & Cleanup
_Meta: sequential, depends on: 6_
```

## Task Sizing Requirements

### Size Guidelines

| Too Small | Good (2-4 hours) | Too Large |
|-----------|------------------|-----------|
| "Add import" | "Create AuthService with login method" | "Implement authentication" |
| "Update variable" | "Add password validation with tests" | "Build user management system" |

### Signs of Incorrect Sizing

**Too Large (MUST split):**
- Multiple files in different layers
- More than two requirements
- Takes more than half a day
- Contains "and" in description
- Has more than 5 validation criteria

**Too Small (MUST merge):**
- Single line change
- No testable outcome
- Just a setup step
- No meaningful validation

## Memory Context Generation

### Subagent Context Schema

For each task group, the system **MUST** generate:

```json
{
  "group_id": N,
  "group_name": "<Group Name>",
  "memory": {
    "decisions": [
      {
        "id": "DEC-001",
        "title": "<decision title>",
        "chosen": "<chosen option>",
        "source": "design.md#L45"
      }
    ],
    "requirements": [
      {
        "id": "AUTH-001",
        "title": "<requirement title>",
        "description": "<full text>",
        "source": "specs/auth/spec.md#L23"
      }
    ],
    "prior_outcomes": {
      "files_created": ["src/models/User.ts"],
      "decisions_made": ["Use interface over class"],
      "constraints_discovered": ["Must support both SQL and NoSQL"]
    }
  },
  "constraints": {
    "allowed_files": ["src/auth/**/*.ts", "tests/auth/**/*.test.ts"],
    "blocked_files": ["src/core/*", "src/db/migrations/*"],
    "allowed_tools": ["read", "write", "edit", "bash:npm test"],
    "blocked_tools": ["bash:rm -rf", "bash:git push"],
    "required_citations": [
      "design.md#decision-*",
      "specs/**/spec.md#req-*"
    ]
  },
  "completion_criteria": {
    "tasks": ["N.1", "N.2", "N.3"],
    "required_signal": "GROUP N COMPLETE",
    "files_must_exist": ["src/auth/AuthService.ts"],
    "files_must_not_exist": [],
    "tests_must_pass": ["tests/auth/AuthService.test.ts"]
  },
  "regulation": {
    "evidential_rules": [
      "Every file MUST have header comment citing requirements",
      "Every function MUST cite the requirement it implements"
    ],
    "scope_rules": [
      "MUST NOT modify files outside allowed_files",
      "MUST NOT create files in blocked paths"
    ],
    "validation_rules": [
      "MUST mark task complete only after validation passes",
      "MUST run tests before marking complete"
    ]
  }
}
```

### Context Injection

The system **MUST** inject this context into subagent prompts:

```
You are executing Group N: <Group Name> of the <spec-name> implementation.

## Memory Context (from prior work)

### Decisions You MUST Follow
<list of relevant decisions with sources>

### Requirements You MUST Satisfy
<list of relevant requirements with sources>

### Prior Work Outcomes
<what was done in prior groups>

## Constraints (YOU MUST NOT VIOLATE)

### Allowed Files
You MAY only create/modify: <list>

### Blocked Files
You MUST NOT touch: <list>

### Required Citations
Every file you create MUST include:
// Implements: REQ-ID (per specs/.../spec.md#L<N>)
// Design: design.md#<section>

## Your Tasks
- [ ] N.1 <task>
- [ ] N.2 <task>
- [ ] N.3 <task>

## Completion Criteria
You MUST:
1. Complete ALL tasks above
2. Verify ALL files in completion_criteria.files_must_exist exist
3. Ensure ALL tests in completion_criteria.tests_must_pass pass
4. Output "GROUP N COMPLETE" as your final line

DO NOT:
- Start work on Group N+1
- Modify files outside allowed_files
- Skip validation steps
```

## Task Examples with SCL Enhancements

### Setup Tasks

```markdown
## 1. Setup

_Meta: sequential, foundation_

### Preconditions
- [ ] `design.md` exists with auth architecture
- [ ] `requirements.json` has AUTH-* requirements indexed

### Memory Context for Subagent
```json
{
  "decisions": [],
  "requirements": [
    {"id": "AUTH-SETUP-001", "title": "Module structure"}
  ],
  "constraints": {
    "allowed_files": ["src/auth/**/*"],
    "must_cite": ["design.md#architecture"]
  }
}
```

### Tasks

- [ ] 1.1 Create auth module directory structure
  - _Requirements: AUTH-SETUP-001 (per specs/auth/spec.md#L10)_
  - _Evidence: design.md#L45-52 (auth module layout decision)_
  - _Creates: src/auth/, src/auth/service/, src/auth/api/, src/auth/utils/_
  - _Validation:_
    - All directories exist
    - index.ts barrel file in each directory
    - Directory structure matches design.md#L45-52
  - _Memory Write:_
    - `decisions.json` ← {"auth_structure": "per design.md#L45-52"}
    - `control-log.json` ← checkpoint for task 1.1

- [ ] 1.2 Add auth dependencies to package.json
  - _Requirements: AUTH-SETUP-002 (per specs/auth/spec.md#L15)_
  - _Evidence: design.md#decision-dependencies (DEC-001)_
  - _Modifies: package.json_
  - _Validation:_
    - Dependencies added: bcrypt, jsonwebtoken
    - Versions match design.md#decision-dependencies
    - npm install succeeds
  - _Memory Write:_
    - `decisions.json#DEC-001.status` ← "implemented"
    - `requirements.json#AUTH-SETUP-002.status` ← "implemented"
```

### Implementation Tasks

```markdown
## 2. Core Implementation

_Meta: parallel-safe, depends on: 1_

### Preconditions
- [ ] Group 1 complete (verified by CONTROL.verify_group(1))
- [ ] Directories exist: src/auth/service/, src/auth/utils/
- [ ] Dependencies installed: bcrypt, jsonwebtoken

### Memory Context for Subagent
```json
{
  "decisions": [
    {
      "id": "DEC-002",
      "title": "Password hashing algorithm",
      "chosen": "bcrypt",
      "rationale": "Industry standard, built-in salt"
    },
    {
      "id": "DEC-003",
      "title": "Password cost factor",
      "chosen": "12",
      "rationale": "Balance between security and performance"
    }
  ],
  "requirements": [
    {"id": "AUTH-001", "description": "Passwords SHALL be hashed with bcrypt"},
    {"id": "AUTH-002", "description": "Cost factor SHALL be at least 10"}
  ],
  "prior_outcomes": {
    "directories": ["src/auth/", "src/auth/service/", "src/auth/utils/"]
  },
  "constraints": {
    "allowed_files": ["src/auth/**/*.ts"],
    "must_cite": ["design.md#decision-*", "specs/auth/spec.md#*"]
  }
}
```

### Tasks

- [ ] 2.1 Implement password hashing utility
  - _Requirements: AUTH-001, AUTH-002 (per specs/auth/spec.md#L23-34)_
  - _Evidence: design.md#decision-password-hashing (DEC-002, DEC-003)_
  - _Precondition: Task 1.2 complete (bcrypt installed)_
  - _Creates: src/auth/utils/hash.ts_
  - _Interface (per design.md#L78):_
    ```typescript
    export function hash(password: string): Promise<string>;
    export function verify(password: string, hash: string): Promise<boolean>;
    ```
  - _Validation:_
    - Unit tests exist: tests/auth/utils/hash.test.ts
    - Tests pass: npm test hash.test.ts
    - Uses bcrypt with cost factor 12 (per DEC-003)
    - hash() returns bcrypt hash format
    - verify() correctly validates hashes
  - _Memory Write:_
    - `requirements.json#AUTH-001.status` ← "implemented"
    - `requirements.json#AUTH-002.status` ← "implemented"
    - `citations.json` ← {"from": "hash.ts", "to": "DEC-002", "relationship": "implements"}
    - `citations.json` ← {"from": "hash.ts", "to": "AUTH-001", "relationship": "satisfies"}

- [ ] 2.2 Implement token generation service
  - _Requirements: AUTH-003, AUTH-004 (per specs/auth/spec.md#L45-67)_
  - _Evidence: design.md#decision-session-storage (DEC-004)_
  - _Precondition: Task 1.2 complete (jsonwebtoken installed)_
  - _Creates: src/auth/service/TokenService.ts_
  - _Interface (per design.md#L102):_
    ```typescript
    export class TokenService {
      generate(userId: string): string;
      validate(token: string): { userId: string } | null;
      refresh(token: string): string | null;
    }
    ```
  - _Validation:_
    - Unit tests exist and pass
    - JWT expiry = 1 hour (per DEC-004)
    - generate() returns valid JWT
    - validate() returns userId for valid tokens
    - validate() returns null for invalid/expired tokens
    - refresh() returns new token for valid tokens
  - _Memory Write:_
    - `requirements.json#AUTH-003.status` ← "implemented"
    - `requirements.json#AUTH-004.status` ← "implemented"
    - `citations.json` ← {"from": "TokenService.ts", "to": "DEC-004"}

- [ ] 2.3 Create authentication service
  - _Requirements: AUTH-005, AUTH-006, AUTH-007 (per specs/auth/spec.md#L78-112)_
  - _Evidence: design.md#components (AuthService design)_
  - _Precondition: Tasks 2.1 AND 2.2 complete_
  - _Creates: src/auth/service/AuthService.ts_
  - _Interface (per design.md#L134):_
    ```typescript
    export class AuthService {
      login(email: string, password: string): Promise<{ token: string } | null>;
      logout(token: string): Promise<void>;
      validate(token: string): Promise<{ userId: string } | null>;
    }
    ```
  - _Validation:_
    - Integration tests exist and pass
    - login() uses hash.verify() (per task 2.1)
    - login() uses TokenService.generate() (per task 2.2)
    - login() returns null for invalid credentials
    - logout() invalidates token
    - validate() returns userId for valid sessions
  - _Memory Write:_
    - `requirements.json#AUTH-005.status` ← "implemented"
    - `requirements.json#AUTH-006.status` ← "implemented"
    - `requirements.json#AUTH-007.status` ← "implemented"
    - `decisions.json` ← {"auth_service_integrates": "hash.ts and TokenService.ts"}
```

### API Layer Tasks

```markdown
## 3. API Layer

_Meta: sequential, depends on: 2_

### Preconditions
- [ ] Group 2 complete
- [ ] AuthService fully implemented
- [ ] All core service tests pass

### Tasks

- [ ] 3.1 Create login endpoint
  - _Requirements: API-001, API-002 (per specs/auth/spec.md#L120-145)_
  - _Evidence: design.md#api-changes (POST /auth/login)_
  - _Creates: src/auth/api/authController.ts_
  - _Validation:_
    - POST /auth/login accepts { email, password }
    - Returns 200 with { token } on success
    - Returns 401 for invalid credentials
    - Returns 400 for missing fields
    - Integration test covers all scenarios
  - _Memory Write:_
    - `requirements.json#API-001.status` ← "implemented"
    - `citations.json` ← authController implements API-001

- [ ] 3.2 Create authentication middleware
  - _Requirements: API-003 (per specs/auth/spec.md#L150-167)_
  - _Evidence: design.md#middleware-design_
  - _Creates: src/auth/api/authMiddleware.ts_
  - _Validation:_
    - Extracts token from Authorization header
    - Attaches user to request object
    - Returns 401 for missing/invalid tokens
    - Allows request through for valid tokens
  - _Memory Write:_
    - `requirements.json#API-003.status` ← "implemented"
```

### Testing Tasks

```markdown
## 6. Testing

_Meta: parallel-safe, depends on: 5_

### Preconditions
- [ ] All implementation groups complete
- [ ] All unit tests exist (created with implementation)

### Tasks

- [ ] 6.1 Integration tests for auth flow
  - _Requirements: TEST-001 (per specs/auth/spec.md#L200)_
  - _Evidence: design.md#test-strategy_
  - _Creates: tests/auth/integration/authFlow.test.ts_
  - _Scenarios to test:_
    - User registers → logs in → accesses protected route → logs out
    - User attempts login with wrong password (401)
    - User accesses protected route without token (401)
    - User accesses protected route with expired token (401)
  - _Validation:_
    - All scenarios pass
    - Coverage >= 80% for auth module
  - _Memory Write:_
    - `requirements.json#TEST-001.status` ← "verified"

- [ ] 6.2 End-to-end tests
  - _Requirements: TEST-002 (per specs/auth/spec.md#L210)_
  - _Evidence: design.md#test-strategy_
  - _Creates: tests/auth/e2e/auth.e2e.test.ts_
  - _Validation:_
    - Full user journey works
    - Error states handled correctly
    - Edge cases covered
  - _Memory Write:_
    - `requirements.json#TEST-002.status` ← "verified"
```

## Validation Checklist

Before finalizing tasks, the system **MUST** verify:

### Traceability
- [ ] All design components have implementation tasks
- [ ] All requirements have at least one task reference
- [ ] No tasks reference non-existent requirements
- [ ] All evidence citations resolve

### Sequencing
- [ ] Tasks properly sequenced by dependency
- [ ] Group metadata correctly specifies dependencies
- [ ] Parallel-safe groups have no interdependencies

### Quality
- [ ] Each task is actionable (clear what to do)
- [ ] Each task has testable validation criteria
- [ ] Task sizes are appropriate (2-4 hours)
- [ ] Memory write operations are specified

### Subagent Readiness
- [ ] Memory context can be generated for each group
- [ ] Constraints are complete and non-contradictory
- [ ] Completion criteria are verifiable

## Common Mistakes (MUST AVOID)

### Missing Dependencies

```markdown
❌ BAD:
- [ ] 1.1 Create login endpoint
- [ ] 1.2 Create auth service (used by 1.1)

✓ CORRECT:
- [ ] 1.1 Create auth service
  - _Precondition: None (foundation)_
- [ ] 1.2 Create login endpoint
  - _Precondition: Task 1.1 complete_
```

### Missing Evidence

```markdown
❌ BAD:
- [ ] 1.1 Implement password hashing
  - _Requirements: AUTH-001_

✓ CORRECT:
- [ ] 1.1 Implement password hashing
  - _Requirements: AUTH-001 (per specs/auth/spec.md#L23)_
  - _Evidence: design.md#decision-password-hashing (DEC-002)_
```

### Vague Validation

```markdown
❌ BAD:
- [ ] 1.1 Implement password hashing
  - _Validation: Works correctly_

✓ CORRECT:
- [ ] 1.1 Implement password hashing
  - _Validation:_
    - Unit tests pass
    - Uses bcrypt with cost factor 12
    - hash() returns valid bcrypt format
    - verify() correctly validates
```

### Missing Memory Write

```markdown
❌ BAD:
- [ ] 1.1 Implement password hashing
  - _Creates: src/auth/utils/hash.ts_

✓ CORRECT:
- [ ] 1.1 Implement password hashing
  - _Creates: src/auth/utils/hash.ts_
  - _Memory Write:_
    - `requirements.json#AUTH-001.status` ← "implemented"
    - `citations.json` ← hash.ts satisfies AUTH-001
```

## Process Summary

1. **Analyze design** → Extract components, decisions, flows
2. **Map requirements** → Link each requirement to design elements
3. **Group work** → Organize into logical groups by layer/feature
4. **Add metadata** → Dependencies, parallel-safety, preconditions
5. **Sequence tasks** → Order by dependency within groups
6. **Size tasks** → Break into 2-4 hour chunks
7. **Add traceability** → Link to requirements, design, evidence
8. **Generate context** → Create memory context for each group
9. **Specify validation** → Define testable completion criteria
10. **Specify memory writes** → Define what to record
11. **Validate** → Check against all checklists
