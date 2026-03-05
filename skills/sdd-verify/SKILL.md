---
name: sdd-verify
description: Verify that implementation matches specifications. Compares code against spec requirements, identifies gaps, inconsistencies, and missing implementations.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "1.0.0"
---

# SDD Verify

Verify that implementation matches specifications.

## When to Use

- Before archiving a change
- After implementing a feature
- During code review
- When inheriting a codebase
- Periodic spec-reality alignment checks

## Verification Levels

| Level | What It Checks | Time |
|-------|----------------|------|
| **Quick** | Files exist, basic structure | Seconds |
| **Standard** | Requirements implemented | Minutes |
| **Deep** | All scenarios covered, edge cases | Longer |

---

## Process

### Step 1: Select What to Verify

```
/sdd-verify

AI: What would you like to verify?

[1] Current change (.specs/changes/<name>)
[2] Specific spec in .specs/specs/
[3] All specs (comprehensive)
[4] Specific capability
```

### Step 2: Load Spec and Code

For each capability being verified:

1. **Read spec requirements**
   ```markdown
   ### Requirement: User Login
   The system SHALL authenticate users via email/password.
   
   #### Scenario: Successful login
   - **WHEN** user provides valid credentials
   - **THEN** system returns JWT token
   ```

2. **Locate implementation files**
   - Use spec's file hints (`_Creates:`, `_Modifies:`)
   - Search for relevant code by function/class names
   - Check test files for scenario coverage

3. **Analyze implementation**
   - Does the code implement the requirement?
   - Are all scenarios handled?
   - Are edge cases covered?

### Step 3: Compare and Report

```
┌──────────────────────────────────────────────────────────────┐
│ VERIFICATION REPORT: authentication                          │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│ Requirements: 4 total                                        │
│                                                              │
│ ✓ IMPLEMENTED (3)                                           │
│   • user-login: Code found in auth/login.ts                  │
│   • token-generation: Implemented in TokenService.ts         │
│   • logout: Handler exists in routes/auth.ts                 │
│                                                              │
│ ⚠ PARTIAL (1)                                               │
│   • rate-limiting: Basic rate limiting exists but:           │
│     - Missing: IP-based blocking                             │
│     - Missing: Exponential backoff                           │
│                                                              │
│ ✗ NOT FOUND (0)                                              │
│                                                              │
│ Scenarios: 12 total                                          │
│ ✓ Covered: 10  ⚠ Partial: 2  ✗ Missing: 0                   │
│                                                              │
│ Tests:                                                       │
│   - Unit tests: Found in tests/auth/                         │
│   - Missing: No test for "rate limited" scenario             │
│                                                              │
│ RECOMMENDATION: Implement missing rate-limiting features     │
│                 before archiving.                            │
│                                                              │
└──────────────────────────────────────────────────────────────┘

Overall: 85% implemented

[1] Show details for partial implementations
[2] Continue anyway (acknowledge gaps)
[3] Stop - I need to fix these first
```

---

## Verification Checks

### Requirement-Level Checks

| Check | Description |
|-------|-------------|
| **Code exists** | Files mentioned in spec exist |
| **Function exists** | Named functions/methods are implemented |
| **Behavior matches** | Code does what spec describes |
| **Error handling** | Error cases from spec are handled |
| **Return values** | Outputs match spec |

### Scenario-Level Checks

| Check | Description |
|-------|-------------|
| **Happy path** | Normal success case works |
| **Error cases** | Error scenarios handled |
| **Edge cases** | Boundary conditions covered |
| **Preconditions** | WHEN conditions checked in code |
| **Postconditions** | THEN outcomes implemented |

### Test Coverage Checks

| Check | Description |
|-------|-------------|
| **Scenario → Test** | Each scenario has corresponding test |
| **Test passes** | Tests actually pass |
| **Test quality** | Tests verify behavior, not just coverage |

---

## Verification Methods

### Method 1: Static Analysis (Quick)

- Check files exist
- Check function signatures
- Check imports/exports
- Check for TODO/FIXME markers

### Method 2: Code Reading (Standard)

- Read implementation code
- Compare to spec requirements
- Trace execution paths
- Identify gaps

### Method 3: Test Execution (Deep)

- Run existing tests
- Map tests to scenarios
- Check test coverage
- Identify untested scenarios

### Method 4: Using Subagents

For comprehensive verification, dispatch subagents:

```markdown
# Subagent Prompt for Verification

Verify that this code implements the spec requirements.

## SPEC REQUIREMENTS

{REQUIREMENTS_FROM_SPEC}

## CODE TO VERIFY

{CODE_FILES}

## YOUR TASK

For each requirement:
1. Check if code implements it
2. Check if all scenarios are handled
3. Identify any gaps or inconsistencies

## OUTPUT FORMAT

Return a verification report:

```
## Requirement: {name}
Status: IMPLEMENTED | PARTIAL | NOT_FOUND

### Evidence
<what you found in code>

