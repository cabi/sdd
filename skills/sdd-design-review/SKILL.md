---
name: sdd-design-review
description: Methodology for iterative design review with analyst feedback. Implements a mandatory 5-iteration review loop where design is created, critiqued, and refined to maximize quality.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: advanced
  author: OpenCode
  version: "1.0.0"
---

# SDD Design Review Loop

Iterative design refinement through structured critique and revision cycles.

## Overview

This skill implements a mandatory 5-iteration review loop:

```
┌─────────────────────────────────────────────────────────────────┐
│                    DESIGN REVIEW LOOP                            │
│                                                                   │
│  ┌──────────────────┐                                            │
│  │  Create Design   │                                            │
│  │    (v1)          │                                            │
│  └────────┬─────────┘                                            │
│           │                                                       │
│           ▼                                                       │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  ITERATION 1                                                  │ │
│  │  ┌───────────────┐    ┌────────────────┐                    │ │
│  │  │ sdd-design-   │───►│ review-        │                    │ │
│  │  │ analyst       │    │ iteration-1.md │                    │ │
│  │  └───────────────┘    └───────┬────────┘                    │ │
│  │                               │                              │ │
│  │                               ▼                              │ │
│  │                     ┌──────────────────┐                    │ │
│  │                     │ Revise Design    │                    │ │
│  │                     │ (v2)             │                    │ │
│  │                     └──────────────────┘                    │ │
│  └─────────────────────────────────────────────┬───────────────┘ │
│                                                │                  │
│                                                ▼                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  ITERATION 2 (same process)                                  │ │
│  │  ... → review-iteration-2.md → Design v3                     │ │
│  └─────────────────────────────────────────────┬───────────────┘ │
│                                                │                  │
│                                                ▼                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  ITERATION 3 (same process)                                  │ │
│  │  ... → review-iteration-3.md → Design v4                     │ │
│  └─────────────────────────────────────────────┬───────────────┘ │
│                                                │                  │
│                                                ▼                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  ITERATION 4 (same process)                                  │ │
│  │  ... → review-iteration-4.md → Design v5                     │ │
│  └─────────────────────────────────────────────┬───────────────┘ │
│                                                │                  │
│                                                ▼                  │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │  ITERATION 5 (same process)                                  │ │
│  │  ... → review-iteration-5.md → Design v6 (FINAL)             │ │
│  └─────────────────────────────────────────────┬───────────────┘ │
│                                                │                  │
│                                                ▼                  │
│                                     ┌──────────────────┐          │
│                                     │ Write Final      │          │
│                                     │ design.md        │          │
│                                     └──────────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

## When to Use Review Loop

**ALWAYS use review loop for:**
- New capabilities from scratch
- Security-related changes
- Data model changes with migrations
- Cross-cutting architectural changes
- Changes affecting multiple services

**Review loop adds ~30-40 minutes** but significantly improves design quality.

## Review Loop Protocol

### Iteration Structure

Each iteration follows the same pattern:

```javascript
async function reviewIteration(design, iterationNumber) {
  // 1. Invoke analyst
  const critique = await invokeAgent('sdd-design-analyst', {
    design: design,
    iteration: iterationNumber,
    previousReviews: getPreviousReviews(iterationNumber)
  });
  
  // 2. Save critique report
  writeReport(`review-iteration-${iterationNumber}.md`, critique);
  
  // 3. Enforce mandatory iteration count and final gate
  if (iterationNumber < 5) {
    const revisedDesign = reviseDesign(design, critique);
    documentChanges(iterationNumber, critique.issues, revisedDesign.changes);
    return { approved: false, design: revisedDesign };
  }

  // 4. Iteration 5 final gate
  const hasBlockingIssues = critique.criticalCount > 0 || critique.majorCount > 0;
  if (critique.verdict !== 'APPROVE' || hasBlockingIssues) {
    return { approved: false, design, blocked: 'Final gate failed' };
  }

  // 5. Final polish and completion
  const finalizedDesign = applyFinalPolish(design, critique.suggestions);
  documentRemainingMinors(finalizedDesign, critique.minorFindings);
  documentChanges(iterationNumber, critique.issues, finalizedDesign.changes);
  return { approved: true, design: finalizedDesign };
}
```

### 5-Iteration Mandatory Loop

The loop **MUST** complete all 5 iterations:

| Iteration | Focus | Expected Outcome |
|-----------|-------|------------------|
| **1** | Find all critical issues | Address contradictions, missing sections, coverage gaps |
| **2** | Find major issues, verify fixes | Address anti-patterns, incomplete sections, edge cases |
| **3** | Stress architecture decisions | Resolve bottlenecks, migration and failure-mode gaps |
| **4** | Operability and maintainability | Tighten monitoring, ownership, and runbook clarity |
| **5** | Final verification | APPROVE with 0 critical, 0 major unresolved, unresolved minors documented |

Even if iteration 1-4 returns APPROVE, continue through iteration 5 for deeper analysis.

### Early Termination

The loop **MUST NOT** terminate before iteration 5.

### Minor Findings Policy

- MIN-* findings **SHOULD** be fixed during each iteration.
- Any unresolved MIN-* finding at iteration 5 **MUST** be documented with rationale and follow-up in `Design Iteration History` and/or `Open Questions`.
- Any MIN-* finding that affects security, compliance, data integrity, or requirement coverage **MUST** be reclassified to MAJOR or CRITICAL.

## Revision Guidelines

### Addressing Critical Issues

For each CRITICAL issue:

```markdown
### Change Log Entry

