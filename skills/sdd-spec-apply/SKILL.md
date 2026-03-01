---
name: sdd-spec-apply
description: Implement a spec by working through tasks. Supports single-task, group, or all-tasks execution modes. Group execution uses subagents with strict scope boundaries.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "2.0.0"
---

# SDD Spec Apply

Implement a specification by working through tasks.

## Execution Modes

| Mode | Command | Behavior |
|------|---------|----------|
| Single | `/sdd-apply` | One task, ask to continue |
| Group | `/sdd-apply-group N` | All tasks in group N via subagent |
| All | `/sdd-apply-all` | All groups via subagents |

## Pre-Conditions

1. Spec exists at `.specs/changes/<name>/`
2. Tasks artifact has content (not just placeholder)
3. All dependencies are understood

---

## Mode 1: Single Task (`/sdd-apply`)

Interactive, one task at a time with review between each.

### Process

1. Load context (proposal, specs, design, tasks)
2. Find next unchecked task
3. Implement minimally
4. Mark complete
5. Report progress
6. Ask to continue

### When to Use

- Learning new codebase
- High-risk changes
- Want full control over each step

---

## Mode 2: Group Execution (`/sdd-apply-group N`)

Execute all tasks in a group via a single subagent with strict scope.

### Process

```
1. Parse tasks.md, identify group N
2. Build constrained subagent prompt
3. Dispatch subagent
4. Verify completion signal
5. Check task checkboxes
6. Verify file modifications
7. Report results
```

### Subagent Prompt Template

When dispatching a subagent for a group, use this structure:

```markdown
# Execute Task Group: {GROUP_NAME}

You are executing ONE task group from an SDD specification.

## MANDATORY SCOPE CONSTRAINTS

**Your group:** {GROUP_NUMBER}
**Your tasks:** {TASK_LIST} (e.g., "2.1, 2.2, 2.3")
**Total tasks:** {TASK_COUNT}

**STOP IMMEDIATELY if:**
- You complete all tasks in your list
- You feel tempted to start task {NEXT_GROUP_FIRST_TASK}
- You need to modify files outside your allowed list
- Any task seems to require work outside your group

## CONTEXT

### From Proposal
{PROPOSAL_CONTEXT}

### Relevant Requirements
{SPEC_SECTIONS_FOR_THIS_GROUP}

### Relevant Design Decisions
{DESIGN_DECISIONS_FOR_THIS_GROUP}

## YOUR TASKS

{FULL_TASK_CONTENT_FOR_GROUP}

## ALLOWED FILE MODIFICATIONS

You may ONLY create/modify these files:
{ALLOWED_FILES_LIST}

You may ONLY modify checkboxes in:
.specs/changes/{SPEC_NAME}/tasks.md

## IMPLEMENTATION RULES

1. Mark each task complete immediately after finishing it
2. Follow the design decisions from context
3. Reference requirement IDs in code comments
4. Only modify files in your allowed list

## COMPLETION REQUIREMENT

When ALL your tasks are done, output EXACTLY this format:

---
GROUP {GROUP_NUMBER} COMPLETE
Completed: {TASK_LIST}
Files created: {list}
Files modified: {list}
---

Then STOP. Do not offer to continue. Do not suggest next steps.
Do not output anything after this signal.
```

### Verification After Subagent

```
1. Parse subagent output for "GROUP N COMPLETE" signal
2. Extract completed tasks list
3. Extract files created/modified
4. Verify tasks.md checkboxes match expected
5. Verify files modified are in allowed list
6. If mismatch → warn user, may need review
```

### When to Use

- Well-defined group of related tasks
- Group has clear boundaries
- Want to batch related work

---

## Mode 3: All Groups (`/sdd-apply-all`)

Execute all remaining groups in dependency order.

### Process

```
1. Parse tasks.md, identify all groups
2. Build dependency graph from group metadata
3. Determine execution order (topological sort)
4. For each group (in order):
   a. Check dependencies satisfied
   b. Dispatch subagent with constrained prompt
   c. Wait for completion signal
   d. Verify results
   e. Report progress
   f. Optionally: ask user to confirm before next group
5. Final summary
```

### Dependency Resolution

Groups have metadata indicating dependencies:

```markdown
## 1. Setup
_Meta: sequential, foundation_

## 2. Core Services
_Meta: parallel-safe, depends on: 1_

## 3. API Routes
_Meta: sequential, depends on: 2_
```

Execution order:
```
Wave 1: Group 1 (Setup)
Wave 2: Group 2 (Core Services) - after Group 1 complete
Wave 3: Group 3 (API Routes) - after Group 2 complete
```