### Gaps (if PARTIAL)
<what's missing>

### Scenarios
- Scenario 1: ✓ Covered / ✗ Missing
- Scenario 2: ✓ Covered / ✗ Missing
```

## CONSTRAINTS

- Only report what you can verify from the code
- Mark unclear as "UNCLEAR" with explanation
- Be specific about gaps
```

---

## Handling Verification Failures

### Critical Gaps (Must Fix)

```
✗ CRITICAL: Core requirement not implemented
  - Requirement: user-login
  - Expected: Authentication via email/password
  - Found: No login implementation

ACTION: Cannot archive. Implement requirement first.
```

### Non-Critical Gaps (Can Acknowledge)

```
⚠ NON-CRITICAL: Partial implementation
  - Requirement: rate-limiting
  - Missing: Exponential backoff (nice to have)
  
OPTIONS:
[1] Acknowledge and archive with known gap
[2] Implement before archiving
```

### Unclear Verification

```
? UNCLEAR: Cannot verify from code
  - Requirement: session-timeout
  - Issue: Timeout value not visible in code (config?)
  
OPTIONS:
[1] Mark as "requires manual verification"
[2] Point me to the relevant code
[3] Skip this requirement
```

---

## Pre-Archive Verification

Before `/sdd-archive`, run verification automatically:

```
/sdd-archive

AI: Running pre-archive verification...

┌──────────────────────────────────────────────────────────────┐
│ VERIFICATION SUMMARY                                         │
├──────────────────────────────────────────────────────────────┤
│ Requirements: 5 IMPLEMENTED, 0 PARTIAL, 0 MISSING            │
│ Scenarios: 12 COVERED, 0 MISSING                             │
│ Tests: All passing                                           │
└──────────────────────────────────────────────────────────────┘

✓ Verification passed. Ready to archive.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

NEXT STEP: Confirm to proceed with merging this change
into accumulated specs.

Proceed with archive? [Yes/No/Show details]
```

If verification fails:

```
/sdd-archive

AI: Running pre-archive verification...

┌──────────────────────────────────────────────────────────────┐
│ VERIFICATION FAILED                                          │
├──────────────────────────────────────────────────────────────┤
│ Requirements: 3 IMPLEMENTED, 1 PARTIAL, 1 MISSING            │
│                                                              │
│ ✗ MISSING: two-factor-enrollment                             │
│   Spec requires 2FA enrollment flow                          │
│   No implementation found                                    │
│                                                              │
│ ⚠ PARTIAL: rate-limiting                                     │
│   Basic rate limiting exists                                 │
│   Missing: IP blocking, exponential backoff                  │
└──────────────────────────────────────────────────────────────┘

Cannot archive with unimplemented requirements.

Options:
[1] Show me what needs to be done
[2] Force archive (not recommended)
[3] Continue implementation
```

---

## Periodic Verification

For ongoing spec-reality alignment:

```
/sdd-verify --all

AI: Verifying all specs in .specs/specs/...

┌──────────────────────────────────────────────────────────────┐
│ SPEC DRIFT REPORT                                            │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│ authentication ━━━━━━━━━━━━━━━━━━━━━ 95%                     │
│   ⚠ Code implements timeout differently than spec            │
│                                                              │
│ registration ━━━━━━━━━━━━━━━━━━━━━━ 100%                     │
│                                                              │
│ password-reset ━━━━━━━━━━━━━━━━━━━ 80%                       │
│   ✗ Spec requires email rate limiting (not implemented)      │
│   ⚠ Reset link expiry is 24h (spec says 1h)                 │
│                                                              │
│ session-mgmt ━━━━━━━━━━━━━━━━━━━━━ 100%                      │
│                                                              │
└──────────────────────────────────────────────────────────────┘

Overall alignment: 94%

Spec drift detected. Consider updating specs or implementation.
```

---

## Integration with Workflow

```
# New feature
/sdd-explore
/sdd-propose
/sdd-init-memory
/sdd-artefact-scl
/sdd-apply-group-scl
/sdd-verify-scl           # ← Check before archive
/sdd-archive

# Existing code
/sdd-reverse
/sdd-verify --all         # ← Check if specs match reality
/sdd-explore              # Then explore changes
/sdd-propose              # Create proposal
```

---

## Output Formats

### Summary (Default)

```
✓ 5 requirements verified
⚠ 1 partial implementation
✗ 0 missing implementations

85% implemented
```

### Detailed

Full report with evidence for each requirement.

### JSON (for tooling)

```json
{
  "spec": "authentication",
  "requirements": {
    "total": 5,
    "implemented": 4,
    "partial": 1,
    "missing": 0
  },
  "details": [...]
}
```

---

## Best Practices

### Do
- Verify before archiving
- Run periodic drift checks
- Fix critical gaps immediately
- Document acknowledged gaps

### Don't
- Archive with unimplemented critical requirements
- Ignore verification failures
- Skip verification for "small" changes
- Let specs drift from reality
