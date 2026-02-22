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

2. **Load requirements:**
   - Parse spec for requirements and scenarios
   - Identify expected files from metadata

3. **Verify implementation:**
   - Check if code exists
   - Compare behavior to spec
   - Check scenario coverage
   - Verify tests exist

4. **Report (with gaps):**
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
   └──────────────────────────────────────────────────────────────┘
   
   Overall: 85% implemented
   
   [1] Show details
   [2] Continue anyway
   [3] Fix issues first
   ```

5. **Report (verification passed):**
   ```
   ┌──────────────────────────────────────────────────────────────┐
   │ VERIFICATION REPORT: authentication                          │
   ├──────────────────────────────────────────────────────────────┤
   │ Requirements: 5 IMPLEMENTED, 0 PARTIAL, 0 MISSING            │
   │ Scenarios: 12 COVERED, 0 MISSING                             │
   │ Tests: All passing                                           │
   └──────────────────────────────────────────────────────────────┘
   
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
