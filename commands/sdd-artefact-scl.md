---
name: sdd-artefact-scl
description: Create the next artifact using SCL approach with memory persistence and control validation
---

Create the next artifact using the Structured Cognitive Loop (SCL) approach.

**Usage:** `/sdd-artefact-scl`

**Process (SCL-Enhanced):**

## Phase 1: Retrieve

1. **Detect current change** - Scan `.specs/changes/`, ask if multiple
2. **Check artifact status** - DONE, READY, BLOCKED
3. **Load memory context**:
   - Read `.memory/decisions.json` for prior decisions
   - Read `.memory/requirements.json` for existing requirements
   - Read `.memory/control-log.json` for blocking issues
   - Read `regulation.md` for active rules
4. **Read dependencies**:
   - For specs: proposal.md
   - For design: proposal.md, specs/**/*.md
   - For tasks: proposal.md, specs/**/*.md, design.md

## Phase 2: Cognition

5. **Generate artifact** with evidential grounding:
   - Every requirement MUST cite source
   - Every decision MUST cite alternatives
   - Every task MUST cite evidence
6. **Show preview** before writing

## Phase 3: Control

7. **Validate citations** - All citations MUST resolve
8. **Check regulation compliance** - Block on violations
9. **Check consistency** - No contradictions with memory

## Phase 4: Action

10. **Write artifact** (if control approved)
11. **Update proposal status**

## Phase 5: Memory Update

12. **Extract decisions** → `.memory/decisions.json`
13. **Extract requirements** → `.memory/requirements.json`
14. **Record citations** → `.memory/citations.json`
15. **Log checkpoint** → `.memory/control-log.json`

**Output:**
```
✓ Retrieved: Memory context loaded
✓ Cognition: Generated specs/auth/spec.md with 5 requirements
✓ Control: All 12 citations verified, regulation compliant
✓ Action: Written to specs/auth/spec.md
✓ Memory: Updated decisions.json, requirements.json, citations.json

Artifact Status:
  proposal: DONE
  specs: DONE ← just completed
  design: READY
  tasks: BLOCKED (waiting for design)

Memory State:
  decisions: 3
  requirements: 5
  citations: 12

Next: Use /sdd-artefact-scl to create design
```

**If control blocks:**
```
✗ Control: BLOCKED

Failed checks:
  - Citation "design.md#L999" does not exist
  - Regulation violation: Requirement AUTH-003 missing source

Required actions:
  1. Add valid citation for AUTH-003 source
  2. Remove or fix invalid citation

Memory state preserved. Re-run after fixes.
```

After creating, report status and output EXACTLY this Next Steps section:

---
## Next Steps

- If specs created: Use `/sdd-artefact-scl` to create design
- If design created: Use `/sdd-artefact-scl` to create tasks
- If tasks created:
  - `/sdd-apply-group-scl N` - Execute group N with memory context
  - `/sdd-apply-all-scl` - Execute all groups
- Use `/sdd-status` or `/sdd-memory-status` to check progress
---

DO NOT suggest commands not listed above.

**Loads skill:** `sdd-artefact-scl`
