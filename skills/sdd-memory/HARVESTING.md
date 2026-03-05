# Harvesting from Proposal

## Overview

The `/sdd-init-memory` command harvests structured knowledge from `proposal.md` into memory files. This preserves exploration knowledge and provides context for the design phase.

## Principles

1. **Goals, not decisions** - Harvest WHAT to achieve, not HOW to implement
2. **Constraints, not choices** - Capture limitations, not technical choices
3. **Context, not conclusions** - Preserve exploration reasoning, not final decisions
4. **Preserve decisions.json** - Design phase owns technical decisions

---

## Extraction Rules

### 1. Goals → requirements.json

**Pattern:**
```markdown
## Goals
- Goal 1: User authentication via email/password
- Goal 2: Session management with 24h expiry
- Goal 3: Secure password storage
```

**Extraction:**
```json
{
  "id": "REQ-FUNC-001",
  "type": "functional",
  "title": "User authentication via email/password",
  "description": "User authentication via email/password",
  "source": "proposal.md#L45",
  "status": "pending",
  "created_at": "2026-03-05T10:30:00Z"
}
```

**Agent Instruction:**
- Read lines under "## Goals" section
- For each goal bullet:
  - Extract full description
  - Create REQ-FUNC-NNN with sequential numbering
  - Source = "proposal.md#L{line_number}"
  - Status = "pending"

---

### 2. Constraints → requirements.json

**Pattern:**
```markdown
## Constraints

### Technical Constraints
- Must use PostgreSQL database (existing infrastructure)
- Must support 10,000 concurrent users

### Business Constraints
- No additional infrastructure costs
- Must complete by Q2 2026

### External Constraints
- Must comply with GDPR
- Must integrate with existing SSO
```

**Extraction:**
```json
{
  "id": "REQ-CONST-001",
  "type": "constraint",
  "title": "Must use PostgreSQL database",
  "description": "Must use PostgreSQL database (existing infrastructure)",
  "source": "proposal.md#L52",
  "status": "pending",
  "created_at": "2026-03-05T10:30:00Z"
}
```

**Agent Instruction:**
- Read "## Constraints" section
- Process each subsection (technical/business/external)
- For each constraint bullet:
  - Extract description and reason
  - Create REQ-CONST-NNN
  - Include category in metadata
  - Source = "proposal.md#L{line_number}"

---

### 3. Context Log → episodes.json

**Pattern:**
```markdown
## Context Log

### Initial Request
User needs to reset forgotten passwords without IT involvement.

### Clarifying Questions

#### Q1: What problem are you solving?
**A:** Users can't access their accounts without IT help, creating delays
_Insight: Self-service is critical for user experience_

#### Q2: What does success look like?
**A:** Users reset passwords autonomously within 5 minutes
_Insight: Automation and speed are expected_

#### Q3: What constraints exist?
**A:** Must use existing email system, no new infrastructure
_Insight: Infrastructure constraint impacts solution design_
```

**Extraction:**
```json
{
  "cycle": 1,
  "phase": "exploration",
  "timestamp": "2026-03-05T10:30:00Z",
  "observations": {
    "files_read": ["proposal.md"],
    "questions": [
      "What problem are you solving?"
    ],
    "answers": [
      "Users can't access their accounts without IT help, creating delays"
    ],
    "insights": [
      "Self-service is critical for user experience"
    ]
  },
  "judgments": [],
  "termination": {
    "ready": false,
    "reason": "Exploration phase complete, ready for design"
  }
}
```

**Agent Instruction:**
- Read "## Context Log" section
- Parse Q&A pairs (look for "#### QN:" or "Question N:" patterns)
- For each Q&A:
  - Extract question, answer, and insight (if present)
  - Create exploration episode
  - Group related Q&A into logical cycles
  - Include timestamp

---

### 4. Exploration Notes → episodes.json

**Pattern:**
```markdown
## Exploration Notes

### Options Considered
- JWT tokens: Stateless, scalable / Cannot revoke easily, larger payload
- Session tokens: Revocable, smaller payload / Requires server storage

### Risks Identified
- Brute force attacks on login endpoint
- Email delivery delays affecting password reset

### Domain Knowledge
- Existing email system has 99.5% delivery rate
- Current user base: 5,000 users, growing 20% annually
```

**Extraction:**
```json
{
  "cycle": 2,
  "phase": "exploration",
  "timestamp": "2026-03-05T10:30:00Z",
  "observations": {
    "files_read": ["proposal.md"]
  },
  "judgments": [
    {
      "proposition": "Option: JWT tokens",
      "evidence": "Stateless, scalable; Cannot revoke easily, larger payload",
      "confidence": "medium"
    },
    {
      "proposition": "Risk: Brute force attacks on login endpoint",
      "evidence": "Security concern requiring rate limiting or CAPTCHA",
      "confidence": "high"
    }
  ],
  "termination": {
    "ready": false,
    "reason": "Options explored but not decided"
  }
}
```

**Agent Instruction:**
- Read "## Exploration Notes" section
- For each option:
  - Create judgment with proposition = option description
  - Evidence = pros and cons combined
  - Confidence = "medium" (not decided yet)
- For each risk:
  - Create judgment with proposition = risk description
  - Evidence = impact/mitigation if provided
  - Confidence = "high" (risk is real, mitigation is optional)
- For each domain knowledge item:
  - Add to observations as context

---

## Semantic Extraction

If proposal sections are not perfectly structured, use semantic pattern matching:

