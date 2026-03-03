# Commands Reference

All 19 SDD commands organized by workflow phase.

## Create Phase

### `/sdd-new`

Start a new specification.

**Creates:** `.specs/changes/<name>/proposal.md`

**Usage:**
```
/sdd-new
```

Prompts for feature description, then creates proposal with WHY, WHAT, capabilities, and scope.

---

### `/sdd-init`

Initialize SDD project structure.

**Creates:** `.specs/specs/`, `.specs/changes/`, `.specs/archive/`

**Usage:**
```
/sdd-init
```

Run once per project to create the `.specs/` directory structure.

---

### `/sdd-init-scl`

Initialize project with SCL structure.

**Creates:** `.specs/` structure + `.memory/` template

**Usage:**
```
/sdd-init-scl
```

Prepares project for SCL-enhanced workflow from the start.

---

### `/sdd-init-memory`

Initialize SCL memory for a specific change.

**Creates:** `.memory/` directory, `regulation.md`

**Usage:**
```
/sdd-init-memory <change-name>
```

Creates memory structure for tracking decisions, requirements, citations, and episodes.

---

## Develop Phase

### `/sdd-artefact`

Create the next ready artifact incrementally.

**Creates:** `specs/`, `design.md`, or `tasks.md`

**Usage:**
```
/sdd-artefact
```

Creates artifacts in order: specs → design → tasks. Only creates the next ready artifact.

---

### `/sdd-artefact-scl`

Create artifact with SCL memory tracking.

**Creates:** Artifact + memory updates

**Usage:**
```
/sdd-artefact-scl
```

Follows SCL 5-phase loop: Retrieve → Cognition → Control → Action → Memory Write.

---

### `/sdd-ff`

Fast-forward: create all artifacts at once.

**Creates:** `specs/`, `design.md`, `tasks.md`

**Usage:**
```
/sdd-ff
```

Use when you have a clear understanding and want to skip incremental creation.

---

### `/sdd-explore`

Think through an idea before committing.

**Usage:**
```
/sdd-explore
```

Interactive exploration to clarify requirements and identify gaps. Does not create files.

---

### `/sdd-status`

Check progress of current change.

**Usage:**
```
/sdd-status
```

Shows which artifacts are complete, ready, or blocked.

---

### `/sdd-reverse`

Extract specs from existing code (brownfield).

**Creates:** Specs in `.specs/specs/`

**Usage:**
```
/sdd-reverse src/<directory>/
```

Scans directory, detects capabilities, creates specification files. Use for existing projects without specs.

---

## Implement Phase

### `/sdd-apply`

Implement one task at a time.

**Usage:**
```
/sdd-apply
```

Finds next incomplete task, implements it, marks complete. Best for learning or risky changes.

---

### `/sdd-apply-group`

Execute all tasks in a specific group.

**Usage:**
```
/sdd-apply-group <N>
```

Groups are defined in `tasks.md` with metadata for dependencies and execution order.

---

### `/sdd-apply-all`

Execute all groups via subagents.

**Usage:**
```
/sdd-apply-all
```

Groups execute in dependency order. Each group dispatched to a subagent with strict scope.

---

### `/sdd-apply-group-scl`

Execute group with SCL memory context.

**Usage:**
```
/sdd-apply-group-scl <N>
```

Before dispatch: loads memory, verifies preconditions, generates scope constraints. After completion: verifies scope, updates memory.

---

### `/sdd-apply-all-scl`

Execute all groups with SCL memory persistence.

**Usage:**
```
/sdd-apply-all-scl
```

Full parallel execution with memory context injected into each subagent.

---

## Verify Phase

### `/sdd-verify`

Verify implementation matches spec.

**Usage:**
```
/sdd-verify
/sdd-verify --spec <name>
/sdd-verify --all
```

Checks requirements coverage, scenario coverage, and test status.

---

### `/sdd-verify-scl`

Verify with SCL memory tracing.

**Usage:**
```
/sdd-verify-scl
```

Full verification including:
- Requirement verification with memory tracing
- Decision compliance check
- Citation integrity verification
- Memory consistency check
- Goal fidelity score

---

### `/sdd-memory-status`

Inspect SCL memory state.

**Usage:**
```
/sdd-memory-status <change-name>
```

Shows decisions, requirements, citations, control log, and episodes.

---

## Complete Phase

### `/sdd-archive`

Complete and archive the change.

**Creates:** `SUMMARY.md`, moves to `.specs/archive/`

**Usage:**
```
/sdd-archive
```

Verifies completion, creates summary, moves to archive, merges deltas into `.specs/specs/`.

---

## Command Summary Table

| Command | Phase | SCL | Purpose |
|---------|-------|-----|---------|
| `/sdd-new` | Create | No | Start new spec |
| `/sdd-init` | Create | No | Initialize project |
| `/sdd-init-scl` | Create | Yes | Initialize with SCL |
| `/sdd-init-memory` | Create | Yes | Initialize memory for change (after /sdd-new) |
| `/sdd-artefact` | Develop | No | Create next artifact |
| `/sdd-artefact-scl` | Develop | Yes | Create with memory tracking |
| `/sdd-ff` | Develop | No | Fast-forward all artifacts |
| `/sdd-explore` | Develop | No | Explore before committing |
| `/sdd-status` | Develop | No | Check progress |
| `/sdd-reverse` | Develop | No | Extract specs from code |
| `/sdd-apply` | Implement | No | One task at a time |
| `/sdd-apply-group` | Implement | No | Execute group N |
| `/sdd-apply-all` | Implement | No | Execute all groups |
| `/sdd-apply-group-scl` | Implement | Yes | Group with memory context |
| `/sdd-apply-all-scl` | Implement | Yes | All groups with persistence |
| `/sdd-verify` | Verify | No | Verify implementation |
| `/sdd-verify-scl` | Verify | Yes | Verify with memory tracing |
| `/sdd-memory-status` | Verify | Yes | Inspect memory state |
| `/sdd-archive` | Complete | No | Complete and archive |
