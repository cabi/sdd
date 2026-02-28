# SCL Architecture

Understanding the Structured Cognitive Loop design and principles.

## The Problem

### Subagent Context Isolation

When using `/sdd-apply-all` for parallel execution, subagents operate in isolated contexts. They cannot:

- Access decisions made in prior groups
- Know what files were created previously
- Understand the reasoning behind design choices

This leads to:
- Redundant actions
- Inconsistent implementations
- Lost context between groups
- ~70% task success rate

### Example Failure Mode

```
Group 1: Creates UserService with interface IUserService
Group 2: Creates AuthService that needs UserService
         → But doesn't know IUserService exists
         → Creates its own incompatible UserService
         → Integration fails
```

---

## The Solution

SCL (Structured Cognitive Loop) provides external memory persistence:

1. **Memory Persistence** - External `.memory/` directory stores state across cycles
2. **Evidential Grounding** - Every claim MUST cite a source
3. **Normative Control** - Control module validates before execution
4. **Scope Enforcement** - Subagents constrained to allowed files

---

## Architecture

### Memory Module

```
.memory/
├── decisions.json          # All decisions with evidence
├── requirements.json       # Requirement index
├── citations.json          # Citation graph
├── control-log.json        # Validation checkpoints
└── episodes.json           # Cycle-by-cycle history
```

#### decisions.json

Records every design decision with alternatives:

```json
{
  "DEC-001": {
    "id": "DEC-001",
    "title": "Password Hashing",
    "context": "Need secure password storage",
    "options": ["bcrypt", "argon2", "scrypt"],
    "chosen": "bcrypt",
    "rationale": "Battle-tested, built-in salt",
    "source": "design.md#L45"
  }
}
```

#### requirements.json

Tracks requirements with status:

```json
{
  "AUTH-001": {
    "id": "AUTH-001",
    "description": "Users SHALL be able to log in",
    "source": "specs/auth/spec.md#L23",
    "status": "pending"
  }
}
```

#### citations.json

Links code to requirements:

```json
{
  "CIT-001": {
    "file": "src/auth/login.ts",
    "line": 15,
    "requirement": "AUTH-001",
    "verified": true
  }
}
```

#### control-log.json

Records validation checkpoints:

```json
{
  "CHK-001": {
    "timestamp": "2024-01-15T10:30:00Z",
    "phase": "pre-dispatch",
    "check": "scope_verify",
    "result": "pass",
    "details": "All files in allowed list"
  }
}
```

#### episodes.json

Cycle-by-cycle history:

```json
{
  "EP-001": {
    "cycle": 2,
    "group": 2,
    "input": ["DEC-001", "AUTH-001"],
    "output": ["src/auth/login.ts"],
    "memory_writes": ["AUTH-001.status = implemented"]
  }
}
```

---

### Control Module

Provides validation functions:

```
precondition_check()  → Verify conditions before action
scope_verify()        → Check file boundaries
citation_validate()   → Verify citation integrity
```

---

### SCL 5-Phase Loop

Every SCL-enhanced operation follows this loop:

```
┌──────────────┐
│   RETRIEVE   │  Load memory context
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  COGNITION   │  Generate content with context
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   CONTROL    │  Validate citations, check regulation
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   ACTION     │  Execute (write files, dispatch subagent)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ MEMORY WRITE │  Update decisions, requirements, citations
└──────────────┘
```

---

## RFC2119 Compliance

SCL uses RFC2119 keywords for normative requirements:

| Keyword | Meaning |
|---------|---------|
| **MUST** / **SHALL** | Absolute requirement |
| **MUST NOT** / **SHALL NOT** | Absolute prohibition |
| **SHOULD** / **RECOMMENDED** | Recommended but exceptions exist |
| **MAY** / **OPTIONAL** | Truly optional |

### Example in Regulation.md

```markdown
## Evidential Rules
1. Every requirement **MUST** cite its source
2. Every decision **MUST** document alternatives considered
3. Every task **MAY** include optional validation criteria
```

---

## Subagent Context Injection

When dispatching subagents, SCL injects full context:

### Before Dispatch

```
MEM.read() → Load prior decisions, requirements, outcomes
CONTROL.evaluate() → Verify preconditions
```

### Injected Context

```
You are executing Group N: <Group Name>

## Memory Context (from prior work)

### Decisions You MUST Follow
1. DEC-001: Use bcrypt (per design.md#L45)
2. DEC-002: JWT + refresh tokens (per design.md#L78)

### Requirements You MUST Satisfy
1. AUTH-001: Users SHALL log in (per specs/auth/spec.md#L23)

### Prior Work Outcomes
- Group 1 created: src/auth/User.ts
- Group 1 decided: Use interface over class

## Constraints (YOU MUST NOT VIOLATE)

### Allowed Files
src/auth/**/*.ts

### Blocked Files
src/core/*

### Required Citations
Every file MUST include:
// Implements: REQ-ID (per specs/.../spec.md#L<N>)
```

### After Completion

```
CONTROL.verify_scope() → Check file boundaries
MEM.write() → Record outcomes, update status
```

---

## Regulation.md (Epistemic Constitution)

Every SCL-enhanced change includes a `regulation.md` defining rules:

```markdown
# Epistemic Constitution: <change-name>

## 1. Evidential Rules
1. Every requirement **MUST** cite its source
2. Every decision **MUST** document alternatives considered
3. Every task **MUST** reference at least one requirement

## 2. Scope Rules
1. Tasks in Group N **MAY ONLY** modify files from Groups 1..N
2. Files outside allowed paths **MUST NOT** be modified

## 3. Validation Rules
1. Tasks **MUST** be verified before marked complete
2. Files **MUST** have header comments citing requirements

## 4. Memory Rules
1. After each group, memory **MUST** be updated
2. Citations **MUST** use format: `filename#location`
```

---

## Expected Benefits

| Metric | Standard | SCL-Enhanced |
|--------|----------|--------------|
| Task Success Rate | ~70% | ~86% |
| Redundant Actions | High | ~50% reduction |
| Memory Fidelity | Low | High (persistent) |
| Hallucination Rate | Moderate | ~3x reduction |
| Error Localization | Poor | Good (cycle-level logs) |

---

## When to Use SCL

| Aspect | Use SCL | Standard OK |
|--------|---------|-------------|
| Subagent execution | ✓ Yes | - |
| Multi-group tasks | ✓ Yes | - |
| Need traceability | ✓ Yes | - |
| High reliability | ✓ Yes | - |
| Simple single-task | - | ✓ OK |
| Quick prototype | - | ✓ OK |
