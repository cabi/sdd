# Commands Reference

All 12 SDD commands organized by workflow phase.

## Create Phase

### `/sdd-explore`

Explore an idea and create exploration context.

**Creates:** `.specs/changes/<name>/context-log.md`

**Usage:**
```
/sdd-explore [name]
```

Runs exploration interview to capture Q&A, goals, constraints, and options. Auto-generates name from description if not provided.

---

### `/sdd-propose`

Create formal proposal from exploration context.

**Creates:** `.specs/changes/<name>/proposal.md`

**Usage:**
```
/sdd-propose <name>
```

Requires context-log.md to exist (run /sdd-explore first). Creates proposal with Context Log, Goals, Constraints, and Exploration Notes sections.

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

Explore an idea and create exploration context.

**Creates:** `.specs/changes/<name>/context-log.md`

**Usage:**
```
/sdd-explore [name]
```

Interactive exploration that creates structured context-log capturing Q&A, goals, constraints, options, and risks. Auto-generates name if not provided.

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

| Command | Phase | Purpose |
|---------|-------|---------|
| `/sdd-explore` | Create | Explore idea, create context-log |
| `/sdd-propose` | Create | Create proposal from context-log |
| `/sdd-init` | Create | Initialize project |
| `/sdd-artefact` | Develop | Create next artifact |
| `/sdd-ff` | Develop | Fast-forward all artifacts |
| `/sdd-status` | Develop | Check progress |
| `/sdd-reverse` | Develop | Extract specs from code |
| `/sdd-apply` | Implement | One task at a time |
| `/sdd-apply-group` | Implement | Execute group N |
| `/sdd-apply-all` | Implement | Execute all groups |
| `/sdd-verify` | Verify | Verify implementation |
| `/sdd-archive` | Complete | Complete and archive |
