---
name: sdd-verify
description: Verify implementation matches specifications
---

Verify that the implementation matches the spec requirements.

**Usage:** `/sdd-verify [options]`

**Options:**
- No args - Verify current change
- `--spec <name>` - Verify specific spec in `.specs/specs/`
- `--all` - Verify all specs (drift check)
- `--quick` - Quick check (files exist, basic structure)
- `--deep` - Deep check (all scenarios, tests)

**Process:**

1. **Select scope:**
   - Current change in `.specs/changes/`
   - Specific spec in `.specs/specs/`
   - All specs

1.5. **Detect change type (for current change):**
   - Read `proposal.md` from change directory
   - Extract `change_type` from "Change Type" section
   - If `change_type` ∈ {removal, rebuild}: switch to Migration Verification mode
   - If `change_type` ∈ {addition, modification, refactor}: use Standard Verification mode
   - If not found: default to Standard Verification mode

2. **Load requirements:**
   - Parse spec for requirements and scenarios
   - Identify expected files from metadata

3. **Verify implementation:**
   - Check if code exists
   - Compare behavior to spec
   - Check scenario coverage
   - Verify tests exist

3.5. **Build test traceability matrix:**
   - Parse specs for all EARS scenarios
   - Parse tasks.md for `_Tests:` bidirectional references
   - Search codebase for test files matching task file hints
   - Map each scenario to its test (or flag as missing)
   - Critical scenarios without tests → BLOCKING
   - Non-critical scenarios without tests → ADVISORY

4. **Run validation checklists:**

   **Requirement Completeness:**
   - [ ] Are all requirements testable and measurable?
   - [ ] Are error cases and edge cases covered in implementation?
   - [ ] Do any implementations conflict with each other?
   - [ ] Are there gaps in the user journey?
   - [ ] Do implementations map to all scenarios from specs?

   - [ ] Does every EARS scenario have a corresponding test?

    **Design Fidelity:**
    - [ ] Does implementation follow the architecture from design?
    - [ ] Were decisions honored? (If deviated, is it documented?)
    - [ ] Are component interfaces as designed?
    - [ ] Do data models match the schema from design?
    - [ ] Does implementation follow existing codebase patterns?
    - [ ] Does implementation respect module boundaries?

    **Constraint Verification:**
    - [ ] Are compliance requirements (GDPR, accessibility, etc.) implemented?
    - [ ] Are integration dependencies working as specified?
    - [ ] **Standard mode:** Are behavioral boundaries (backward compatibility, data compatibility) respected?
    - [ ] **Migration mode:** Are removed behaviors documented? Is migration path tested?
    - [ ] Are all constraints from proposal.md addressed?

    **System Fit:**
    - [ ] Were existing tests updated (not broken)?
    - [ ] Are new dependencies justified in design decisions?
    - [ ] **Standard mode:** Do API changes maintain backward compatibility?
    - [ ] **Migration mode:** Are all REMOVED requirements fully removed from codebase? Is migration path implemented and tested?

  5. **Build test traceability matrix:**
   For each EARS scenario in specs:
   - Find corresponding test file in codebase
   - Check if test file references the correct scenario
   - Identify scenarios without test coverage

   - Flag critical scenarios without tests as **BLOCKING**
   - Flag non-critical scenarios without tests as **ADVISORY**

 6. **Run system fit check:**
   For each modified file:
   - Does implementation follow existing patterns?
   - Does it stay within module boundaries?
   - Were existing tests updated?

   - Are new dependencies justified?

 7. **Report (with gaps):**
    ```
    ┌──────────────────────────────────────────────────────────────┐
    │ VERIFICATION REPORT: authentication                          │
    ├──────────────────────────────────────────────────────────────┤
    │ Requirements: 4 IMPLEMENTED, 1 PARTIAL, 0 MISSING            │
    │ Scenarios: 10 COVERED, 2 MISSING                             │
    │                                                              │
    │ ✓ IMPLEMENTED: user-login, token-generation, logout         │
    │ ⚠ PARTIAL: rate-limiting (missing IP blocking)              │
    │ ✗ MISSING: none                                              │
    │                                                              │
     │ Tests: 8/10 scenarios tested                                 │
     │ Test Traceability:                                │
     │   AUTH-001: login-success → tests/auth/login.test.ts      │
     │   AUTH-001: login-failure → tests/auth/login.test.ts      │
     │   AUTH-002: token-expiry  -> ✗ NO TEST                    │
     │ System Fit:                                              │
     │ ✓ Pattern: Follows existing service layer patterns           │
     │ ✓ Boundaries: Changes stay within auth/ module               │
     │ ⚠ Deps: bcrypt not in existing deps (justified: DEC-003)    │
     ├──────────────────────────────────────────────────────────────┤
     │ VALIDATION CHECKLISTS                                        │
     │ Requirement Completeness: 4/5 pass                           │
     │ Design Fidelity: 3/4 pass                                    │
     │ Constraint Verification: 3/3 pass                            │
     │ System Fit: 4/5 pass                                         │
     │ Test Traceability: 8/10 pass                                   │
     │                                                              │
     │ ⚠ CHECK: Error cases and edge cases not fully covered       │
     │ ⚠ CHECK: Decision DEC-002 deviated (not documented)         │
     │ ✗ CHECK: No test for token-expiry scenario                  │
     └──────────────────────────────────────────────────────────────┘
    
    Overall: 85% implemented
    
    [1] Show details
    [2] Continue anyway
    [3] Fix issues first
    ```

6. **Report (verification passed):**
   ```
   ┌──────────────────────────────────────────────────────────────┐
   │ VERIFICATION REPORT: authentication                          │
   ├──────────────────────────────────────────────────────────────┤
   │ Requirements: 5 IMPLEMENTED, 0 PARTIAL, 0 MISSING            │
   │ Scenarios: 12 COVERED, 0 MISSING                             │
   │ Tests: All passing                                           │
   │ Test Traceability: 100% scenarios mapped to tests              │
   │ System Fit: All checks passed                                │
   └──────────────────────────────────────────────────────────────┘
   │                                                              │
   │ ✓ Verification passed. All requirements verified.           │
   │ ✓ Test traceability complete. No uncovered scenarios.        │
   │ ✓ System fit verified. Implementation fits the codebase.     │
   
   ✓ Verification passed. All requirements verified.
   
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   
   NEXT STEP: Run /sdd-archive to merge this change into
   accumulated specs and complete the workflow.
   ```

**Use when:**
   - Before archiving a change
   - After implementing features
   - Checking for spec drift
   - Code review preparation

---

## Valid Next Commands

**If verification passed:**
- `/sdd-archive` - Archive the completed change
- `/sdd-status` - Review final status

**If verification failed:**
- `/sdd-apply` - Fix failed tasks one at a time
- `/sdd-apply-group N` - Fix failed tasks in specific group
- `/sdd-status` - See which tasks failed

**Do NOT suggest:**
- ❌ `/sdd-artefact` (already completed)
