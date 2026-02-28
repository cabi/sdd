---
name: sdd-reverse
description: Extract specifications from existing code. Detects capabilities and creates separate specs per capability, useful for brownfield projects before making changes.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: advanced
  author: OpenCode
  version: "2.0.0"
---

# SDD Reverse: Spec Extraction from Code

Create specifications from existing code to understand brownfield projects before making changes.

## When to Use

- Inherited a legacy codebase without documentation
- Need to make changes but don't understand existing system
- Want to establish baseline before refactoring
- Team needs shared understanding of current behavior

## When NOT to Use

- Well-documented codebase
- Simple, self-explanatory code
- Just adding isolated new features

---

## Key Principle: Capability Detection

Instead of creating one giant spec, detect **capabilities** and create **separate specs per capability**:

```
/src/auth/
├── login.ts
├── logout.ts
├── registration.ts
├── password-reset.ts
└── session.ts

Creates:
.specs/specs/auth/
├── specs/
│   ├── authentication/spec.md    # login, logout
│   ├── registration/spec.md      # registration
│   ├── password-reset/spec.md    # password-reset
│   └── session-mgmt/spec.md      # session
└── proposal.md                   # Overview
```

This matches the SDD workflow structure and enables proper delta specs.

---

## Process

### Step 1: Scope Definition

Ask user to define scope:

```
What should I analyze?

Options:
[1] Specific module/directory (e.g., src/auth/)
[2] Specific feature (e.g., "user registration")
[3] Entire codebase (time-intensive)
[4] Files matching pattern (e.g., **/api/*.ts)
```

### Step 2: Capability Detection

Analyze code to detect capabilities:

```
/sdd-reverse src/auth/

AI: Scanning src/auth/...

Detected 4 capabilities:

┌─────────────────┬─────────────────────┬──────────────┐
│ Capability      │ Files               │ Functions    │
├─────────────────┼─────────────────────┼──────────────┤
│ authentication  │ login.ts, logout.ts │ login,       │
│                 │                     │ logout,      │
│                 │                     │ validateToken│
├─────────────────┼─────────────────────┼──────────────┤
│ registration    │ registration.ts     │ register,    │
│                 │                     │ validateEmail│
├─────────────────┼─────────────────────┼──────────────┤
│ password-reset  │ password-reset.ts   │ requestReset,│
│                 │                     │ confirmReset │
├─────────────────┼─────────────────────┼──────────────┤
│ session-mgmt    │ session.ts          │ createSession│
│                 │                     │ destroySession│
└─────────────────┴─────────────────────┴──────────────┘

How should I create specs?
[1] Separate specs per capability (recommended)
[2] Single combined spec
[3] Let me select which capabilities to spec
```

### Step 3: Capability Detection Rules

Detect capabilities by analyzing:

| Signal | Weight | Example |
|--------|--------|---------|
| File/directory names | High | `registration.ts` → registration capability |
| Function grouping | High | login, logout together → authentication |
| Data model focus | Medium | UserAuth, UserRegistration different models |
| External dependencies | Medium | Calls to email service → notification capability |
| Test file grouping | Medium | `auth.test.ts` boundaries |

**Naming convention:**
- Use kebab-case: `authentication`, `password-reset`
- Keep names concise but descriptive
- Avoid generic names like `utils`, `helpers`

### Step 4: Parallel Analysis

For multiple capabilities, dispatch subagents:

```
AI: Will analyze 4 capabilities in parallel:

Agent A: authentication (2 files, 3 functions)
Agent B: registration (1 file, 2 functions)
Agent C: password-reset (1 file, 2 functions)
Agent D: session-mgmt (1 file, 2 functions)

Each agent will:
1. Read files for its capability
2. Infer requirements from code
3. Extract data models
4. Identify dependencies

Dispatching...
```

#### Subagent Prompt for Capability Analysis

```markdown
# Capability Analysis Task

Analyze code and extract specifications for ONE capability.

## YOUR SCOPE

**Capability:** {CAPABILITY_NAME}
**Files:** {FILE_LIST}
**Depth:** {shallow|medium|deep}

## CONSTRAINTS

You are analyzing ONLY this capability.
- DO NOT analyze other capabilities
- DO NOT infer requirements outside this scope
- DO NOT reference files not in your list

## WHAT TO EXTRACT

1. **Purpose**: What does this capability do?
2. **Public Interface**: Exported functions/classes
3. **Data Models**: Data structures used
4. **Requirements**: Infer from code behavior
5. **Dependencies**: What this depends on

## OUTPUT FORMAT

Return EXACTLY this format:

```markdown
# Specification: {capability-name}

> Baseline spec documenting EXISTING behavior.
> Generated: {date}

## Overview
<What this capability does>

## Requirements

### Requirement: {inferred-name}
The system currently <behavior>.

#### Scenario: {scenario-name}
- **WHEN** <condition>
- **THEN** <behavior>

## Data Models

<From interfaces/schemas>

## Dependencies

- Internal: <list>
- External: <list>

## Open Questions

<things unclear from code>
```

## FILES TO ANALYZE

{FILE_CONTENTS}
```

### Step 5: Create Spec Structure

Create specs in the main specs folder (this becomes the single source of truth):

```
.specs/specs/{module-name}/
├── proposal.md              # Overview of module
├── specs/
│   ├── {capability-1}/
│   │   └── spec.md
│   ├── {capability-2}/
│   │   └── spec.md
│   └── {capability-N}/
│       └── spec.md
└── design.md (optional)     # Architecture overview
```

#### Proposal Template