**Issue:** CRIT-001 - Decision DEC-002 contradicts DEC-001
**Resolution:** Updated DEC-002 to align with JWT choice from DEC-001
**Location:** design.md#Decisions → DEC-002

**Before:**
> The system SHALL use session-based authentication...

**After:**
> The system SHALL use JWT tokens for stateless authentication...
> (Aligned with DEC-001)
```

### Addressing Major Issues

For each MAJOR issue:

```markdown
**Issue:** MAJ-003 - AuthService interface undefined
**Resolution:** Added complete interface definition
**Location:** design.md#Components → AuthService
```

### Tracking Changes

Maintain a change log at the end of design.md:

```markdown
## Design Iteration History

### Iteration 5 → Final (Current)
**Issues Addressed:** 2 minor, 0 critical, 0 major
- MIN-001: Clarified retry/backoff behavior in API error paths
- MIN-002: Added explicit monitoring thresholds for alerts

### Iteration 4 → 5
**Issues Addressed:** 2 major, 1 critical
- CRIT-001: Closed remaining requirement coverage gap for REQ-012
- MAJ-001: Added rollback validation steps to migration plan
- MAJ-002: Completed component ownership boundaries

### Iteration 3 → 4
**Issues Addressed:** 3 major, 0 critical
- MAJ-001: Added missing runbook links in alerting section
- MAJ-002: Added data retention strategy for observability data
- MAJ-003: Clarified API idempotency and retry semantics

### Iteration 2 → 3
**Issues Addressed:** 4 major, 2 critical
- CRIT-001: Fixed architecture contradiction in auth/session strategy
- CRIT-002: Added missing migration rollback safety for data changes
- MAJ-001 through MAJ-004: Strengthened edge-case handling and interfaces

### Iteration 1 → 2
**Issues Addressed:** 5 critical, 4 major
- CRIT-001 through CRIT-005: Closed initial requirement coverage gaps
- MAJ-001 through MAJ-004: Completed structural completeness requirements
```

## Review Reports Location

All review reports are saved in the change directory:

```
.specs/changes/<change-name>/
├── design.md              # Final design (after 5 iterations)
├── review-iteration-1.md  # First critique
├── review-iteration-2.md  # Second critique
├── review-iteration-3.md  # Third critique
├── review-iteration-4.md  # Fourth critique
└── review-iteration-5.md  # Fifth critique
```

## Quality Metrics

Track these metrics across iterations:

| Metric | Target |
|--------|--------|
| Critical Issues | 0 by iteration 5 |
| Major Issues | 0 unresolved by iteration 5 |
| Requirements Covered | 100% |
| Sections Complete | 100% |
| Diagrams Valid | 100% |

### Metric Improvement Pattern

```
Iteration 1: 5 critical, 8 major, 12 minor → REVISE
Iteration 2: 1 critical, 3 major, 6 minor → REVISE
Iteration 3: 1 critical, 2 major, 5 minor → REVISE
Iteration 4: 0 critical, 1 major, 4 minor → REVISE
Iteration 5: 0 critical, 0 major, 2 minor → APPROVE
```

## Integration with SDD Workflow

### Command Integration

The review loop is automatically invoked when:
- `/sdd-artefact` creates design.md
- `sdd-design` agent is launched

No separate command needed - review is always performed.

### SCL-Enhanced Integration

For SCL-enhanced workflow:

```javascript
// In sdd-design-scl agent
PHASE 3: CONTROL
  // Existing validation
  ✓ Citation validation
  ✓ Regulation compliance
  
  // NEW: Review loop
  FOR iteration = 1 to 5:
    critique = await invokeAnalyst(design, iteration)
    MEM.write({ type: "control-log", checkpoint: `review-iteration-${iteration}` })
    
    IF critique.verdict !== 'APPROVE' OR iteration < 5:
      design = reviseDesign(design, critique)
      MEM.write({ type: "decisions", updates: design.newDecisions })
