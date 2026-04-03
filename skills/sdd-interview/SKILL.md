---
name: sdd-interview
description: Exploration interview skill for SDD workflow. Asks clarifying questions, populates context-log.md, and transitions to /sdd-propose. Do not use automatically, only when invoked by /sdd-explore.
---

# Exploration Interview for SDD Workflow

## Goal

Ask the minimum set of clarifying questions needed to populate the context-log.md for a change exploration. After capturing goals, constraints, scope, and options, save the context-log and direct the user to run /sdd-propose to create the formal proposal.

## Workflow

### 1) Decide whether the request is underspecified

Treat a request as underspecified if after exploring how to perform the work, some or all of the following are not clear:

- Define the objective (what should change vs stay the same)
- Define "done" (acceptance criteria, examples, edge cases)
- Define scope (which files/components/users are in/out)
- Define constraints (compatibility, performance, style, deps, time)
- Identify environment (language/runtime versions, OS, build/test runner)
- Clarify safety/reversibility (data migration, rollout/rollback, risk)

If multiple plausible interpretations exist, assume it is underspecified.

### 2) Ask questions in batches

Use the `question` tool to ask structured questions in 3 batches:

1. **Offer fast-path first**: "Use all recommended defaults?"
   - If yes: Skip to Batch 3 only
   - If no: Ask all 3 batches

2. **Batch 1** (Problem Context): Problem type + Audience
3. **Batch 2** (Scope & Timeline): Scope areas (multi-select) + Timeline
4. **Batch 3** (Success & Risk): Breaking change risk + Open-ended success criteria

Make questions easy to answer:
- Use multiple-choice with descriptions
- Mark recommended defaults with "(Recommended)"
- Allow multiple selections for scope questions
- Keep headers short (≤30 chars)
- Include one open-ended question (success criteria)

### 3) Pause before saving

Until answers arrive:
- Wait for user responses before proceeding
- Do perform low-risk discovery if needed (inspect repo structure)
- If user asks to proceed without answers: State assumptions and confirm

### 4) Complete exploration and save context-log

Once you have answers:

1. **UPDATE context-log.md** with all sections populated:
   - Goals Identified
   - Constraints Discovered (technical/business/external)
   - Scope Boundaries (In Scope / Out of Scope)
   - Options Considered
   - Risks Identified
   - Domain Knowledge

2. **SHOW completion summary**:

```
✓ Exploration complete for: <change-name>

Captured:
  - X goals identified
  - Y constraints discovered  
  - Z scope boundaries defined
  - N options considered

Context log saved: .specs/changes/<change-name>/context-log.md

Next: Run /sdd-propose <change-name> to create the formal proposal
```

3. **STOP** - Do not ask about implementation or proceed with work

**CRITICAL:** This skill is for exploration ONLY. It populates context-log.md and prepares for /sdd-propose. It never asks about implementation.

## Using the Question Tool

For optimal user experience, use the `question` tool to ask structured questions in batches.

### Question Batching Strategy

Divide questions into 3 batches to reduce cognitive load:

**Batch 1: Problem Context** (2 questions)
- Problem type (new feature/bug fix/enhancement/refactor)
- Primary audience (who will use this)

**Batch 2: Scope & Timeline** (2 questions)
- Scope areas affected (API/Database/UI/etc. - multiple select)
- Timeline expectations

**Batch 2.5: Constraints & Boundaries** (3 questions)
- Compliance / regulatory requirements
- Integration dependencies
- Behavioral constraints (backward compatibility, existing behavior to preserve)

**Batch 3: Success & Risk** (1 question + open-ended)
- Breaking change risk
- Success criteria (open-ended)

### Fast-Path Option

Include a "Use all defaults" option at the start:
- Ask: "Quick start: Use all recommended defaults?" (yes/no)
- If yes: Skip to Batch 3 (success criteria only)
- If no: Ask all 3 batches

### Question Specifications

#### Batch 1: Problem Context

```json
[
  {
    "header": "Problem Type",
    "question": "What type of change is this?",
    "options": [
      {"label": "New feature (Recommended)", "description": "Adding new functionality"},
      {"label": "Bug fix", "description": "Fixing broken behavior"},
      {"label": "Enhancement", "description": "Improving existing feature"},
      {"label": "Refactor", "description": "Code cleanup without behavior change"}
    ]
  },
  {
    "header": "Audience",
    "question": "Who will primarily use this?",
    "options": [
      {"label": "End users (Recommended)", "description": "Direct user-facing feature"},
      {"label": "Developers", "description": "Internal or API consumers"},
      {"label": "Internal team", "description": "Team productivity tool"},
      {"label": "External API", "description": "Third-party integrations"}
    ]
  }
]
```

