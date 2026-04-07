---
name: sdd-testing
description: Testing strategy integration for SDD. Maps EARS scenarios to test cases, defines test traceability matrix format, and provides test scaffolding patterns for requirement-to-test traceability. Enforces that every requirement scenario has corresponding test tasks.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "1.0.0"
---

# SDD Testing Strategy

Map EARS requirement scenarios to testable, traceable test cases.

## Core Principle

Every EARS scenario **MUST** have a corresponding test task. If a scenario is worth specifying, it is worth testing.

## Test Traceability Matrix

The traceability matrix maps each EARS scenario to its test case. This matrix **MUST** be produced during task creation and verified during `/sdd-verify`.

### Matrix Format

```markdown
| Requirement | Scenario | Test Task | Test File |
|-------------|----------|-----------|-----------|
| AUTH-001 | successful-login | 4.1 | tests/auth/login.test.ts |
| AUTH-001 | invalid-password | 4.1 | tests/auth/login.test.ts |
| AUTH-001 | account-locked | 4.1 | tests/auth/login.test.ts |
| AUTH-002 | token-generated | 4.2 | tests/auth/token.test.ts |
| AUTH-002 | token-expired | 4.2 | tests/auth/token.test.ts |
| AUTH-002 | token-refreshed | 4.2 | tests/auth/token.test.ts |
```

### Matrix Location

Include the traceability matrix as an appendix in `tasks.md`:

```markdown
## Appendix: Test Traceability Matrix

| Requirement | Scenario | Test Task | Test File |
|-------------|----------|-----------|-----------|
| ... | ... | ... | ... |
```

## Scenario-to-Test Mapping

### EARS Pattern → Test Type

| EARS Pattern | Scenario Type | Test Type | What to Assert |
|--------------|---------------|-----------|----------------|
| `The system SHALL <behavior>` | Ubiquitous | Unit | Behavior always holds |
| `WHEN <event> THEN system SHALL <response>` | Event-Driven | Integration | Event triggers correct response |
| `WHILE <state> system SHALL <behavior>` | State-Driven | Unit + State | Behavior holds in given state |
| `WHERE <feature> is enabled, system SHALL` | Optional | Unit + Feature flag | Behavior conditional on feature |
| `WHEN <event> IF <condition> THEN system SHALL` | Exception | Unit + Error | Condition handled correctly |

### Scenario Classification → Test Coverage

| Scenario Type | Source in Spec | Required Test Coverage |
|---------------|---------------|----------------------|
| **Happy Path** | `WHEN ... THEN ...` (normal flow) | At least 1 test per scenario |
| **Error Case** | `IF <error condition> THEN ...` | At least 1 test per error |
| **Edge Case** | Boundary values, empty inputs, limits | At least 1 test per boundary |
| **Alternative** | `WHERE <feature> is enabled/disabled` | Tests for both states |

## Test Task Format

### Required Fields

Every implementation task that creates or modifies functional code **SHOULD** have a corresponding test task. Test tasks use the standard task format with an additional `_Tests:` field:

```markdown
- [ ] 2.1 Implement AuthService.login() method
  - Validate credentials against database
  - Generate JWT token on success
  - _Requirements: AUTH-001_
  - _Creates: src/auth/service/AuthService.ts_
  - _Tests: 4.1_

- [ ] 4.1 Unit tests for AuthService.login()
  - Test successful login with valid credentials
  - Test failed login with invalid password
  - Test account lockout after 5 failures
  - Test concurrent login attempts
  - _Requirements: AUTH-001_
  - _Creates: tests/auth/service/AuthService.test.ts_
  - _Tests: 2.1_
```

### _Tests: Field

The `_Tests:` field creates bidirectional traceability between implementation and test tasks:

| Field | Direction | Meaning |
|-------|-----------|---------|
| `_Tests: 4.1` on task 2.1 | Impl → Test | "Task 2.1 is tested by task 4.1" |
| `_Tests: 2.1` on task 4.1 | Test → Impl | "Task 4.1 tests task 2.1" |

This enables:
- Finding which tests cover a given implementation
- Finding which implementation a test verifies
- Detecting untested implementation tasks
- Detecting orphaned test tasks

## Test Group Organization

### Option A: Dedicated Test Group

Separate test tasks into their own group:

```markdown
## 4. Testing
_Meta: sequential, depends on: 1, 2, 3_

- [ ] 4.1 Unit tests for AuthService
  - _Requirements: AUTH-001, AUTH-002_
  - _Creates: tests/auth/service/AuthService.test.ts_
  - _Tests: 2.1_

- [ ] 4.2 Integration tests for login flow
  - _Requirements: AUTH-001_
  - _Creates: tests/auth/integration/login.test.ts_
  - _Tests: 3.1_
```

