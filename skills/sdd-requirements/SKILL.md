---
name: sdd-requirements
description: Requirements engineering using EARS format. Creates unambiguous, testable requirements with WHEN/THEN/SHALL patterns. Includes validation checklists for completeness and consistency.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: beginner
  author: OpenCode
  version: "1.0.0"
---

# SDD Requirements Engineering

Master the EARS format for writing clear, testable requirements.

## What is EARS?

**Easy Approach to Requirements Syntax** - A structured format that eliminates ambiguity and makes requirements testable.

## EARS Patterns

### 1. Ubiquitous Requirements
Always applies, no conditions.

```
The system SHALL <behavior>
```

Example:
```
The system SHALL encrypt all stored passwords using bcrypt.
```

### 2. Event-Driven Requirements
Triggered by an event.

```
WHEN <event> THEN the system SHALL <response>
```

Example:
```
WHEN user submits login form THEN the system SHALL validate credentials.
```

### 3. State-Driven Requirements
Applies while in a specific state.

```
WHILE <state> the system SHALL <behavior>
```

Example:
```
WHILE user is authenticated the system SHALL maintain session state.
```

### 4. Optional Requirements
Only applies when a feature is enabled.

```
WHERE <feature> is enabled, the system SHALL <behavior>
```

Example:
```
WHERE two-factor auth is enabled, the system SHALL require verification code.
```

### 5. Unwanted Behavior Requirements
Prevents something from happening.

```
The system SHALL NOT <behavior>
```

Example:
```
The system SHALL NOT store plain text passwords.
```

### 6. Complex Requirements
Multiple conditions.

```
WHEN <event> AND <condition> THEN the system SHALL <response>
```

Example:
```
WHEN user requests password reset AND email is valid 
THEN the system SHALL send reset link within 5 minutes.
```

## Scenario Format

Every requirement should have scenarios:

```markdown
#### Scenario: <scenario-name>
- **WHEN** <event or condition>
- **THEN** <expected system behavior>
```

### Scenario Types

| Type | Purpose | Example |
|------|---------|---------|
| **Happy Path** | Normal successful flow | Valid input → Success |
| **Edge Case** | Boundary conditions | Empty input, max length |
| **Error Case** | Failure handling | Invalid input → Error message |
| **Alternative** | Other valid paths | Different auth method |

## Spec Level

Specifications define the required behavior and boundaries of the system, not the internal design.

Specs SHOULD contain:

- externally observable behavior
- invariants that must remain true
- compatibility constraints
- data constraints and preservation rules
- migration expectations when behavior or interfaces change

Specs SHOULD NOT contain:

- class or module layout
- helper extraction or decomposition
- naming of internal abstractions
- pattern choices unless they are true architectural constraints
- implementation steps or task-level execution details

Rule of thumb:
If a statement describes what users, callers, operators, or downstream systems can observe or rely on, it belongs in the spec.
If a statement describes how the code should be organized internally, it belongs in `design.md`.

## Writing Good Requirements

### DO ✓

- Use SHALL or MUST for mandatory behavior
- Be specific and measurable
- One behavior per requirement
- Cover all user roles
- Include error handling

### DON'T ✗

- Use vague terms (fast, user-friendly, good)
- Combine multiple behaviors
- Leave error cases unspecified
- Use SHOULD or MAY (too ambiguous)
- Include implementation details
- Specify internal module or class structure
- Require helper extraction strategy
- Encode design patterns unless they are externally mandated

## Requirement Structure

```markdown
### Requirement: <name>

The system SHALL <specific behavior>.

#### Scenario: <happy-path>
- **WHEN** <normal condition>
- **THEN** <expected success>

#### Scenario: <error-case>
- **WHEN** <error condition>
- **THEN** <expected error handling>

#### Scenario: <edge-case>
- **WHEN** <boundary condition>
- **THEN** <expected behavior>
```

## Validation Checklist

Before finalizing requirements, verify:

### Completeness
- [ ] All user roles identified and addressed
- [ ] Normal (happy path) cases covered
- [ ] Edge cases covered (boundaries, limits)
- [ ] Error cases covered (failures, invalid input)
- [ ] Business rules captured
- [ ] Constraints documented

### Clarity
- [ ] Each requirement has one clear behavior
- [ ] No ambiguous terms without definitions
- [ ] Specific, measurable outcomes
- [ ] Consistent terminology throughout

### Consistency
- [ ] EARS format used throughout
- [ ] SHALL/MUST used for all requirements
- [ ] No conflicting requirements
- [ ] Related requirements grouped together

### Testability
- [ ] Each requirement can be verified
- [ ] Inputs and outputs are specified
- [ ] Success criteria are measurable
- [ ] Each scenario is a potential test case

## Common Mistakes

### Mistake 1: Vague Requirements

❌ Bad:
```
The system should be fast.
```

✓ Good:
```
The system SHALL respond to API requests within 200ms under normal load.
```

### Mistake 2: Implementation Details

❌ Bad:
```
The system SHALL use Redis for session storage.
```

✓ Good:
```
The system SHALL persist session state across server restarts.
```
(Implementation can choose Redis or other solutions)

### Mistake 3: Missing Error Handling

❌ Bad:
```
WHEN user submits form THEN the system SHALL save data.
```

✓ Good:
```
WHEN user submits form with valid data THEN the system SHALL save data.
WHEN user submits form with invalid data THEN the system SHALL display validation errors.
```

### Mistake 4: Combining Behaviors

❌ Bad:
```
The system SHALL authenticate users and log access attempts.
```

✓ Good:
```
The system SHALL authenticate users using credentials.
The system SHALL log all authentication attempts.
```

## Delta Spec Operations

### ADDED Requirements
New capabilities not in existing specs.

```markdown
## ADDED Requirements

### Requirement: User can export data
The system SHALL allow users to export their data in CSV format.

#### Scenario: Successful export
- **WHEN** user clicks "Export"
- **THEN** system downloads CSV file
```

### MODIFIED Requirements
Changed behavior - MUST include full content.

```markdown
## MODIFIED Requirements

### Requirement: Password requirements
The system SHALL require passwords with minimum 12 characters, 
including uppercase, lowercase, number, and special character.

#### Scenario: Weak password rejected
- **WHEN** user enters password shorter than 12 characters
- **THEN** system SHALL display "Password must be at least 12 characters"
```

### REMOVED Requirements
Deprecated features.

```markdown
## REMOVED Requirements

### Requirement: Legacy login
**Reason**: Replaced by OAuth integration
**Migration**: Use /auth/oauth endpoint instead of /auth/legacy
```

## Process

1. **Gather** user stories and acceptance criteria
2. **Transform** to EARS format
3. **Add scenarios** for each requirement
4. **Validate** against checklist
5. **Review** with stakeholders
6. **Iterate** based on feedback
