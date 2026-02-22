---
name: sdd-control
description: Control module for SCL-enhanced SDD. Implements normative validation, precondition checking, and scope enforcement across all cognitive cycles.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "1.0.0"
---

# SDD Control Module

The Control module implements **Normative Control** from the Structured Cognitive Loop (SCL) framework. It enforces epistemic norms, validates preconditions, and gates action execution.

## RFC2119 Requirements

### Core Requirements

1. The system **MUST** implement the loop invariant: "no action without preconditions"
2. The Control module **MUST** validate all proposals before execution
3. The Control module **MUST** enforce scope boundaries for subagents
4. The Control module **MUST** log all validation decisions
5. The Control module **MUST NOT** approve actions that violate regulation rules

### Control Policies

The Control module **MUST** implement the following policies:

| Policy | Description | Enforcement |
|--------|-------------|-------------|
| Precondition Check | Verify required information exists in memory | MUST block if missing |
| Deduplication | Prevent redundant actions | MUST reject duplicates |
| Conditional Evaluation | Evaluate branch conditions explicitly | MUST verify before action |
| Termination Detection | Track goal satisfaction | MUST signal when complete |
| Scope Enforcement | Restrict file modifications | MUST block out-of-scope |
| Citation Verification | Ensure evidence citations exist | MUST reject invalid citations |

## Control Operations

### CONTROL.evaluate()

The system **MUST** evaluate proposals before execution:

```
CONTROL.evaluate(proposal, state) -> Decision

Parameters:
  - proposal:
      type: "create_artifact" | "execute_task" | "modify_file"
      target: "<file path or artifact name>"
      action: "<specific action>"
      evidence: ["<citations>"]
      requirements: ["<REQ-IDs>"]
  - state:
      memory: current memory state
      regulation: active regulation rules

Returns:
  - decision:
      approve: true | false | defer
      reason: "<explanation>"
      violated_rules: ["<rule-ids>"] if any
      required_actions: ["<actions to take first>"] if deferred

Validation Steps:
  1. Check preconditions exist in memory
  2. Verify evidence citations resolve
  3. Check scope boundaries
  4. Evaluate conditional logic
  5. Check for duplicates
  6. Verify regulation compliance
```

### CONTROL.check_preconditions()

The system **MUST** verify preconditions before approval:

```
CONTROL.check_preconditions(action) -> PreconditionResult

Parameters:
  - action:
      type: "<action type>"
      dependencies: ["<required prior actions>"]
      required_files: ["<files that must exist>"]
      required_decisions: ["<DEC-IDs>"]

Returns:
  - satisfied: true | false
  - missing: ["<missing preconditions>"]
  - blocking: ["<blocking issues>"]

Requirements:
  - All dependencies MUST be complete
  - All required files MUST exist
  - All required decisions MUST be in memory
```

### CONTROL.deduplicate()

The system **MUST** prevent redundant actions:

```
CONTROL.deduplicate(action) -> DeduplicationResult

Parameters:
  - action:
      type: "<action type>"
      target: "<target>"
      arguments: "<action arguments>"

Returns:
  - is_duplicate: true | false
  - prior_occurrence: "<where this was done before>" | null
  - cached_result: "<result if available>" | null

Requirements:
  - Repeated actions with same arguments MUST be flagged
  - Cached results MUST be returned when available
  - State changes MUST invalidate relevant caches
```

### CONTROL.enforce_scope()

The system **MUST** enforce file modification boundaries:

```
CONTROL.enforce_scope(action, scope) -> ScopeResult

Parameters:
  - action:
      type: "create" | "modify" | "delete"
      target: "<file path>"
  - scope:
      allowed_paths: ["<glob patterns>"]
      blocked_paths: ["<glob patterns>"]
      allowed_operations: ["create", "modify", "delete"]

Returns:
  - allowed: true | false
  - reason: "<explanation if blocked>"
  - matching_rule: "<rule that determined result>"

Requirements:
  - Actions outside allowed_paths MUST be blocked
  - Actions matching blocked_paths MUST be blocked
  - Operations not in allowed_operations MUST be blocked
```

### CONTROL.verify_citations()

The system **MUST** validate evidence citations:

```
CONTROL.verify_citations(citations) -> CitationResult

Parameters:
  - citations: ["<file>#<location>", ...]

Returns:
  - all_valid: true | false
  - results: [
      {
        citation: "<citation>",
        valid: true | false,
        resolved_to: "<path>" | null,
        error: "<message>" | null
      }
    ]

Requirements:
  - All citations MUST resolve to existing content
  - Invalid citations MUST cause rejection
  - Citation format MUST be: "<file>#<location>"
```

### CONTROL.check_termination()

The system **MUST** detect goal satisfaction:

```
CONTROL.check_termination(state) -> TerminationResult

Parameters:
  - state:
      goal: "<original goal>"
      completed_actions: ["<actions>"]
      pending_actions: ["<actions>"]
      memory: current memory state

Returns:
  - ready: true | false
  - reason: "<why ready or not>"
  - remaining: ["<what's left>"] if not ready
  - goal_fidelity: 0.0 - 1.0

Requirements:
  - All required actions MUST be complete
  - All requirements MUST be verified
  - Goal fidelity MUST be >= 0.8 for termination
```

### Termination Success Output

When verification passes and the system is ready for archive, the output **MUST** display:

```
═══════════════════════════════════════════════════════════════
✓ VERIFICATION PASSED
═══════════════════════════════════════════════════════════════

All requirements implemented and verified:
- X/X requirements with status="implemented"
- Y/Y tests passing
- Z/Z citations valid
- Memory consistent across all artifacts
- Goal fidelity: 1.0 (100%)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

NEXT STEP: Run /sdd-archive to merge this change into
accumulated specs and complete the workflow.

═══════════════════════════════════════════════════════════════
```