| Pattern | Type | Example |
|---------|------|---------|
| "MUST/MUST NOT/SHALL" | constraint | "System MUST use HTTPS" |
| "Goal/Objective/Success" | functional | "Goal: Enable user login" |
| "Q:/A:" or "Question/Answer" | exploration episode | Q&A pairs |
| "Considered/Option/Tried" | options_not_decided | "Considered using Redis" |
| "Because/Due to/Reason" | rationale | Extract as evidence |
| "Risk/Concern" | risk | "Risk: Data loss" |

**Fallback Algorithm:**

```
FOR each line in proposal.md:
  IF line matches "MUST|MUST NOT|SHALL":
    CREATE constraint requirement
  
  ELIF line matches "Goal:|Objective:|Success:|Outcome:":
    CREATE functional requirement
  
  ELIF line matches "Q\d+:|Question:|#### Q\d+":
    START Q&A episode
  
  ELIF line matches "\*\*A:\*\*|Answer:":
    ADD to current Q&A episode
  
  ELIF line matches "Option:|Considered:|Tried:":
    CREATE options_not_decided judgment
  
  ELIF line matches "Risk:|Concern:":
    CREATE risk judgment
```

---

## Re-harvesting

When `/sdd-init-memory` is run again after proposal changes:

### Strategy: Match, Update, Add, Remove

1. **Match by source location**
   - Read existing requirements
   - Match entries by source field (proposal.md#L<N>)
   - If source matches: UPDATE entry

2. **Add new entries**
   - Parse proposal for new goals/constraints
   - If not found in existing memory: ADD

3. **Remove stale entries**
   - Check existing requirements
   - If source location no longer exists in proposal: REMOVE
   - Log removal in control-log

4. **Preserve decisions**
   - NEVER modify decisions.json
   - Design phase owns technical decisions
   - Only harvest from proposal (goals, constraints, context)

### Example

**Before (proposal.md):**
```markdown
## Goals
- Goal 1: User authentication
- Goal 2: Session management
```

**After (proposal.md edited):**
```markdown
## Goals
- Goal 1: User authentication via email/password
- Goal 2: Session management with 24h expiry
- Goal 3: Two-factor authentication (NEW)
```

**Re-harvest result:**
```json
{
  "requirements_updated": 2,  // Goals 1 and 2 updated
  "requirements_added": 1,     // Goal 3 is new
  "requirements_removed": 0,
  "decisions_preserved": true
}
```

---

## Control Log Entry

After harvesting, log in control-log.json:

```json
{
  "id": "CHK-001",
  "timestamp": "2026-03-05T10:30:00Z",
  "phase": "harvest",
  "checks": [
    {
      "name": "goals_extracted",
      "status": "pass",
      "message": "Extracted 3 functional requirements from Goals section"
    },
    {
      "name": "constraints_extracted",
      "status": "pass",
      "message": "Extracted 2 constraint requirements from Constraints section"
    },
    {
      "name": "context_extracted",
      "status": "pass",
      "message": "Extracted 4 exploration episodes from Context Log"
    },
    {
      "name": "decisions_preserved",
      "status": "pass",
      "message": "Preserved 0 decisions (design phase not started)"
    }
  ],
  "overall": "pass",
  "blocked": false
}
```

---

## Quality Checks

After harvesting, verify:

1. **All goals harvested** - Every goal in proposal has corresponding requirement
2. **All constraints harvested** - Every constraint has corresponding requirement
3. **Context preserved** - Q&A pairs captured in episodes
4. **No decisions harvested** - decisions.json remains empty (or preserved if re-harvest)
5. **Valid sources** - All source citations resolve to actual proposal lines

---

## Integration Points

### Before Design Phase

Design agent loads memory context:

```
MEM.read({
  requirement_ids: ["REQ-FUNC-*", "REQ-CONST-*"],
  phase: "exploration"
})

Returns:
  - Goals to achieve (functional requirements)
  - Constraints to respect (constraint requirements)
  - Exploration context (episodes with Q&A, options, risks)
  - No decisions (agent will create these)
```

### During Design Phase

Design agent creates decisions:

```
MEM.write({
  type: "decision",
  data: {
    title: "Session Storage Strategy",
    chosen: "JWT",
    rationale: "Stateless architecture fits constraints",
    evidence: ["REQ-CONST-001"]  // References harvested constraint
  }
})
```

### After Design Phase

Verification checks:

- All requirements have corresponding decisions (or explicit justification)
- All decisions respect constraints
- All goals have implementation path

---

## Common Issues

### Issue: Missing Context Log

**Symptom:** proposal.md lacks Context Log section

**Solution:**
- Use semantic extraction from free-form text
- Look for Q&A patterns, goals, constraints in "Why" or "Description" sections
- Create minimal exploration episode with initial request

### Issue: Ambiguous Goals vs Constraints

**Symptom:** Hard to distinguish goal from constraint

**Rule:**
- Goal = What we want to achieve (outcome)
- Constraint = What limits how we achieve it (boundary)

**Example:**
- "Users can authenticate" → Goal (functional)
- "Must complete in <2 seconds" → Constraint (performance)

### Issue: Duplicate Requirements

**Symptom:** Same requirement extracted multiple times

**Solution:**
- Check for duplicate titles/descriptions
- Merge duplicates, keep earliest source
- Log merge in control-log

---

## Best Practices

### DO

- Harvest after every proposal change
- Preserve decisions.json at all costs
- Use semantic extraction as fallback
- Log all harvest operations
- Verify source citations resolve

### DON'T

- Harvest technical decisions from proposal
- Modify requirements created by design phase
- Skip quality checks
- Assume sections are perfectly structured
- Remove entries without logging
