---
name: sdd-memory
description: Memory module for SCL-enhanced SDD. Maintains persistent structured state across reasoning cycles, enabling evidential grounding and cross-artifact consistency.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "1.0.0"
---

# SDD Memory Module

The Memory module maintains persistent structured state across all SDD reasoning cycles. It implements the **Memorial Persistence** condition from the Structured Cognitive Loop (SCL) framework.

## RFC2119 Requirements

### Core Requirements

1. The system **MUST** maintain a `.memory/` directory within each change specification.
2. The system **MUST** persist all decisions, requirements, and citations in structured JSON format.
3. The system **MUST** load memory context before any artifact creation or task execution.
4. The system **MUST** update memory after each cognitive cycle completes.
5. The system **MUST NOT** allow memory state to be lost between cycles.

### Memory Structure

```
.specs/changes/<change-name>/.memory/
├── decisions.json        # All decisions made with evidence
├── requirements.json     # Extracted requirements index
├── citations.json        # Cross-reference graph
├── control-log.json      # Validation checkpoints
└── episodes.json         # Cycle-by-cycle history
```

## Memory Files Specification

### decisions.json

The system **MUST** store all design and implementation decisions with the following schema:

```json
{
  "version": "1.0",
  "change_name": "<string>",
  "decisions": [
    {
      "id": "DEC-NNN",
      "title": "<string>",
      "source": "<file>#<location>",
      "context": "<string describing the situation>",
      "options_considered": [
        {
          "name": "<option name>",
          "pros": ["<benefit>"],
          "cons": ["<drawback>"]
        }
      ],
      "chosen": "<chosen option>",
      "rationale": "<why this option was selected>",
      "evidence": ["REQ-NNN", "REQ-MMM"],
      "status": "active | superseded | deprecated",
      "superseded_by": "DEC-NNN | null",
      "created_at": "<ISO 8601 timestamp>",
      "created_by": "<artifact or process that created this>"
    }
  ]
}
```

### requirements.json

The system **MUST** index all requirements with the following schema:

```json
{
  "version": "1.0",
  "change_name": "<string>",
  "requirements": {
    "<REQ-ID>": {
      "id": "<REQ-ID>",
      "title": "<short title>",
      "description": "<full requirement text>",
      "source": "<file>#<location>",
      "type": "functional | non-functional | constraint",
      "status": "pending | in_progress | implemented | verified",
      "tasks": ["<task-id>"],
      "scenarios": ["<scenario-id>"],
      "evidence": ["<citation>"],
      "created_at": "<ISO 8601 timestamp>",
      "verified_at": "<ISO 8601 timestamp> | null"
    }
  }
}
```

### citations.json

The system **MUST** maintain a citation graph with the following schema:

```json
{
  "version": "1.0",
  "change_name": "<string>",
  "citations": [
    {
      "id": "CIT-NNN",
      "from": "<source file>#<location>",
      "to": "<target file>#<location>",
      "relationship": "implements | satisfies | depends_on | references | contradicts",
      "verified": true | false,
      "verified_at": "<ISO 8601 timestamp> | null",
      "verifier": "control-module | manual"
    }
  ]
}
```

### control-log.json

The system **MUST** record all control module validations:

```json
{
  "version": "1.0",
  "change_name": "<string>",
  "checkpoints": [
    {
      "id": "CHK-NNN",
      "timestamp": "<ISO 8601 timestamp>",
      "phase": "artefact-creation | task-execution | verification",
      "checks": [
        {
          "name": "<check name>",
          "status": "pass | fail | warn",
          "message": "<description>",
          "details": {}
        }
      ],
      "overall": "pass | fail | warn",
      "blocked": true | false
    }
  ]
}
```

### episodes.json

The system **SHOULD** maintain a cycle-by-cycle history for debugging:

```json
{
  "version": "1.0",
  "change_name": "<string>",
  "episodes": [
    {
      "cycle": 1,
      "phase": "specs-creation",
      "timestamp": "<ISO 8601 timestamp>",
      "observations": {
        "files_read": ["proposal.md"],
        "files_created": ["specs/auth/spec.md"],
        "requirements_extracted": ["AUTH-001", "AUTH-002"]
      },
      "judgments": [
        {
          "proposition": "<what was concluded>",
          "evidence": ["<citations>"],
          "confidence": "high | medium | low"
        }
      ],
      "termination": {
        "ready": false,
        "reason": "More artifacts to create"
      }
    }
  ]
}
```

## Memory Operations

### MEM.read()

The system **MUST** provide a read operation with the following behavior:

```
MEM.read(query) -> MemoryState

Parameters:
  - query: optional filter criteria
    - decision_ids: ["DEC-001"]
    - requirement_ids: ["AUTH-001"]
    - phase: "setup" | "implementation" | "verification"
    - since: <timestamp>

Returns:
  - decisions: matching decisions
  - requirements: matching requirements
  - citations: relevant citation edges
  - control_status: latest checkpoint status
```

### MEM.write()

The system **MUST** provide a write operation with validation:

```
MEM.write(entry) -> WriteResult

Parameters:
  - entry:
    - type: "decision" | "requirement" | "citation" | "checkpoint"
    - data: the entry content

Returns:
  - success: true | false
  - id: the assigned ID
  - errors: validation errors if any

Validation:
  - Decisions MUST have at least two options_considered
  - Requirements MUST have at least one scenario
  - Citations MUST resolve to existing targets
  - Checkpoints MUST include at least one check
```

### MEM.update()

The system **MUST** provide an update operation:

```
MEM.update(key, value) -> UpdateResult

Parameters:
  - key: "<type>/<id>/<field>"
    - e.g., "requirements/AUTH-001/status"
  - value: the new value

Returns:
  - success: true | false
  - previous_value: the old value
  - timestamp: when the update occurred

Side Effects:
  - Updates to "status" fields MUST update timestamps
  - Updates that contradict existing citations MUST log warnings
```

### MEM.verify_citation()

The system **MUST** verify citation targets exist:

```
MEM.verify_citation(citation) -> VerificationResult

Parameters:
  - citation: "<file>#<location>"

Returns:
  - valid: true | false
  - resolved_path: absolute path if valid
  - error: description if invalid

The system MUST support the following location formats:
  - Line number: "design.md#L45"
  - Section: "design.md#decision-password-hashing"
  - Range: "design.md#L45-52"
```

## Integration Points

### Before Artifact Creation

The system **MUST** load memory context:

1. Read `.memory/decisions.json` for prior decisions
2. Read `.memory/requirements.json` for existing requirements
3. Read `.memory/control-log.json` for any blocking issues
4. Inject context into cognition phase

### After Artifact Creation

The system **MUST** update memory:

1. Extract new decisions from artifact
2. Extract new requirements from specs
3. Record new citations
4. Log control checkpoint

### Before Task Execution

The system **MUST** provide context to subagents:

1. Read memory for tasks in this group
2. Read prior group outcomes
3. Build constraint set from decisions
4. Inject into subagent prompt

### After Task Execution

The system **MUST** record outcomes:

1. Record files created/modified
2. Update requirement status
3. Verify citations
4. Log control checkpoint

## Quality Requirements

### Persistence Guarantees

1. Memory state **MUST** survive process termination
2. Memory files **MUST** be valid JSON at all times
3. Memory updates **MUST** be atomic (no partial writes)
4. Memory **MUST** be human-readable for debugging

### Consistency Requirements

1. All citations **MUST** eventually resolve (verified=true)
2. Requirement status **MUST** match task completion
3. Decision supersession **MUST** be explicitly recorded
4. Control checkpoints **MUST** be chronologically ordered

### Performance Requirements

1. MEM.read() **SHOULD** complete in < 100ms for typical change
2. MEM.write() **SHOULD** complete in < 50ms
3. Memory files **SHOULD NOT** exceed 1MB per change

## Example Usage

### Initializing Memory for New Change

```
MEM.init("user-authentication")

Creates:
  .memory/decisions.json      {"version": "1.0", "decisions": []}
  .memory/requirements.json   {"version": "1.0", "requirements": {}}
  .memory/citations.json      {"version": "1.0", "citations": []}
  .memory/control-log.json    {"version": "1.0", "checkpoints": []}
  .memory/episodes.json       {"version": "1.0", "episodes": []}
```

### Recording a Decision

```
MEM.write({
  type: "decision",
  data: {
    title: "Session Storage Strategy",
    source: "design.md#L78",
    context: "Need to store user sessions across requests",
    options_considered: [
      {"name": "In-memory", "pros": ["Fast"], "cons": ["Lost on restart"]},
      {"name": "Redis", "pros": ["Persistent", "Scalable"], "cons": ["Additional infrastructure"]},
      {"name": "JWT", "pros": ["Stateless", "No infrastructure"], "cons": ["Cannot revoke"]}
    ],
    chosen: "JWT",
    rationale: "Stateless architecture fits microservices pattern",
    evidence: ["REQ-SESS-001"]
  }
})

Returns: {success: true, id: "DEC-001"}
```

### Querying Context for Task Execution

```
MEM.read({
  decision_ids: ["DEC-001", "DEC-002"],
  requirement_ids: ["AUTH-001", "AUTH-002"]
})

Returns: {
  decisions: [{id: "DEC-001", ...}],
  requirements: {AUTH-001: {...}, AUTH-002: {...}},
  citations: [{from: "tasks.md#L45", to: "DEC-001"}],
  control_status: {overall: "pass", last_checkpoint: "CHK-003"}
}
```

### Harvesting from Proposal

```
MEM.harvest_from_proposal("proposal.md")

Extracts:
  Goals → requirements.json (type: functional)
  Constraints → requirements.json (type: constraint)
  Context Log → episodes.json (phase: exploration)
  Exploration Notes → episodes.json (judgments)

Preserves:
  decisions.json (design phase owns this)

Returns: {
  requirements_added: 5,
  requirements_updated: 0,
  episodes_added: 3,
  decisions_preserved: true
}
```

**Extraction Rules:**

| Source Section | Target | Type | Pattern |
|---------------|--------|------|---------|
| Goals | requirements.json | functional | "- Goal N: ..." |
| Constraints | requirements.json | constraint | "- <constraint>: <reason>" |
| Context Log | episodes.json | exploration | "#### QN: ... **A:** ..." |
| Exploration Notes | episodes.json | options | "- Option: ..." |

**Semantic Extraction:**

If sections aren't perfectly structured, use keyword patterns:

- Lines containing "MUST/MUST NOT/SHALL" → constraint requirement
- Lines starting with "Goal/Objective/Success" → functional requirement
- Q&A pairs ("Q:"/"A:" or "Question"/"Answer") → exploration episodes
- Lines with "Considered/Option/Tried" → options_not_decided judgments

**Re-harvesting:**

If memory already exists when harvesting runs:

1. Match requirements by source location (proposal.md#L<N>)
2. Update existing if source matches
3. Add new if not found
4. Remove stale (was in proposal, now gone)
5. Always preserve decisions.json (design phase owns these)
```