### Parallel Groups (Optional)

If multiple groups have `_Meta: parallel-safe, depends on: 1`:

```
Wave 1: Group 1
Wave 2: Group 2, Group 3 (dispatch simultaneously)
Wave 3: Group 4 (depends on 2 and 3)
```

**Note:** Parallel groups must be truly independent (no shared file modifications).

### User Confirmation Options

```
You: /sdd-apply-all

AI: Found 4 task groups to execute:

| Group | Tasks | Dependencies | Parallel-Safe |
|-------|-------|--------------|---------------|
| 1. Setup | 3 | none | no |
| 2. Core Services | 3 | 1 | yes |
| 3. API Routes | 4 | 2 | no |
| 4. Testing | 2 | 3 | no |

Execution will be:
  Step 1: Group 1 (sequential)
  Step 2: Group 2 (sequential, could parallelize with nothing)
  Step 3: Group 3 (sequential)
  Step 4: Group 4 (sequential)

Options:
[1] Run all automatically
[2] Confirm after each group
[3] Confirm after Groups 1-2, then auto

You: 2
```

### When to Use

- Large project with many tasks
- Well-defined spec with clear group boundaries
- Groups are mostly independent
- User unavailable for extended period

---

## Task Format for Group Execution

Tasks must include group-level metadata:

```markdown
## 1. Setup
_Meta: sequential, foundation for all groups_

- [ ] 1.1 Create directory structure
  - _Creates: src/auth/_
- [ ] 1.2 Install dependencies
  - _Modifies: package.json_
- [ ] 1.3 Create database migrations
  - _Creates: migrations/001_users.sql_

## 2. Core Services
_Meta: parallel-safe, depends on: 1_

- [ ] 2.1 Create PasswordService
  - _Creates: src/auth/services/PasswordService.ts_
  - _Requirements: auth-001_
- [ ] 2.2 Create TokenService
  - _Creates: src/auth/services/TokenService.ts_
  - _Requirements: auth-002_
- [ ] 2.3 Create UserService
  - _Creates: src/auth/services/UserService.ts_
  - _Requirements: auth-003, auth-004_

## 3. API Routes
_Meta: sequential, depends on: 2_

- [ ] 3.1 Create registration route
  - _Modifies: src/auth/routes/index.ts_
  - _Requirements: auth-001_
- [ ] 3.2 Create login route
  - _Modifies: src/auth/routes/index.ts_
  - _Requirements: auth-002_
```

### Metadata Fields

| Field | Purpose | Example |
|-------|---------|---------|
| `sequential` | Must run alone, no parallelism | Setup, migrations |
| `parallel-safe` | Can run alongside other parallel-safe groups | Independent services |
| `depends on: N` | Requires group N to complete first | API routes depend on services |

### Per-Task Fields

| Field | Purpose |
|-------|---------|
| `_Creates: path_` | New file created (safe for parallel) |
| `_Modifies: path_` | Existing file modified (check for conflicts) |
| `_Requirements: ids_` | Links to spec requirements |

---

## Handling Errors

### Subagent Stops Early

```
AI: Group 2 subagent returned without "GROUP 2 COMPLETE" signal.

Completed tasks found: 2.1, 2.2
Expected: 2.1, 2.2, 2.3

Options:
[1] Review what was done, continue manually
[2] Retry Group 2 with remaining task
[3] Abort, investigate issue
```

### Subagent Exceeds Scope

```
AI: WARNING - Group 2 subagent modified files outside allowed list.

Allowed: src/auth/services/*
Modified: src/auth/routes/index.ts (NOT ALLOWED)

Options:
[1] Review changes, accept if appropriate
[2] Revert unauthorized changes
[3] Abort entire operation
```

### Dependency Not Satisfied

```
AI: Cannot start Group 3 - dependency not satisfied.

Group 3 depends on: Group 2
Group 2 status: 2/3 tasks complete (incomplete)

Options:
[1] Complete Group 2 first
[2] Force start Group 3 anyway (risky)
[3] Abort
```

---

## Summary

| Mode | Control | Speed | Risk |
|------|---------|-------|------|
| `/sdd-apply` | High | Slow | Low |
| `/sdd-apply-group N` | Medium | Medium | Medium |
| `/sdd-apply-all` | Low | Fast | Medium |

**Recommendations:**
- Use `/sdd-apply` for high-risk or learning
- Use `/sdd-apply-group` for batching related work
- Use `/sdd-apply-all` with confirmation mode for large projects