```

## Analyst Agent Behavior

The `sdd-design-analyst` agent:

1. **Reads the design** and all context files
2. **Performs systematic analysis** across all categories
3. **Prioritizes issues** by severity
4. **Writes critique report** to `review-iteration-N.md`
5. **Returns verdict** with reasoning

### Verdict Types

| Verdict | Meaning | Action |
|---------|---------|--------|
| **REVISE** | Significant issues found | Must revise before next iteration |
| **CONDITIONAL** | Minor issues only | May proceed after quick fixes |
| **APPROVE** | Ready for implementation | Continue to next iteration or finalize |

## Example: 5-Iteration Review

### Iteration 1

**Analyst finds:**
- CRIT-001: REQ-003 not addressed (password reset)
- CRIT-002: DEC-001 says REST, DEC-005 assumes GraphQL
- MAJ-001: No error handling in token validation flow
- MAJ-002: Missing rate limiting strategy

**Designer revises:**
- Adds password reset to Components and API sections
- Updates DEC-005 to use REST endpoints
- Adds error handling to sequence diagram
- Adds DEC-006 for rate limiting

### Iteration 2

**Analyst finds:**
- MAJ-003: Token expiry not specified in DEC-001
- MAJ-004: No refresh token rotation strategy
- MIN-001: Consider adding token revocation

**Designer revises:**
- Adds token expiry (15 min access, 7 day refresh) to DEC-001
- Adds DEC-007 for refresh token rotation
- Adds token revocation to Risks section

### Iteration 3

**Analyst finds:**
- CRIT-003: Migration rollback misses data reconciliation step
- MAJ-005: Alert thresholds too vague for operations

**Designer revises:**
- Adds explicit rollback reconciliation sequence
- Adds concrete alert thresholds and paging policy

### Iteration 4

**Analyst finds:**
- MAJ-006: Component ownership still ambiguous between AuthService and SessionService
- MIN-002: Could clarify database index strategy

**Designer revises:**
- Clarifies ownership boundaries and interface contracts
- Adds index recommendations to Data Models

### Iteration 5

**Analyst finds:**
- MIN-003: Add example curl commands to API section

**Verdict:** APPROVE

**Designer finalizes:**
- Adds curl examples to API endpoints
- Writes final design.md and confirms 0 critical / 0 major unresolved

## Anti-Patterns to Avoid

### 1. Skipping Iterations

❌ **BAD:** "Iteration 1 looks good, let's stop"
✅ **GOOD:** Complete all 5 iterations for thorough review

### 2. Not Addressing All Issues

❌ **BAD:** "I'll fix the critical ones, skip the majors"
✅ **GOOD:** Address ALL critical and major issues each iteration

### 2b. Misclassifying Minors

❌ **BAD:** "This security concern is minor"
✅ **GOOD:** Reclassify security/compliance/data integrity/coverage-impacting items to MAJOR/CRITICAL

### 3. Repeating the Same Design

❌ **BAD:** Submit unchanged design to next iteration
✅ **GOOD:** Document specific changes made

### 4. Ignoring Previous Reviews

❌ **BAD:** Analyst re-finds the same issue from iteration 1
✅ **GOOD:** Designer confirms fix, analyst verifies

## Checklist: Before Each Iteration

- [ ] Previous review report read
- [ ] All issues from previous iteration addressed
- [ ] Changes documented in design
- [ ] No new contradictions introduced
- [ ] All requirements still covered after changes

## Checklist: After 5 Iterations

- [ ] 0 critical issues remaining
- [ ] 0 major unresolved issues remaining
- [ ] Unresolved minor issues (if any) documented with rationale and follow-up
- [ ] 100% requirement coverage confirmed
- [ ] All sections complete with substance
- [ ] Change log shows progression
- [ ] All review reports saved