**Important:** The archive command is `/sdd-archive`.

## Control Checkpoint Schema

The system **MUST** record control decisions in `control-log.json`:

```json
{
  "id": "CHK-NNN",
  "timestamp": "<ISO 8601>",
  "phase": "artefact-creation | task-execution | verification",
  "action": {
    "type": "<action type>",
    "target": "<target>",
    "proposer": "<who proposed this>"
  },
  "checks": [
    {
      "name": "<check name>",
      "status": "pass | fail | warn",
      "duration_ms": "<milliseconds>",
      "message": "<description>",
      "details": {}
    }
  ],
  "decision": {
    "approve": true | false | defer,
    "reason": "<explanation>",
    "violated_rules": [],
    "required_actions": []
  },
  "overall": "pass | fail | warn",
  "blocked": true | false
}
```

## Regulation Enforcement

### Regulation Loading

The system **MUST** load regulation rules from `regulation.md`:

```markdown
# Epistemic Constitution: <change-name>

## Evidential Rules
1. Every requirement MUST cite its source
2. Every decision MUST document alternatives
3. Every task MUST reference at least one requirement

## Scope Rules
1. Tasks in Group N can only modify files from Groups 1..N
2. Files outside allowed paths MUST NOT be modified
3. Modifying completed groups MUST require explicit override

## Validation Rules
1. Tasks MUST be verified before marked complete
2. Files MUST have header comments citing requirements
3. Subagents MUST output completion signal

## Memory Rules
1. After each group, memory MUST be updated
2. Citations MUST use specified format
3. Contradictions MUST be explicitly resolved
```

### Rule Schema

The system **MUST** parse rules into the following schema:

```json
{
  "rule_id": "RULE-NNN",
  "category": "evidential | scope | validation | memory",
  "statement": "<the rule text>",
  "keywords": ["MUST", "MUST NOT", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "MAY"],
  "enforcement": "block | warn | log",
  "exceptions": ["<conditions where rule does not apply>"]
}
```

### Rule Evaluation

The system **MUST** evaluate actions against all applicable rules:

```
CONTROL.evaluate_rules(action, rules) -> RuleEvaluationResult

Returns:
  - compliant: true | false
  - violations: [
      {
        rule_id: "<RULE-NNN>",
        severity: "block" | "warn" | "log",
        message: "<what was violated>"
      }
    ]
  - warnings: ["<non-blocking issues>"]

Enforcement:
  - Block violations MUST prevent action
  - Warning violations MUST be logged but allow action
  - Log violations MUST be recorded only
```

## Subagent Scope Constraints

### Scope Definition

When dispatching subagents, the Control module **MUST** generate scope constraints:

```json
{
  "subagent_id": "SA-GROUP-N",
  "task_group": N,
  "scope": {
    "allowed_files": ["src/auth/**/*.ts"],
    "blocked_files": ["src/core/*", "tests/*"],
    "allowed_tools": ["read", "write", "edit"],
    "blocked_tools": ["bash:rm", "bash:git push"],
    "required_citations": ["design.md#*", "spec.md#*"]
  },
  "preconditions": {
    "required_files": ["src/models/User.ts"],
    "required_decisions": ["DEC-001"],
    "required_groups_complete": [1]
  },
  "completion_criteria": {
    "tasks": ["2.1", "2.2", "2.3"],
    "required_outputs": ["GROUP 2 COMPLETE"],
    "files_must_exist": ["src/auth/utils/hash.ts"]
  }
}
```

### Scope Verification

After subagent completion, the Control module **MUST** verify:

```
CONTROL.verify_scope_completion(result, scope) -> VerificationResult

Checks:
  1. All tasks in completion_criteria.tasks are marked complete
  2. All files in completion_criteria.files_must_exist exist
  3. No files outside scope.allowed_files were modified
  4. Completion signal was output
  5. No blocked tools were used

Returns:
  - verified: true | false
  - violations: ["<scope violations>"]
  - warnings: ["<non-blocking issues>"]
```

## Control Flow Integration

### In Artifact Creation

```
1. COGNITION: Propose artifact content
2. CONTROL.evaluate(proposal):
   - Check preconditions (dependencies exist)
   - Verify citations
   - Check regulation compliance
3. IF approved:
   - ACTION: Write artifact
   - MEMORY: Update with extractions
4. IF deferred:
   - Return to COGNITION with required_actions
5. IF blocked:
   - Log violation, halt, request intervention
```

### In Task Execution

```
1. COGNITION: Propose task execution (by subagent)
2. CONTROL.check_preconditions(task):
   - Verify dependencies complete
   - Verify required files exist
3. CONTROL.enforce_scope(task, scope):
   - Check allowed files
   - Check blocked operations
4. Dispatch subagent with approved scope
5. After completion, CONTROL.verify_scope_completion(result):
   - Check files modified
   - Check completion signal
   - Check task checkboxes
6. CONTROL.check_termination():
   - Determine if goal is satisfied
```

## Error Handling

### Blocking Errors

The Control module **MUST** block execution when:

1. Preconditions are not satisfied
2. Scope boundaries are violated
3. Citation targets do not exist
4. Regulation rules with "block" enforcement are violated
5. Duplicate actions are detected (unless explicitly allowed)

### Warning Conditions

The Control module **SHOULD** warn but allow when:

1. Citations exist but are unverified
2. Regulation rules with "warn" enforcement are violated
3. Task is marked complete without full verification
4. Memory state is stale (not recently updated)

### Recovery Actions

When blocking occurs, the system **MUST** provide:

1. Clear explanation of what was blocked
2. Which rule or precondition failed
3. Required actions to proceed
4. Option to override (if regulation allows)
