---
name: sdd-design-review
description: Methodology for iterative design review with analyst feedback. Implements 3-iteration review loop where design is created, critiqued, and refined until quality standards are met.
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

This skill implements a 3-iteration review loop:

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
│  │  ... → review-iteration-3.md → Design v4 (FINAL)             │ │
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

**Review loop adds ~15-20 minutes** but significantly improves design quality.

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
  
  // 3. Check verdict
  if (critique.verdict === 'APPROVE' && iterationNumber >= 3) {
    return { approved: true, design };
  }
  
  // 4. Revise design addressing all issues
  const revisedDesign = reviseDesign(design, critique);
  
  // 5. Document changes
  documentChanges(iterationNumber, critique.issues, revisedDesign.changes);
  
  return { approved: false, design: revisedDesign };
}
```

### 3-Iteration Minimum

The loop **MUST** complete at least 3 iterations:

| Iteration | Focus | Expected Outcome |
|-----------|-------|------------------|
| **1** | Find all critical issues | Address contradictions, missing sections, coverage gaps |
| **2** | Find major issues, verify fixes | Address anti-patterns, incomplete sections, edge cases |
| **3** | Polish, final verification | Minor issues, suggestions, final approval |

Even if iteration 1 returns APPROVE, continue to iterations 2 and 3 for deeper analysis.

### Early Termination

The loop MAY terminate early (after iteration 3) if:
- All iterations return APPROVE
- No new issues found in latest iteration
- All previous issues are confirmed fixed

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

### Iteration 3 → Final (Current)
**Issues Addressed:** 2 minor, 0 critical/major
- MIN-001: Added connection pooling details to DEC-004
- MIN-002: Clarified error handling in API section

### Iteration 2 → 3
**Issues Addressed:** 3 major, 1 critical
- CRIT-001: Fixed contradiction between DEC-001 and DEC-002
- MAJ-001: Added missing API endpoints for token refresh
- MAJ-002: Completed testability section with coverage targets
- MAJ-003: Defined AuthService interface

### Iteration 1 → 2
**Issues Addressed:** 5 critical, 4 major
- CRIT-001 through CRIT-005: All requirement coverage gaps
- MAJ-001 through MAJ-004: All structural completeness issues
```

## Review Reports Location

All review reports are saved in the change directory:

```
.specs/changes/<change-name>/
├── design.md              # Final design (after 3 iterations)
├── review-iteration-1.md  # First critique
├── review-iteration-2.md  # Second critique
└── review-iteration-3.md  # Third critique
```

## Quality Metrics

Track these metrics across iterations:

| Metric | Target |
|--------|--------|
| Critical Issues | 0 by iteration 3 |
| Major Issues | ≤2 by iteration 3 |
| Requirements Covered | 100% |
| Sections Complete | 100% |
| Diagrams Valid | 100% |

### Metric Improvement Pattern

```
Iteration 1: 5 critical, 8 major, 12 minor → REVISE
Iteration 2: 1 critical, 3 major, 6 minor → REVISE
Iteration 3: 0 critical, 1 major, 3 minor → APPROVE
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
  FOR iteration = 1 to 3:
    critique = await invokeAnalyst(design, iteration)
    MEM.write({ type: "control-log", checkpoint: `review-iteration-${iteration}` })
    
    IF critique.verdict !== 'APPROVE' OR iteration < 3:
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

## Example: 3-Iteration Review

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
- MIN-002: Could clarify database index strategy
- MIN-003: Add example curl commands to API section

**Verdict:** APPROVE

**Designer finalizes:**
- Adds index recommendations to Data Models
- Adds curl examples to API endpoints
- Writes final design.md

## Anti-Patterns to Avoid

### 1. Skipping Iterations

❌ **BAD:** "Iteration 1 looks good, let's stop"
✅ **GOOD:** Complete all 3 iterations for thorough review

### 2. Not Addressing All Issues

❌ **BAD:** "I'll fix the critical ones, skip the majors"
✅ **GOOD:** Address ALL critical and major issues each iteration

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

## Checklist: After 3 Iterations

- [ ] 0 critical issues remaining
- [ ] ≤2 major issues remaining (documented in Open Questions)
- [ ] 100% requirement coverage confirmed
- [ ] All sections complete with substance
- [ ] Change log shows progression
- [ ] All review reports saved
