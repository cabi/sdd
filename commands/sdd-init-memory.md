---
name: sdd-init-memory
description: Initialize SCL memory structure for a change specification
---

Initialize the SCL memory structure for the current or specified change.

**Usage:** `/sdd-init-memory [change-name]`

**Process:**

1. **Determine target**:
   - If change-name provided: Initialize `.specs/changes/<change-name>/.memory/`
   - If no change-name: Use current active change or ask

2. **Create memory structure**:
   ```
   .specs/changes/<change-name>/.memory/
   ├── decisions.json
   ├── requirements.json
   ├── citations.json
   ├── control-log.json
   └── episodes.json
   ```

3. **Initialize each file** with empty schema:
   ```json
   {
     "version": "1.0",
     "change_name": "<change-name>",
     "created_at": "<timestamp>",
     "decisions": []
   }
   ```

4. **Create regulation.md** from template if not exists

5. **Report initialization**

**Output:**
```
✓ Initialized memory for: user-authentication

Created:
  .specs/changes/user-authentication/.memory/
  ├── decisions.json      (0 decisions)
  ├── requirements.json   (0 requirements)
  ├── citations.json      (0 citations)
  ├── control-log.json    (0 checkpoints)
  └── episodes.json       (0 episodes)

Created:
  .specs/changes/user-authentication/regulation.md

Memory is ready for SCL-enhanced artifact creation.
Use /sdd-artefact-scl to create artifacts with memory tracking.
```

**Loads skills:** `sdd-memory`