```markdown
# Proposal: {module-name} (Baseline)

> This is a baseline spec documenting existing behavior.
> Generated: {date}

## Why
Reverse-engineered from existing code to establish baseline understanding.

## Capabilities

### Documented Capabilities
- `{capability-1}`: <brief description>
- `{capability-2}`: <brief description>
- `{capability-N}`: <brief description>

## Module Overview
<High-level description of what this module does>

## Key Files
| File | Purpose |
|------|---------|
| path/to/file.ts | <purpose> |

## Technical Debt / Notes
<Observed issues or patterns during analysis>
```

---

## Analysis Depth

### Shallow Analysis (Quick)

- File structure
- Public interfaces
- Capability boundaries

**Time:** Minutes
**Output:** Capability list, basic spec skeletons

### Medium Analysis (Default)

- Shallow +
- Read key files
- Infer requirements
- Identify patterns

**Time:** 10-30 minutes per module
**Output:** Full specs with requirements

### Deep Analysis (Thorough)

- Medium +
- Read all files
- Trace all paths
- Document edge cases

**Time:** Hours per module
**Output:** Comprehensive specs with all scenarios

---

## Handling Edge Cases

### Tightly Coupled Code

If code can't be cleanly separated:

```
AI: Code is tightly coupled. Detected:
- 80% of functions touch shared state
- No clear capability boundaries

Options:
[1] Create combined spec (not recommended but sometimes necessary)
[2] Create specs with shared dependencies section
[3] Focus on specific subset of files
```

### Shared Utilities

```
/src/auth/
├── utils/
│   ├── validation.ts    # Used by registration AND password-reset
│   └── crypto.ts        # Used by all capabilities
```

**Solution:** Create separate `auth-utils` capability OR document shared dependencies in each spec.

### Legacy "God Files"

```
/src/auth/
└── auth.ts  # 2000 lines, does everything
```

**Solution:**
```
AI: auth.ts is a large file (2000 lines). I can:
[1] Create one spec (not recommended)
[2] Try to detect logical sections within the file
[3] Analyze specific line ranges you specify
```

---

## Workflow After Reverse Spec

Once you have baseline specs:

```
1. /sdd-reverse src/auth/
   → Creates .specs/specs/auth/ with separate capability specs

2. /sdd-new add-two-factor
   → Creates change spec

3. Change proposal references baseline:
   "Modifies: authentication capability (see baseline/auth/specs/authentication/spec.md)"

4. /sdd-artefact
   → Creates delta specs showing changes from baseline

5. Delta spec:
   ## MODIFIED Requirements
   (Changes to existing authentication behavior)
   
   ## ADDED Requirements
   (New two-factor behavior)
```

---

## Example Output

### Before (Code Only)

```typescript
// src/auth/registration.ts
export async function register(email: string, password: string) {
  if (!isValidEmail(email)) throw new Error('Invalid email');
  if (password.length < 8) throw new Error('Password too short');
  
  const existing = await db.users.findByEmail(email);
  if (existing) throw new Error('Email already registered');
  
  const hashedPassword = await hashPassword(password);
  const user = await db.users.create({
    email,
    passwordHash: hashedPassword
  });
  
  await sendWelcomeEmail(email);
  return { success: true, userId: user.id };
}
```

### After (Baseline Spec)

```
.specs/specs/auth/
├── proposal.md
├── specs/
│   ├── registration/
│   │   └── spec.md
│   └── ...
```

**specs/registration/spec.md:**

```markdown
# Specification: registration

> Baseline spec documenting EXISTING behavior.
> Generated: 2026-02-21

## Overview
Handles new user registration with email and password validation.

## Requirements

### Requirement: User Registration
The system currently allows new users to register with email and password.

#### Scenario: Successful registration
- **WHEN** user provides valid email and password (≥8 chars)
- **THEN** system creates account and sends welcome email

#### Scenario: Invalid email format
- **WHEN** user provides invalid email format
- **THEN** system throws "Invalid email" error

#### Scenario: Password too short
- **WHEN** user provides password shorter than 8 characters
- **THEN** system throws "Password too short" error

#### Scenario: Email already registered
- **WHEN** user provides email that already exists
- **THEN** system throws "Email already registered" error

## Data Models

```typescript
interface RegisterInput {
  email: string;
  password: string;
}

interface RegisterResult {
  success: boolean;
  userId: string;
}
```

## Dependencies

- Internal: `db.users`, `hashPassword`, `sendWelcomeEmail`
- External: none

## Open Questions

- Email validation regex used? (need to check isValidEmail)
- Welcome email retry logic? (not visible)
- Rate limiting? (not implemented)
```

---

## Best Practices

### Do
- Start with capability detection
- Create separate specs per capability
- Use parallel subagents for multiple capabilities
- Keep baseline specs separate from change specs
- Mark uncertain inferences as "inferred" or "unclear"

### Don't
- Create one giant spec for entire module
- Mix baseline specs with change specs
- Skip capability detection for "small" modules
- Spend more time documenting than understanding

---

## Directory Structure

```
.specs/
├── specs/                     # Accumulated specs (single source of truth)
│   └── auth/
│       ├── proposal.md        # Module overview
│       ├── specs/
│       │   ├── authentication/spec.md
│       │   ├── registration/spec.md
│       │   ├── password-reset/spec.md
│       │   └── session-mgmt/spec.md
│       └── design.md          # Architecture overview
├── changes/                   # Active changes in progress
│   └── add-sso/
│       └── specs/authentication/spec.md  # Delta spec
└── archive/                   # Completed changes
    └── 2026-02-21-add-sso/
        └── SUMMARY.md
```

**Note:** No separate "baseline" folder. The main `specs/` folder IS the baseline and gets updated when changes are archived.
