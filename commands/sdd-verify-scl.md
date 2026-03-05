---
name: sdd-verify-scl
description: Verify implementation matches spec using SCL memory and control validation
---

Verify that the implementation matches the specification using SCL-enhanced verification with memory tracing and control checkpoints.

**Usage:** `/sdd-verify-scl [options]`

**Options:**
- `--deep`: Perform deep verification (slower, more thorough)
- `--quick`: Quick verification (faster, less thorough)
- `--fix`: Attempt to fix minor issues automatically

**Process (SCL-Enhanced):**

## Phase 1: Retrieve

1. **Detect current change** from `.specs/changes/`
2. **Load memory state**:
   - `.memory/decisions.json` - All decisions made
   - `.memory/requirements.json` - Requirement status
   - `.memory/citations.json` - Implementation citations
   - `.memory/control-log.json` - Prior checkpoints
3. **Load artifacts**:
   - `proposal.md` - Original goals
   - `specs/**/*.md` - Requirements
   - `design.md` - Technical approach
   - `tasks.md` - Implementation tasks

## Phase 2: Requirement Verification

4. **For each requirement**:
   - Check implementation exists
   - Verify citations in code
   - Verify tests exist and pass
   - Calculate coverage

5. **Generate requirement matrix**:
   ```
   | REQ-ID | Status | Implementation | Tests | Citations |
   |--------|--------|----------------|-------|-----------|
   | AUTH-001 | ✓ | hash.ts:L12-34 | ✓ | 3 |
   | AUTH-002 | ✓ | hash.ts:L36-45 | ✓ | 2 |
   | AUTH-003 | ⚠ | TokenService.ts | ✗ | 1 |
   ```

## Phase 3: Decision Verification

6. **For each decision**:
   - Verify implementation follows decision
   - Check for superseded decisions
   - Identify any contradictions

7. **Generate decision compliance**:
   ```
   | DEC-ID | Decision | Compliant | Evidence |
   |--------|----------|-----------|----------|
   | DEC-001 | JWT for sessions | ✓ | TokenService.ts:L5 |
   | DEC-002 | Bcrypt cost 12 | ⚠ | hash.ts uses 10 |
   | DEC-003 | 1-hour expiry | ✓ | TokenService.ts:L23 |
   ```

## Phase 4: Citation Verification

8. **For each citation**:
   - Verify target exists
   - Verify relationship is valid
   - Check for broken links

9. **Generate citation report**:
   ```
   Citations: 34 total
   - Valid: 32
   - Broken: 2 (CIT-012, CIT-027)
   - Unverified: 0
   ```

## Phase 5: Memory Consistency

10. **Check memory consistency**:
    - All implemented requirements have status="implemented"
    - All task completions logged in episodes
    - No orphaned citations (citing deleted content)

## Phase 6: Goal Fidelity

11. **Calculate goal fidelity score**:
    - Requirements implemented / total requirements
    - Tests passing / total tests
    - Citations valid / total citations
    - Memory consistency score

12. **Generate fidelity report**

## Phase 7: Gap Analysis

13. **Identify gaps**:
    - Missing implementations
    - Missing tests
    - Broken citations
    - Memory inconsistencies

14. **Classify gaps**:
    - **Critical**: Core requirement not implemented
    - **Major**: Important feature incomplete
    - **Minor**: Nice-to-have missing
    - **Cosmetic**: Documentation/cleanup needed

**Output:**
```
═══════════════════════════════════════════════════════════════
SCL Verification Report: user-authentication
═══════════════════════════════════════════════════════════════

## Summary

Goal Fidelity: 0.87 (GOOD)
- Requirements: 10/12 implemented (83%)
- Tests: 18/20 passing (90%)
- Citations: 32/34 valid (94%)
- Memory: Consistent

## Requirement Status

| Status | Count | Percentage |
|--------|-------|------------|
| ✓ Verified | 8 | 67% |
| ⚠ Implemented (unverified) | 2 | 17% |
| ✗ Not implemented | 2 | 17% |

## Decision Compliance

Compliant: 7/8 (88%)
Violations:
  - DEC-002: Bcrypt cost factor is 10, should be 12
    Location: src/auth/utils/hash.ts:L15

## Citation Integrity

Valid: 32/34 (94%)
Broken:
  - CIT-012: design.md#L999 (line does not exist)
  - CIT-027: specs/auth/spec.md#L200 (section removed)

## Gap Analysis

### Critical (MUST fix before archive)
- [ ] AUTH-011: Password reset not implemented
- [ ] AUTH-012: Email verification not implemented

### Major (SHOULD fix)
- [ ] DEC-002 violation: Update bcrypt cost factor to 12
- [ ] Missing test for AUTH-003 (token refresh)

### Minor (MAY fix)
- [ ] CIT-012: Fix or remove broken citation
- [ ] CIT-027: Fix or remove broken citation
- [ ] Add integration test for logout flow

## Memory Consistency

[✓] All implemented requirements have correct status
[✓] All task completions logged
[✓] No orphaned citations
[✓] No contradictions in decisions

## Recommendation

⚠ NOT READY FOR ARCHIVE

Required actions:
1. Implement AUTH-011 and AUTH-012 (critical)
2. Fix DEC-002 violation (major)
3. Fix broken citations (minor)

After fixes, re-run /sdd-verify-scl to confirm readiness.

══════════════════════════════════════════════════════════════
```

**When verification passes (ready for archive):**
```
═══════════════════════════════════════════════════════════════
✓ VERIFICATION PASSED
═══════════════════════════════════════════════════════════════

All requirements implemented and verified:
- 10/10 requirements with status="implemented"
- 24/24 tests passing
- 42/42 citations valid
- Memory consistent across all artifacts
- Goal fidelity: 1.0 (100%)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

NEXT STEP: Run /sdd-archive to merge this change into
accumulated specs and complete the workflow.

═══════════════════════════════════════════════════════════════
```

**With --fix option:**
```
...
Auto-fixing minor issues:
  ✓ Removed broken citation CIT-012
  ✓ Removed broken citation CIT-027
  ✓ Updated memory status for AUTH-003

Re-verification after fixes:
  Citations: 32/32 valid (100%)

Remaining issues require manual intervention.
```

---

## Valid Next Commands

**If verification passed:**
- `/sdd-archive` - Archive the completed change
- `/sdd-status` - Review final status
- `/sdd-memory-status` - View final memory state

**If verification failed:**
- `/sdd-apply-group-scl N` - Fix failed tasks in specific group
- `/sdd-apply-all-scl` - Re-execute all groups after fixes
- `/sdd-status` - See which tasks failed
- `/sdd-memory-status` - Debug memory inconsistencies

**Do NOT suggest:**
- ❌ `/sdd-artefact-scl` (already completed)
- ❌ `/sdd-init-memory` (already completed)
- ❌ `/sdd-verify` (use /sdd-verify-scl for SCL workflow)

**Loads skills:** `sdd-memory`, `sdd-control`
