# Epistemic Constitution: {{CHANGE_NAME}}

> This document defines the normative rules governing this change specification.
> All artifacts and tasks MUST comply with these rules.
> Version: 1.0
> Created: {{TIMESTAMP}}

---

## 1. Evidential Rules

These rules govern how claims MUST be grounded in evidence.

### 1.1 Requirement Traceability

1. Every requirement **MUST** cite its source:
   - User request (with reference to original request)
   - Business requirement (with document reference)
   - Technical constraint (with rationale)
   - Derived requirement (with parent requirement reference)

2. Requirements without clear sources **MUST NOT** be included.

3. Requirement format **MUST** follow EARS notation:
   ```
   WHEN <event> THEN system SHALL <response>
   IF <condition> THEN system SHALL <response>
   ```

### 1.2 Decision Documentation

1. Every design decision **MUST** document:
   - The context requiring a decision
   - At least two alternatives considered
   - Pros and cons of each alternative
   - The chosen option
   - The rationale for the choice

2. Decisions without alternatives **MUST NOT** be accepted.

3. Decision rationale **MUST** cite relevant requirements.

### 1.3 Task Evidence

1. Every task **MUST** reference:
   - At least one requirement (with source citation)
   - At least one piece of evidence (design decision, spec section)

2. Tasks without requirement references **MUST NOT** be created.

3. Tasks without evidence citations **MUST NOT** be created.

### 1.4 Citation Format

Citations **MUST** use one of the following formats:
- Line reference: `filename#L45`
- Line range: `filename#L45-52`
- Section reference: `filename#section-name`
- Requirement reference: `REQ-ID (per specs/capability/spec.md#L23)`

All citations **MUST** resolve to existing content.

---

## 2. Scope Rules

These rules govern what files and operations are permitted.

### 2.1 Group Scope

1. Tasks in Group N **MAY ONLY** modify files:
   - Created in Groups 1 through N
   - Explicitly listed in the task's allowed files

2. Tasks in Group N **MUST NOT** modify files from:
   - Future groups (N+1, N+2, ...)
   - Groups not in their dependency chain
   - Blocked paths

3. Group scope **MUST** be defined in group metadata:
   ```markdown
   _Meta: sequential, depends on: 1, 2_
   ```

### 2.2 File Modifications

1. Every task **MUST** specify file operations:
   - `_Creates: path` for new files
   - `_Modifies: path` for existing files

2. Tasks **MUST NOT** modify files not listed in their metadata.

3. File paths **MUST** be relative to project root.

### 2.3 Protected Files

The following file patterns **MUST NOT** be modified by tasks:
- `.specs/specs/**` (accumulated specs)
- `.specs/archive/**` (completed changes)
- `.memory/**` (managed by memory module)
- `regulation.md` (this file)

### 2.4 Scope Override

Scope rules **MAY** be overridden only when:
1. Explicit user approval is documented
2. The override is recorded in control-log.json
3. The reason is documented in the task

---

## 3. Validation Rules

These rules govern how completion is verified.

### 3.1 Task Completion

1. A task **MUST NOT** be marked complete until:
   - All specified files exist
   - All specified tests pass
   - All validation criteria are satisfied
   - Memory has been updated

2. Checkbox marking **MUST** follow:
   - `[ ]` - Not started
   - `[x]` - Complete and verified
   - `[~]` - Skipped (with documented reason)
   - `[!]` - Blocked (with blocking issue noted)

### 3.2 File Headers

Files created by tasks **MUST** include header comments:
```typescript
/**
 * Implements: REQ-ID, REQ-ID2
 * Design: design.md#section-name
 * Created: YYYY-MM-DD as part of <change-name>
 */
```

### 3.3 Subagent Signals

1. Subagents **MUST** output completion signal:
   ```
   GROUP N COMPLETE
   ```

2. Subagents **MUST NOT** continue to next group without:
   - Completion signal
   - Control verification

3. Missing completion signal **MUST** trigger investigation.

### 3.4 Test Requirements

1. All new functionality **MUST** have tests.