#### Batch 2: Scope & Timeline

```json
[
  {
    "header": "Scope Areas",
    "question": "Which areas will be affected? (Select all that apply)",
    "multiple": true,
    "options": [
      {"label": "API endpoints", "description": "REST/GraphQL routes"},
      {"label": "Database", "description": "Schema migrations or queries"},
      {"label": "UI components", "description": "Frontend changes"},
      {"label": "Configuration", "description": "Settings or environment variables"},
      {"label": "Tests", "description": "Test coverage or fixtures"},
      {"label": "Documentation", "description": "README or API docs"}
    ]
  },
  {
    "header": "Timeline",
    "question": "What's the expected timeline?",
    "options": [
      {"label": "No deadline (Recommended)", "description": "Flexible timeline, quality focus"},
      {"label": "This month", "description": "Medium priority, weeks to complete"},
      {"label": "This week", "description": "High priority, days to complete"},
      {"label": "Urgent (days)", "description": "Critical, needs immediate attention"}
    ]
  }
]
```

#### Batch 3: Success & Risk

```json
[
  {
    "header": "Breaking Risk",
    "question": "Could this break existing functionality?",
    "options": [
      {"label": "No (Recommended)", "description": "Backward compatible change"},
      {"label": "Unsure", "description": "Need to investigate further"},
      {"label": "Yes", "description": "Breaking change, needs migration plan"}
    ]
  }
]
```

After this batch, ask the open-ended question:
```
**Success Criteria:** What does success look like? How will we know it's done?
(Free-form text response)
```

#### Batch 2.5: Constraints & Boundaries

```json
[
  {
    "header": "Compliance",
    "question": "Are there compliance or regulatory requirements?",
    "multiple": true,
    "options": [
      {"label": "None (Recommended)", "description": "No special compliance needs"},
      {"label": "GDPR / Data privacy", "description": "EU data protection, consent, right to erasure"},
      {"label": "Accessibility (WCAG)", "description": "Web Content Accessibility Guidelines"},
      {"label": "Security / Audit", "description": "SOC2, HIPAA, PCI-DSS, or similar"},
      {"label": "Industry-specific", "description": "Domain regulations (financial, medical, etc.)"}
    ]
  },
  {
    "header": "Integrations",
    "question": "Does this change depend on or affect external systems?",
    "multiple": true,
    "options": [
      {"label": "None (Recommended)", "description": "Self-contained change"},
      {"label": "Existing APIs", "description": "Must integrate with internal or external APIs"},
      {"label": "Database / storage", "description": "Schema changes, data migration, new tables"},
      {"label": "Third-party services", "description": "Payment, email, analytics, etc."},
      {"label": "Authentication / SSO", "description": "Login, roles, permissions integration"}
    ]
  },
  {
    "header": "Behavior Boundaries",
    "question": "Are there behaviors that must be preserved unchanged?",
    "options": [
      {"label": "No restrictions (Recommended)", "description": "Free to change any behavior in scope"},
      {"label": "Backward compatible", "description": "Existing API contracts must not change"},
      {"label": "Data compatibility", "description": "Existing data must remain valid and accessible"},
      {"label": "Specific behaviors", "description": "Certain features must work exactly as before"}
    ]
  }
]
```

After this batch, ask the follow-up:
```
**Constraint Details:** Any specific details about the constraints selected above?
For example: which APIs, what data formats, which behaviors must be preserved?
(Free-form text response)
```

### Extracting Context-Log Data

After receiving answers, extract and populate context-log.md:

1. **From Batch 1:**
   - Problem type → Goals Identified
   - Audience → Domain Knowledge

2. **From Batch 2:**
   - Scope areas → Scope Boundaries (In Scope)
   - Timeline → Business Constraints

3. **From Batch 2.5:**
   - Compliance selections → External Constraints (regulatory)
   - Integration dependencies → Technical Constraints (systems, APIs, protocols)
   - Behavior boundaries → Technical Constraints (backward compatibility, data compatibility)
   - Constraint details → Constraints Discovered (specifics under each category)

4. **From Batch 3:**
   - Breaking risk → Risks Identified
   - Success criteria → Goals Identified (refined)

4. **Inferred Data:**
   - Scope areas NOT selected → Scope Boundaries (Out of Scope)
   - Timeline urgency → Technical Constraints (if urgent)

## Anti-Patterns

- Don't ask questions you can answer with a quick, low-risk discovery read (e.g., configs, existing patterns, docs).
- Don't ask open-ended questions if a tight multiple-choice or yes/no would eliminate ambiguity faster.
- Don't ask about implementation details - that's for the design phase, not exploration.
- Don't offer to "proceed with implementation" - the only next step is /sdd-propose.
