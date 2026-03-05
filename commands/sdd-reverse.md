---
name: sdd-reverse
description: Extract specifications from existing code for brownfield projects
---

Analyze existing code, detect capabilities, and create separate specs per capability.

**Usage:** `/sdd-reverse <path> [options]`

**Options:**
- `<path>` - Directory or file to analyze (e.g., `src/auth/`)
- `--depth=shallow` - Quick overview (capability boundaries only)
- `--depth=medium` - Default - read key files, infer requirements
- `--depth=deep` - Thorough analysis, all files

**Process:**

1. **Scan code:**
   - Analyze file structure
   - Detect capability boundaries
   - Group related files/functions

2. **Confirm capabilities:**
   ```
   Detected 4 capabilities:
   ┌─────────────────┬─────────────────────┐
   │ Capability      │ Files               │
   ├─────────────────┼─────────────────────┤
   │ authentication  │ login.ts, logout.ts │
   │ registration    │ registration.ts     │
   │ password-reset  │ password-reset.ts   │
   │ session-mgmt    │ session.ts          │
   └─────────────────┴─────────────────────┘
   
   [1] Create separate specs per capability (recommended)
   [2] Create single combined spec
   [3] Let me select which to spec
   ```

3. **Analyze in parallel:**
   - Dispatch subagent per capability
   - Each extracts requirements from its files
   - Aggregates into separate specs

4. **Create spec structure:**
   ```
   .specs/specs/{module}/
   ├── proposal.md           # Overview
   └── specs/
       ├── {capability-1}/spec.md
       ├── {capability-2}/spec.md
       └── ...
   ```

**Example:**
```
You: /sdd-reverse src/auth/ --depth=medium

AI: Scanning src/auth/ (12 files)...

Detected 4 capabilities:
- authentication (login.ts, logout.ts, token.ts)
- registration (registration.ts, validation.ts)
- password-reset (password-reset.ts)
- session-mgmt (session.ts)

Analyzing 4 capabilities in parallel...
[====] 100%

✓ Created .specs/specs/auth/
  ├── proposal.md
  └── specs/
      ├── authentication/spec.md (3 requirements)
      ├── registration/spec.md (2 requirements)
      ├── password-reset/spec.md (2 requirements)
      └── session-mgmt/spec.md (2 requirements)

Next: Use /sdd-explore to explore a change, then /sdd-propose to create a spec in .specs/changes/
that references these existing capabilities.
```

**Use when:**
- Working with legacy/undocumented code
- Need to understand before changing
- Establishing specs for refactoring