2. Test coverage **SHOULD** be at least 80% for new code.

3. Tests **MUST** be listed in validation criteria.

---

## 4. Memory Rules

These rules govern memory state management.

### 4.1 Memory Updates

1. Memory **MUST** be updated after:
   - Artifact creation (extract decisions, requirements)
   - Task completion (update status, record citations)
   - Group completion (log checkpoint)

2. Memory updates **MUST** be specified in task metadata:
   ```markdown
   _Memory Write: requirements.json#AUTH-001.status ← "implemented"_
   ```

### 4.2 Citation Recording

1. All citations **MUST** be recorded in `citations.json`.

2. Citation relationships **MUST** be one of:
   - `implements` - Code implements a decision
   - `satisfies` - Code satisfies a requirement
   - `depends_on` - Artifact depends on another
   - `references` - General reference
   - `contradicts` - Contradiction (requires resolution)

### 4.3 Contradiction Resolution

1. Contradictions between artifacts **MUST** be resolved.

2. Resolution **MUST** be recorded:
   ```json
   {
     "contradiction": "DEC-001 vs DEC-005",
     "resolution": "DEC-005 supersedes DEC-001",
     "reason": "New information invalidated DEC-001"
   }
   ```

3. Superseded decisions **MUST** be marked with status: "superseded".

### 4.4 Memory Integrity

1. Memory files **MUST** be valid JSON at all times.

2. Memory updates **MUST** be atomic.

3. Memory state **MUST** be recoverable from control-log.json.

---

## 5. Control Rules

These rules govern the control module behavior.

### 5.1 Precondition Enforcement

1. Actions **MUST NOT** execute without precondition verification.

2. Failed preconditions **MUST** block execution.

3. Blocked actions **MUST** report missing preconditions.

### 5.2 Deduplication

1. Duplicate actions **MUST** be rejected.

2. Cached results **SHOULD** be returned for duplicates.

3. State changes **MUST** invalidate relevant caches.

### 5.3 Termination

1. The change **MAY** terminate when:
   - All required tasks complete
   - All requirements verified
   - Goal fidelity >= 0.8

2. Early termination **MUST** be explicitly approved.

3. Termination **MUST** be logged in control-log.json.

---

## 6. Exception Handling

### 6.1 Override Authority

The following roles **MAY** override rules:
1. User (with explicit approval)
2. System administrator (for infrastructure rules)

### 6.2 Override Documentation

All overrides **MUST** be documented:
```markdown
### Override: RULE-ID
- **Overridden by:** <who>
- **Reason:** <why>
- **Date:** <when>
- **Impact:** <what changes>
```

### 6.3 Override Limits

The following rules **MUST NOT** be overridden:
- Evidential grounding requirements
- Scope rules for protected files
- Memory integrity requirements

---

## 7. Compliance Verification

### 7.1 Artifact Checklist

Before an artifact is accepted:
- [ ] All citations verified
- [ ] No regulation violations
- [ ] Memory updated
- [ ] Control checkpoint logged

### 7.2 Task Checklist

Before a task is marked complete:
- [ ] Files created/modified as specified
- [ ] Tests pass
- [ ] Evidence citations present
- [ ] Memory updated
- [ ] Header comments present

### 7.3 Change Checklist

Before a change is archived:
- [ ] All tasks complete or intentionally skipped
- [ ] All requirements verified
- [ ] No open contradictions
- [ ] Control log shows all checks passed
- [ ] Goal fidelity >= 0.8

---

## 8. Appendix: RFC2119 Keyword Reference

| Keyword | Meaning |
|---------|---------|
| **MUST** / **REQUIRED** / **SHALL** | Absolute requirement |
| **MUST NOT** / **SHALL NOT** | Absolute prohibition |
| **SHOULD** / **RECOMMENDED** | Recommended but exceptions may exist |
| **SHOULD NOT** / **NOT RECOMMENDED** | Not recommended but exceptions may exist |
| **MAY** / **OPTIONAL** | Truly optional |

---

## 9. Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | {{TIMESTAMP}} | {{AUTHOR}} | Initial constitution |