**Best for:** Projects where tests are managed separately.

### Option B: Inline Test Tasks

Include test tasks within each implementation group:

```markdown
## 2. Core Implementation
_Meta: sequential, depends on: 1_

- [ ] 2.1 Implement AuthService.login()
  - _Requirements: AUTH-001_
  - _Creates: src/auth/service/AuthService.ts_
  - _Tests: 2.2_

- [ ] 2.2 Test AuthService.login()
  - _Requirements: AUTH-001_
  - _Creates: tests/auth/service/AuthService.test.ts_
  - _Tests: 2.1_
```

**Best for:** Feature-slice organization, immediate test feedback.

### Option C: Hybrid

Unit tests inline, integration tests in dedicated group:

```markdown
## 2. Core Implementation
- [ ] 2.1 Implement AuthService
- [ ] 2.2 Test AuthService (unit)

## 5. Integration Testing
_Meta: depends on: 2, 3_
- [ ] 5.1 Test full login flow (integration)
```

**Best for:** Most projects. Recommended default.

## Test Naming Convention

Test names **SHOULD** follow a pattern derived from the requirement:

```
<test-level> / <module> / <requirement-id> . <scenario-name> . test . <ext>
```

**Examples:**

| Level | Path | Requirement | File |
|-------|------|-------------|------|
| Unit | tests/auth/service/ | AUTH-001 | successful-login.test.ts |
| Unit | tests/auth/service/ | AUTH-001 | invalid-password.test.ts |
| Integration | tests/auth/integration/ | AUTH-001 | login-flow.test.ts |
| E2E | tests/e2e/auth/ | AUTH-001 | full-auth-journey.test.ts |

### Test Function Naming

Within test files, describe blocks **SHOULD** reference the scenario:

```typescript
describe('AUTH-001: User Login', () => {
  it('WHEN valid credentials THEN returns JWT token')
  it('WHEN invalid password THEN returns 401')
  it('WHEN 5 failed attempts THEN account locked')
})
```

## Test Scaffolding from EARS Scenarios

### Pattern: WHEN/THEN → Test Case

Given EARS scenario:
```markdown
#### Scenario: Successful login
- **WHEN** user provides valid email and password
- **THEN** system returns JWT token with 1-hour expiry
```

Generate test skeleton:
```typescript
it('WHEN user provides valid email and password THEN system returns JWT token with 1-hour expiry', async () => {
  // Arrange: set up valid user in test database
  
  // Act: call login endpoint
  
  // Assert: response contains token, token has 1-hour expiry
})
```

### Pattern: Exception → Test Case

Given EARS scenario:
```markdown
#### Scenario: Account locked
- **WHEN** user attempts login
- **IF** account is locked
- **THEN** system returns 403 with lockout reason
```

Generate test skeleton:
```typescript
it('WHEN user attempts login IF account is locked THEN system returns 403 with lockout reason', async () => {
  // Arrange: create locked user account
  
  // Act: attempt login
  
  // Assert: response status 403, body contains lockout reason
})
```

### Pattern: State → Test Case

Given EARS scenario:
```markdown
#### Scenario: Token already valid
- **WHILE** user has valid session token
- **THEN** system SHALL not generate new token
```

Generate test skeleton:
```typescript
it('WHILE user has valid session token THEN system does not generate new token', async () => {
  // Arrange: authenticate user, get valid token
  
  // Act: attempt re-authentication with valid token
  
  // Assert: same token returned, no new token generated
})
```

## Testing Checklist for Task Review

When reviewing tasks for test coverage:

- [ ] Every requirement scenario has at least one test task
- [ ] Implementation tasks have `_Tests:` references
- [ ] Test tasks have `_Tests:` references back to implementation
- [ ] Error scenarios have dedicated test tasks
- [ ] Edge cases have dedicated test tasks
- [ ] Integration tests exist for multi-component changes
- [ ] Test file paths follow project conventions
- [ ] Test names reference requirement IDs and scenarios

## Integration Points

### During Task Creation

The `sdd-task` agent **MUST**:
1. Generate test tasks alongside implementation tasks
2. Create the test traceability matrix appendix
3. Add `_Tests:` bidirectional references

### During Task Review

The `sdd-task-analyst` agent **MUST**:
1. Verify every requirement scenario has test coverage
2. Flag implementation tasks without corresponding test tasks
3. Validate `_Tests:` references are bidirectional
4. Check for missing test types (error, edge, integration)

### During Verification

The `/sdd-verify` command **MUST**:
1. Build the test traceability matrix from specs and code
2. Identify scenarios without test coverage
3. Check that test files exist and reference correct scenarios
4. Report test coverage gaps as blocking (critical scenarios) or advisory (non-critical)
