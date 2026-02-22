# Spec-Driven Development (SDD) for OpenCode

A complete workflow for systematic software development using structured specifications.

## What is SDD?

Spec-Driven Development is a methodology that emphasizes **clarity before code**. By writing specifications before implementation, you:

- Reduce ambiguity and rework
- Create better AI collaboration context
- Build traceable, maintainable software
- Document decisions as you go

## Quick Start

```bash
# Copy the SDD workflow to your OpenCode config
cp -r skill/sdd-* ~/.config/opencode/skill/
cp commands/sdd-*.md ~/.config/opencode/commands/

# Restart OpenCode or start a new session
# Then use:
/sdd-new        # For new features
/sdd-reverse    # For existing code (brownfield)
```

## Installation

### Prerequisites

**Required:**
- OpenCode CLI installed and working

**Vanilla OpenCode includes everything needed:**
- All core tools (Read, Write, Edit, Bash, Glob, Grep)
- Task delegation for parallel agents
- WebFetch for documentation lookup
- Question tool for user interaction
- Skills and commands (built-in feature)

### Install SDD Workflow

```bash
# 1. Create directories if they don't exist
mkdir -p ~/.config/opencode/skill
mkdir -p ~/.config/opencode/commands

# 2. Copy the SDD files
cp -r skill/sdd-* ~/.config/opencode/skill/
cp commands/sdd-*.md ~/.config/opencode/commands/

# 3. Restart OpenCode or start a new session
```

### Verify Installation

```
> /sdd-new
```

If the command is recognized, installation was successful.

---

## Components

### Skills (13)

**Standard Skills (9):**

| Skill | Purpose |
|-------|---------|
| `sdd-spec-create` | Start new specifications |
| `sdd-spec-artefact` | Create next artifact incrementally |
| `sdd-spec-apply` | Implement tasks from spec |
| `sdd-spec-archive` | Archive completed specs |
| `sdd-requirements` | EARS format requirements guide |
| `sdd-design` | Technical design documentation |
| `sdd-tasks` | Task breakdown and sequencing |
| `sdd-reverse` | Extract specs from existing code |
| `sdd-verify` | Verify implementation matches specs |

**SCL-Enhanced Skills (4):**

| Skill | Purpose |
|-------|---------|
| `sdd-memory` | Memory module with JSON schemas for decisions, requirements, citations |
| `sdd-control` | Control module with precondition checking, scope enforcement |
| `sdd-artefact-scl` | SCL-enhanced artifact creation with 5-phase loop |
| `sdd-tasks-scl` | SCL-enhanced task breakdown with memory context generation |

### Commands (17)

**Standard Commands (11):**

| Command | Purpose |
|---------|---------|
| `/sdd-new` | Start a new spec |
| `/sdd-artefact` | Create next artifact |
| `/sdd-apply` | Implement one task at a time |
| `/sdd-apply-group N` | Execute all tasks in group N |
| `/sdd-apply-all` | Execute all groups via subagents |
| `/sdd-verify` | Verify implementation matches spec |
| `/sdd-status` | Check progress |
| `/sdd-archive` | Complete and archive |
| `/sdd-explore` | Think before committing |
| `/sdd-ff` | Fast-forward all artifacts |
| `/sdd-reverse` | Extract spec from existing code |

**SCL-Enhanced Commands (6):**

| Command | Purpose |
|---------|---------|
| `/sdd-init-memory [name]` | Initialize `.memory/` structure for a change |
| `/sdd-artefact-scl` | Create artifacts with memory tracking |
| `/sdd-apply-group-scl N` | Execute group with memory context injection |
| `/sdd-apply-all-scl` | Execute all groups with memory persistence |
| `/sdd-verify-scl` | Verify with memory tracing and gap analysis |
| `/sdd-memory-status [name]` | Inspect memory state for debugging |

### Templates (1)

| Template | Purpose |
|----------|---------|
| `templates/regulation.md` | Epistemic constitution with RFC2119 rules |

---

## Workflows

### Workflow 1: Full Spec (Complex Features)

**Use for:**
- Features requiring > 1 day of work
- Multiple components or integrations
- High-stakes changes
- Team collaboration

**Steps:**

```
┌─────────────────────────────────────────────────────────────────────┐
│  1. EXPLORE (optional)                                              │
│     /sdd-explore                                                    │
│     Think through the idea, clarify requirements                    │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  2. CREATE SPEC                                                     │
│     /sdd-new                                                        │
│     Creates proposal.md with WHY, WHAT, scope                       │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  3. DEVELOP ARTIFACTS                                               │
│     Option A: Incremental                                           │
│       /sdd-artefact  → creates specs                                │
│       /sdd-artefact  → creates design                               │
│       /sdd-artefact  → creates tasks                                │
│                                                                     │
│     Option B: Fast-forward                                          │
│       /sdd-ff  → creates all at once                                │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  4. IMPLEMENT                                                       │
│     /sdd-apply                                                      │
│     Repeatedly: find task → implement → mark complete               │
│     Update specs if gaps found                                      │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  5. ARCHIVE                                                         │
│     /sdd-archive                                                    │
│     Verify completion, create summary, move to archive              │
└─────────────────────────────────────────────────────────────────────┘
```

#### Full Spec Example: User Authentication

**Step 1: Explore (Optional)**
```
You: /sdd-explore

AI: What would you like to explore?

You: I need to add user authentication to my app. Users should be able
to sign up, log in, and log out. I'm thinking about using JWT tokens.

AI: Let me clarify a few things:
1. What's your current tech stack?
2. Do you need password reset?
3. Should sessions persist across browser restarts?
4. Any OAuth requirements (Google, GitHub)?

You: TypeScript, Express, PostgreSQL. Yes to password reset. 
Yes to persistent sessions. No OAuth for now.

AI: Based on that, here's what I understand:
- Email/password authentication
- JWT with refresh tokens for persistence
- Password reset via email
- PostgreSQL for user storage

Key decisions to make:
- Token expiry duration?
- Password requirements?
- Rate limiting for login attempts?

Ready for /sdd-new when you are.
```

**Step 2: Create Spec**
```
You: /sdd-new

AI: What would you like to build?

You: User authentication with email/password, including signup, login,
logout, and password reset. Using TypeScript, Express, PostgreSQL.

AI: Let me check for existing specs...
No conflicts found.

Creating change at .specs/changes/user-authentication/

[Creates proposal.md]

# Proposal: user-authentication

## Why
Users need secure access to personalized features. Currently anyone can 
access all features without identification.

## What Changes
- New user registration with email/password
- Login/logout functionality
- Password reset via email
- Session management with JWT

## Capabilities

### New Capabilities
- `user-auth`: Email/password authentication
- `password-reset`: Self-service password recovery

## Impact
- New: /auth/* API endpoints
- New: users table in PostgreSQL
- New: email service integration
- Modified: Routes requiring authentication

## Scope

### In Scope
- Email/password authentication
- JWT-based sessions
- Password reset flow

### Out of Scope
- OAuth providers (future)
- Two-factor authentication
- Single sign-on

## Status
- [ ] Requirements: pending
- [ ] Design: pending
- [ ] Tasks: pending

✓ Created change at .specs/changes/user-authentication/
✓ Proposal document created

Next: Use /sdd-artefact to create requirements
```

**Step 3: Develop Artifacts**
```
You: /sdd-artefact

AI: Checking artifact status...
  proposal: ✓ DONE
  specs:    READY (proposal complete)
  design:   READY (proposal complete)
  tasks:    BLOCKED (needs specs + design)

Creating specs next (highest priority ready artifact).

[Reads proposal for capabilities]

Creating .specs/changes/user-authentication/specs/user-auth/spec.md:

# Specification: user-auth

## ADDED Requirements

### Requirement: User Registration
The system SHALL allow new users to create accounts with email and password.

#### Scenario: Successful registration
- **WHEN** user submits valid email and password
- **THEN** system SHALL create new account and return success

#### Scenario: Duplicate email
- **WHEN** user submits email that already exists
- **THEN** system SHALL return error "Email already registered"

#### Scenario: Weak password
- **WHEN** user submits password shorter than 8 characters
- **THEN** system SHALL return error "Password must be at least 8 characters"

### Requirement: User Login
The system SHALL authenticate users with email and password.

#### Scenario: Successful login
- **WHEN** user submits correct email and password
- **THEN** system SHALL return JWT token and refresh token

#### Scenario: Invalid credentials
- **WHEN** user submits incorrect email or password
- **THEN** system SHALL return error "Invalid credentials"

#### Scenario: Rate limited
- **WHEN** user fails login 5 times within 15 minutes
- **THEN** system SHALL return error "Too many attempts, try again later"

### Requirement: Session Management
The system SHALL maintain user sessions with JWT tokens.

#### Scenario: Token validation
- **WHEN** request includes valid JWT token
- **THEN** system SHALL allow access to protected resources

#### Scenario: Token expiry
- **WHEN** JWT token is expired
- **THEN** system SHALL return 401 and prompt re-authentication

#### Scenario: Token refresh
- **WHEN** user submits valid refresh token
- **THEN** system SHALL issue new JWT token

✓ Created: specs/user-auth/spec.md
✓ Updated proposal status

Artifact Status:
  proposal: ✓ DONE
  specs:    ✓ DONE
  design:   READY
  tasks:    BLOCKED (waiting for design)

Next: Use /sdd-artefact to create design
```

```
You: /sdd-artefact

AI: Creating design next.

[Reads proposal and specs for context]

Creating .specs/changes/user-authentication/design.md:

# Design: user-authentication

## Context
Express.js API with PostgreSQL database. Currently no authentication.
Need to add secure user authentication.

## Goals / Non-Goals

### Goals
- Secure email/password authentication
- JWT-based session management
- Password reset via email

### Non-Goals
- OAuth providers
- Two-factor authentication
- Enterprise SSO

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Client    │────▶│  Express    │────▶│ PostgreSQL  │
│             │     │  /auth/*    │     │   users     │
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   Email     │
                    │   Service   │
                    └─────────────┘
```

## Decisions

### Decision: Password Hashing

**Context:** Need secure password storage

**Options Considered:**
1. **bcrypt** - Pros: Battle-tested, built-in salt / Cons: CPU intensive
2. **argon2** - Pros: Modern, GPU-resistant / Cons: Native dependency
3. **scrypt** - Pros: Built-in Node.js / Cons: More configuration

**Decision:** bcrypt

**Rationale:** Most widely used, well-understood, good enough security

### Decision: Token Strategy

**Context:** Session management approach

**Options Considered:**
1. **JWT only** - Pros: Stateless / Cons: Can't revoke, long expiry risky
2. **JWT + Refresh** - Pros: Stateless, revocable refresh / Cons: More complex
3. **Session DB** - Pros: Full control, revocable / Cons: DB hit every request

**Decision:** JWT + Refresh tokens

**Rationale:** Balance of statelessness and security. Refresh tokens 
stored in DB, can be revoked. JWT short-lived (15 min).

## Components

### POST /auth/register
- Input: email, password
- Output: success message
- Hash password with bcrypt (cost 12)
- Insert user into database

### POST /auth/login
- Input: email, password
- Output: accessToken, refreshToken
- Verify password against hash
- Generate JWT (15 min expiry)
- Generate refresh token (7 days)
- Store refresh token hash in DB

### POST /auth/refresh
- Input: refreshToken
- Output: new accessToken
- Validate refresh token against DB
- Issue new JWT

### POST /auth/logout
- Input: refreshToken
- Output: success
- Invalidate refresh token in DB

### POST /auth/password-reset/request
- Input: email
- Output: success (always)
- Generate reset token (1 hour expiry)
- Send email with reset link

### POST /auth/password-reset/confirm
- Input: token, newPassword
- Output: success
- Validate token
- Update password hash
- Invalidate all refresh tokens

## Data Models

```typescript
// users table
interface User {
  id: string;           // UUID
  email: string;        // unique, indexed
  passwordHash: string; // bcrypt hash
  createdAt: Date;
  updatedAt: Date;
}

// refresh_tokens table
interface RefreshToken {
  id: string;           // UUID
  userId: string;       // FK to users
  tokenHash: string;    // SHA-256 of token
  expiresAt: Date;
  createdAt: Date;
}

// password_reset_tokens table
interface PasswordResetToken {
  id: string;           // UUID
  userId: string;       // FK to users
  tokenHash: string;    // SHA-256 of token
  expiresAt: Date;      // 1 hour
  usedAt: Date | null;
}
```

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| Password breach | bcrypt + pepper from env |
| Token theft | Short JWT expiry + HTTPS only |
| Brute force | Rate limiting (5 per 15 min) |
| Email deliverability | Use established provider (SendGrid) |

## Migration Plan

1. Create database tables (users, refresh_tokens, password_reset_tokens)
2. Add /auth/* routes (non-breaking, no auth required)
3. Add authentication middleware
4. Protect existing routes (breaking change)
5. Deploy with feature flag if needed

## Open Questions
- [ ] Email provider: SendGrid vs AWS SES?
- [ ] Rate limit storage: Redis vs in-memory?

✓ Created: design.md
✓ Updated proposal status

Next: Use /sdd-artefact to create tasks
```

```
You: /sdd-artefact

AI: Creating tasks next.

[Reads proposal, specs, and design]

Creating .specs/changes/user-authentication/tasks.md:

# Tasks: user-authentication

## 1. Setup

- [ ] 1.1 Create auth module directory structure
  - Create src/auth/, src/auth/routes/, src/auth/middleware/, src/auth/services/
  - _Requirements: setup_

- [ ] 1.2 Install dependencies
  - bcrypt, jsonwebtoken, @types/bcrypt, @types/jsonwebtoken
  - _Requirements: setup_

- [ ] 1.3 Create database migrations
  - Create migrations for users, refresh_tokens, password_reset_tokens tables
  - _Requirements: user-storage_

## 2. Core Services

- [ ] 2.1 Implement password hashing service
  - Create src/auth/services/PasswordService.ts
  - hash(password) → hash, verify(password, hash) → boolean
  - Use bcrypt with cost 12
  - _Requirements: password-security_

- [ ] 2.2 Implement token service
  - Create src/auth/services/TokenService.ts
  - generateAccessToken(userId) → JWT
  - generateRefreshToken(userId) → token
  - verifyAccessToken(token) → payload | null
  - _Requirements: session-management_

- [ ] 2.3 Implement user service
  - Create src/auth/services/UserService.ts
  - create(email, password) → User
  - findByEmail(email) → User | null
  - updatePassword(userId, newPassword) → void
  - _Requirements: user-registration, user-login_

## 3. API Routes

- [ ] 3.1 Create registration route
  - POST /auth/register
  - Validate email format, password strength
  - Return 201 on success, 409 on duplicate email
  - _Requirements: user-registration_

- [ ] 3.2 Create login route
  - POST /auth/login
  - Return accessToken and refreshToken
  - Return 401 on invalid credentials
  - _Requirements: user-login_

- [ ] 3.3 Create refresh route
  - POST /auth/refresh
  - Validate refresh token
  - Return new accessToken
  - _Requirements: session-management_

- [ ] 3.4 Create logout route
  - POST /auth/logout
  - Invalidate refresh token
  - _Requirements: session-management_

- [ ] 3.5 Create password reset request route
  - POST /auth/password-reset/request
  - Generate reset token, send email
  - Always return success (security)
  - _Requirements: password-reset_

- [ ] 3.6 Create password reset confirm route
  - POST /auth/password-reset/confirm
  - Validate reset token, update password
  - Invalidate all refresh tokens
  - _Requirements: password-reset_

## 4. Middleware

- [ ] 4.1 Create authentication middleware
  - Extract Bearer token from Authorization header
  - Validate JWT, attach user to request
  - Return 401 on missing/invalid token
  - _Requirements: session-management_

- [ ] 4.2 Create rate limiting middleware
  - Track login attempts by IP/email
  - Block after 5 failures in 15 minutes
  - _Requirements: rate-limiting_

## 5. Testing

- [ ] 5.1 Unit tests for PasswordService
  - Test hash, verify, incorrect password
  - _Requirements: password-security_

- [ ] 5.2 Unit tests for TokenService
  - Test generation, validation, expiry
  - _Requirements: session-management_

- [ ] 5.3 Integration tests for auth routes
  - Test register, login, refresh, logout flow
  - Test password reset flow
  - _Requirements: user-registration, user-login, password-reset_

## 6. Documentation

- [ ] 6.1 Document API endpoints
  - Add OpenAPI/Swagger documentation
  - Include request/response examples
  - _Requirements: documentation_

✓ Created: tasks.md
✓ Updated proposal status

Artifact Status:
  proposal: ✓ DONE
  specs:    ✓ DONE
  design:   ✓ DONE
  tasks:    ✓ DONE

All planning artifacts complete!
Ready for /sdd-apply to start implementation.
```

**Step 4: Implement**
```
You: /sdd-apply

AI: Loading spec context...
- Read proposal.md ✓
- Read specs/user-auth/spec.md ✓
- Read design.md ✓
- Read tasks.md ✓

Finding next task...

Next task: 1.1 Create auth module directory structure
Requirements: setup

Creating directories:
- src/auth/
- src/auth/routes/
- src/auth/middleware/
- src/auth/services/

✓ Completed: 1.1 Create auth module directory structure

Progress: 1/19 tasks (5%)

Continue with /sdd-apply for next task.
```

```
You: /sdd-apply

AI: Next task: 1.2 Install dependencies
Requirements: setup

Running: npm install bcrypt jsonwebtoken
Running: npm install -D @types/bcrypt @types/jsonwebtoken

✓ Completed: 1.2 Install dependencies

Progress: 2/19 tasks (11%)
```

[... continues for all tasks ...]

**Step 5: Archive**
```
You: /sdd-archive

AI: Checking completion status...
Tasks: 19/19 (100%) ✓

All tasks complete. Creating summary...

# Summary: user-authentication

## What Was Delivered
Complete user authentication system with email/password, JWT sessions,
and password reset functionality.

## Requirements Implemented

| Requirement | Status |
|-------------|--------|
| User Registration | ✓ Done |
| User Login | ✓ Done |
| Session Management | ✓ Done |
| Password Reset | ✓ Done |

## Key Decisions
- bcrypt for password hashing (cost 12)
- JWT + refresh tokens for session management
- 15 min JWT expiry, 7 day refresh token expiry
- Rate limiting: 5 attempts per 15 minutes

## Files Changed
- Created: src/auth/* (all auth module files)
- Created: migrations/* (database migrations)
- Modified: src/index.ts (added auth routes)

Ready to archive? [y/N]

You: y

✓ Created SUMMARY.md
✓ Moved to .specs/archive/user-authentication/

Spec archived successfully.
```

---

### Workflow 2: Micro Spec (Simple Changes)

**Use for:**
- Changes < 1 day of work
- Single component modifications
- Bug fixes with known solution
- Well-understood patterns

**Simplified Structure:**
```
.specs/changes/<name>/
├── proposal.md    # Combines WHY, WHAT, and HOW
└── tasks.md       # Implementation checklist
```

**Steps:**

```
┌─────────────────────────────────────────────────────────────────────┐
│  1. CREATE MICRO SPEC                                               │
│     /sdd-new                                                        │
│     When asked, indicate this is a small change                     │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  2. IMPLEMENT DIRECTLY                                              │
│     /sdd-apply                                                      │
│     Skip full artifact development                                  │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  3. ARCHIVE                                                         │
│     /sdd-archive                                                    │
└─────────────────────────────────────────────────────────────────────┘
```

#### Micro Spec Example: Add Loading Spinner

```
You: /sdd-new

AI: What would you like to build?

You: Add a loading spinner to the submit button while forms are submitting.
This is a small change.

AI: Creating micro spec (simple change)...

Creating .specs/changes/loading-spinner/proposal.md:

# Proposal: loading-spinner

## Why
Users need visual feedback when forms are submitting. Currently no 
indication that action is in progress.

## What Changes
- Add loading state to form submit buttons
- Show spinner during submission
- Disable button while loading

## Implementation
- Add `isLoading` state to form components
- Add spinner component
- Disable submit button when isLoading is true

## Status
- [ ] Tasks: pending

---

Creating .specs/changes/loading-spinner/tasks.md:

# Tasks: loading-spinner

## 1. Implementation

- [ ] 1.1 Create Spinner component
  - Create src/components/Spinner.tsx
  - Use CSS animation for rotation

- [ ] 1.2 Add loading state to form hooks
  - Update useFormSubmission hook
  - Return isLoading state

- [ ] 1.3 Update submit buttons
  - Show Spinner when isLoading
  - Disable button when isLoading

✓ Created micro spec at .specs/changes/loading-spinner/

This is a micro spec - skipping separate specs/design documents.
Ready for /sdd-apply
```

```
You: /sdd-apply

AI: Next task: 1.1 Create Spinner component

Creating src/components/Spinner.tsx:

```tsx
export function Spinner({ size = 'md' }: { size?: 'sm' | 'md' | 'lg' }) {
  const sizeClass = {
    sm: 'w-4 h-4',
    md: 'w-6 h-6',
    lg: 'w-8 h-8',
  }[size];

  return (
    <svg
      className={`animate-spin ${sizeClass}`}
      viewBox="0 0 24 24"
    >
      <circle
        className="opacity-25"
        cx="12"
        cy="12"
        r="10"
        stroke="currentColor"
        strokeWidth="4"
        fill="none"
      />
      <path
        className="opacity-75"
        fill="currentColor"
        d="M4 12a8 8 0 018-8V0C5.4 0 0 5.4 0 12h4z"
      />
    </svg>
  );
}
```

✓ Completed: 1.1 Create Spinner component

Progress: 1/3 tasks (33%)
```

```
You: /sdd-apply

AI: Next task: 1.2 Add loading state to form hooks

Updating src/hooks/useFormSubmission.ts:

```tsx
export function useFormSubmission<T>(
  onSubmit: (data: T) => Promise<void>
) {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  const handleSubmit = async (data: T) => {
    setIsLoading(true);
    setError(null);
    try {
      await onSubmit(data);
    } catch (e) {
      setError(e.message);
    } finally {
      setIsLoading(false);
    }
  };

  return { handleSubmit, isLoading, error };
}
```

✓ Completed: 1.2 Add loading state to form hooks

Progress: 2/3 tasks (67%)
```

```
You: /sdd-apply

AI: Next task: 1.3 Update submit buttons

Updating src/components/Form.tsx:

```tsx
function SubmitButton() {
  const { isLoading } = useFormSubmission();

  return (
    <button
      type="submit"
      disabled={isLoading}
      className="..."
    >
      {isLoading ? (
        <>
          <Spinner size="sm" />
          <span>Submitting...</span>
        </>
      ) : (
        'Submit'
      )}
    </button>
  );
}
```

✓ Completed: 1.3 Update submit buttons

Progress: 3/3 tasks (100%)

All tasks complete!
```

```
You: /sdd-archive

AI: Checking completion status...
Tasks: 3/3 (100%) ✓

✓ Created SUMMARY.md
✓ Archived to .specs/archive/loading-spinner/
```

---

## When to Use Which Workflow

| Aspect | Full Spec | Micro Spec |
|--------|-----------|------------|
| **Duration** | > 1 day | < 1 day |
| **Complexity** | Multiple components | Single component |
| **Risk** | High (breaking changes) | Low (additive) |
| **Artifacts** | proposal, specs, design, tasks | proposal, tasks |
| **Review** | Multiple checkpoints | Quick review |

---

### Workflow 3: SCL-Enhanced (RECOMMENDED)

**Use for:**
- Complex features requiring high reliability
- Multi-agent task execution
- Projects requiring full traceability
- When subagent context isolation is a problem

**What is SCL?**

SCL (Structured Cognitive Loop) is an architectural framework that addresses a fundamental limitation: **subagents operate in isolated contexts** and cannot access decisions made in prior groups.

**SCL solves this with:**
1. **Memory Persistence** - External `.memory/` directory stores state across cycles
2. **Evidential Grounding** - Every claim MUST cite a source (RFC2119)
3. **Normative Control** - Control module validates before execution
4. **Scope Enforcement** - Subagents constrained to allowed files

**Installation (SCL-enhanced):**
```bash
# Standard SDD installation
cp -r skill/sdd-* ~/.config/opencode/skill/
cp commands/sdd-*.md ~/.config/opencode/commands/

# Copy SCL-specific templates
cp -r templates/ ~/.config/opencode/templates/
```

**SCL Commands:**

| Command | Purpose |
|---------|---------|
| `/sdd-init-memory [name]` | Initialize memory structure for change |
| `/sdd-artefact-scl` | Create artifact with memory tracking |
| `/sdd-apply-group-scl N` | Execute group with memory context |
| `/sdd-apply-all-scl` | Execute all groups with persistence |
| `/sdd-verify-scl` | Verify with memory tracing |
| `/sdd-memory-status [name]` | Inspect memory state |

**SCL Directory Structure:**
```
.specs/changes/<change-name>/
├── proposal.md
├── specs/<capability>/spec.md
├── design.md
├── tasks.md
├── .memory/                    # SCL Memory Module
│   ├── decisions.json          # All decisions with evidence
│   ├── requirements.json       # Requirement index
│   ├── citations.json          # Citation graph
│   ├── control-log.json        # Validation checkpoints
│   └── episodes.json           # Cycle-by-cycle history
└── regulation.md               # Epistemic Constitution
```

**SCL Workflow:**

```
┌─────────────────────────────────────────────────────────────────────┐
│  1. INITIALIZE SCL MEMORY                                           │
│     /sdd-init-memory <change-name>                                  │
│     Creates .memory/ directory with JSON schemas                    │
│     Creates regulation.md (epistemic constitution)                  │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  2. CREATE SPEC (SCL-Enhanced)                                      │
│     /sdd-new                                                        │
│     /sdd-artefact-scl                                               │
│     Each artifact created with:                                      │
│     - Evidence citations (MUST cite sources)                        │
│     - Memory updates (decisions, requirements extracted)            │
│     - Control validation (citations verified)                       │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  3. IMPLEMENT (SCL-Enhanced)                                        │
│     /sdd-apply-group-scl N   OR   /sdd-apply-all-scl                │
│                                                                     │
│     Before dispatch:                                                │
│     - Load memory context (decisions, requirements)                 │
│     - Verify preconditions                                          │
│     - Generate scope constraints                                    │
│                                                                     │
│     Subagent receives:                                              │
│     - Full memory context from prior groups                         │
│     - Allowed/blocked file lists                                    │
│     - Required citations                                            │
│                                                                     │
│     After completion:                                               │
│     - Verify scope compliance                                       │
│     - Update memory (status, citations)                             │
│     - Log episode                                                   │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  4. VERIFY (SCL-Enhanced)                                           │
│     /sdd-verify-scl                                                 │
│     - Requirement verification with memory tracing                  │
│     - Decision compliance check                                     │
│     - Citation integrity verification                               │
│     - Memory consistency check                                      │
│     - Goal fidelity score                                           │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────────┐
│  5. ARCHIVE                                                         │
│     /sdd-archive                                                    │
│     Memory preserved in archive for future reference                │
└─────────────────────────────────────────────────────────────────────┘
```

#### SCL-Enhanced Example: User Authentication

**Step 1: Initialize Memory**
```
You: /sdd-init-memory user-authentication

AI: ✓ Initialized memory for: user-authentication

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
```

**Step 2: Create Artifacts with Memory Tracking**
```
You: /sdd-artefact-scl

AI: ✓ Retrieved: Memory context loaded (0 decisions, 0 requirements)
    ✓ Cognition: Generated specs/user-auth/spec.md with 5 requirements
    ✓ Control: All 12 citations verified, regulation compliant
    ✓ Action: Written to specs/user-auth/spec.md
    ✓ Memory: Updated decisions.json, requirements.json, citations.json

Artifact Status:
  proposal: DONE
  specs: DONE ← just completed
  design: READY
  tasks: BLOCKED (waiting for design)

Memory State:
  decisions: 0
  requirements: 5 (extracted)
  citations: 12 (recorded)

Next: Use /sdd-artefact-scl to create design
```

**Step 3: Execute with Memory Context**
```
You: /sdd-apply-group-scl 2

AI: ✓ Retrieved: Memory context (3 decisions, 5 requirements)
    ✓ Control: Preconditions verified
    ✓ Dispatch: Subagent for Group 2 with scope constraints

    Injecting memory context into subagent:
    - Decisions: DEC-001 (JWT), DEC-002 (bcrypt cost 12)
    - Requirements: AUTH-001 through AUTH-005
    - Prior outcomes: Group 1 created src/auth/ structure
    - Constraints: allowed_files = ["src/auth/**/*.ts"]

    [Subagent executes with full context...]

    ✓ Completion Signal: "GROUP 2 COMPLETE"
    ✓ Scope Verified: All files in allowed list
    ✓ Tasks Verified: 5/5 complete
    ✓ Tests: All passing

Memory Updates:
  requirements.json: AUTH-001, AUTH-002 → implemented
  citations.json: +8 new citations
  episodes.json: +1 episode (Group 2 outcome)

Ready for next group. Use /sdd-apply-group-scl 3
```

**Step 4: Verify with Memory Tracing**
```
You: /sdd-verify-scl

AI: ═══════════════════════════════════════════════════════════
    SCL Verification Report: user-authentication
    ═══════════════════════════════════════════════════════════

    ## Summary

    Goal Fidelity: 0.92 (EXCELLENT)
    - Requirements: 11/12 implemented (92%)
    - Tests: 18/20 passing (90%)
    - Citations: 45/47 valid (96%)
    - Memory: Consistent

    ## Citation Integrity

    Valid: 45/47 (96%)
    Broken:
      - CIT-012: design.md#L999 (line does not exist)
      - CIT-027: specs/auth/spec.md#L200 (section removed)

    ## Recommendation

    ✓ READY FOR ARCHIVE

    All critical requirements implemented and verified.
    Minor citation issues can be addressed in future iteration.

    ═══════════════════════════════════════════════════════════
```

**SCL-Enhanced Task Format:**

Tasks in SCL mode include additional metadata:

```markdown
- [ ] 2.1 Implement password hashing utility
  - _Requirements: AUTH-001 (per specs/auth/spec.md#L23)_
  - _Evidence: design.md#decision-password-hashing (DEC-003)_
  - _Precondition: Task 1.2 complete (bcrypt installed)_
  - _Creates: src/auth/utils/hash.ts_
  - _Validation:_
    - Unit tests pass
    - Bcrypt cost factor = 12 (per DEC-003)
    - Export signature matches interface
  - _Memory Write:_
    - `requirements.json#AUTH-001.status ← "implemented"`
    - `citations.json ← hash.ts implements AUTH-001`
```

**Regulation.md (Epistemic Constitution):**

Every SCL-enhanced change includes a `regulation.md`:

```markdown
# Epistemic Constitution: user-authentication

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

**Expected Benefits (from SCL research):**

| Metric | Standard | SCL-Enhanced |
|--------|----------|--------------|
| Task Success Rate | ~70% | ~86% |
| Redundant Actions | High | ~50% reduction |
| Memory Fidelity | Low | High (persistent) |
| Hallucination Rate | Moderate | ~3x reduction |
| Error Localization | Poor | Good (cycle-level logs) |

**When to Use SCL:**

| Aspect | Use SCL | Standard OK |
|--------|---------|-------------|
| Subagent execution | ✓ Yes | - |
| Multi-group tasks | ✓ Yes | - |
| Need traceability | ✓ Yes | - |
| Simple single-task | - | ✓ OK |
| Quick prototype | - | ✓ OK |

---

## Brownfield Workflow (Existing Projects)

For projects without specs, use `/sdd-reverse` to extract specs from code:

```
You: /sdd-reverse src/auth/

AI: Scanning src/auth/...

Detected 4 capabilities:
┌─────────────────┬─────────────────────┐
│ Capability      │ Files               │
├─────────────────┼─────────────────────┤
│ authentication  │ login.ts, logout.ts │
│ registration    │ registration.ts     │
│ password-reset  │ password-reset.ts   │
│ session-mgmt    │ session.ts          │
└─────────────────┴─────────────────────┘

Create specs:
[1] Separate specs per capability (recommended)
[2] Single combined spec

You: 1

AI: ✓ Created .specs/specs/auth/
    ├── proposal.md
    └── specs/
        ├── authentication/spec.md
        ├── registration/spec.md
        ├── password-reset/spec.md
        └── session-mgmt/spec.md
```

**Key principle:** Create separate specs per detected capability (not one giant spec). This:
- Matches the SDD workflow structure
- Enables proper delta specs for changes
- Keeps specs focused and maintainable

**Brownfield workflow:**
```
1. /sdd-reverse src/auth/     → Detect capabilities, create specs in .specs/specs/
2. /sdd-new                   → Create change in .specs/changes/
3. Change proposal references specific existing capability
4. /sdd-artefact              → Creates delta specs (ADDED/MODIFIED/REMOVED)
5. /sdd-apply                 → Implement changes
6. /sdd-archive               → Merge deltas into .specs/specs/
```

**Directory structure:**
```
.specs/
├── specs/            # Accumulated specs (single source of truth)
│   └── auth/
│       ├── proposal.md
│       └── specs/
│           ├── authentication/spec.md
│           ├── registration/spec.md
│           └── ...
├── changes/          # Active changes
│   └── add-two-factor/
│       └── specs/authentication/spec.md  # Delta spec
└── archive/          # Completed changes (history)
```

---

## Tips

### During Development

1. **Use `/sdd-explore` first** for complex or unclear features
2. **Review artifacts** between phases with `/sdd-status`
3. **Update specs** if implementation reveals gaps (don't workaround)
4. **One task at a time** with `/sdd-apply`

### Common Commands

```bash
# Start new feature
/sdd-new

# Check where you are
/sdd-status

# Continue developing spec
/sdd-artefact

# Implement (three modes)
/sdd-apply              # One task at a time (high control)
/sdd-apply-group 2      # All tasks in group 2 (batched)
/sdd-apply-all          # All groups via subagents (fastest)

# Think before committing
/sdd-explore

# Create everything at once (when clear)
/sdd-ff

# Done
/sdd-archive
```

### Execution Modes for Implementation

| Mode | Command | Control | Speed | Best For |
|------|---------|---------|-------|----------|
| Single | `/sdd-apply` | High | Slow | Learning, risky changes |
| Group | `/sdd-apply-group N` | Medium | Medium | Batching related work |
| All | `/sdd-apply-all` | Low | Fast | Large projects, well-defined |

**How groups work:**

Tasks are organized into groups with metadata:

```markdown
## 1. Setup
_Meta: sequential, foundation_

## 2. Core Services
_Meta: parallel-safe, depends on: 1_

## 3. API Routes
_Meta: sequential, depends on: 2_
```

When using `/sdd-apply-all`:
1. Groups execute in dependency order
2. Each group dispatched to subagent with strict scope
3. Subagent cannot exceed its group's tasks
4. Verification after each group completes

### Verification

Before archiving, verify implementation matches spec:

```
/sdd-verify

AI: Verifying change: add-sso

┌──────────────────────────────────────────────────────────────┐
│ VERIFICATION REPORT                                          │
├──────────────────────────────────────────────────────────────┤
│ Requirements: 4 IMPLEMENTED, 1 PARTIAL, 0 MISSING            │
│ Scenarios: 10 COVERED, 2 MISSING                             │
│                                                              │
│ ✓ IMPLEMENTED: user-login, token-generation, logout         │
│ ⚠ PARTIAL: rate-limiting (missing IP blocking)              │
│                                                              │
│ Tests: 8/10 scenarios tested                                 │
└──────────────────────────────────────────────────────────────┘

Overall: 85% implemented

[1] Show details
[2] Continue anyway (acknowledge gaps)
[3] Fix issues first
```

**Verification commands:**
- `/sdd-verify` - Verify current change
- `/sdd-verify --spec auth` - Verify specific spec
- `/sdd-verify --all` - Check all specs for drift

**When to verify:**
- Before archiving (automatic prompt)
- After implementing features
- Periodic drift checks on existing specs

---

## Troubleshooting

### Command Not Found

```bash
# Verify installation
ls ~/.config/opencode/commands/sdd-*.md

# If missing, reinstall
cp commands/sdd-*.md ~/.config/opencode/commands/
```

### Skills Not Loading

```bash
# Verify installation
ls ~/.config/opencode/skill/sdd-*/SKILL.md

# If missing, reinstall
cp -r skill/sdd-* ~/.config/opencode/skill/
```

### Spec Directory Not Created

```bash
# Create manually
mkdir -p .specs/specs .specs/changes .specs/archive
```

### Multiple Active Specs

When you have multiple specs, `/sdd-artefact` and `/sdd-apply` will ask which one to work on.

---

## Philosophy

### Clarity Before Code

Ambiguity in requirements leads to wasted implementation effort. By writing specs first:

- You understand the problem before solving it
- AI has better context for assistance
- Future maintainers understand decisions

### Iterative Refinement

Specs aren't perfect on first pass. The workflow encourages:

- Refinement at each phase
- Updates when implementation reveals gaps
- Living documentation that evolves

### Traceability

Every task links to requirements. This means:

- You know why code exists
- Changes can assess impact
- Documentation stays current

---

## Quick Reference

**Installation:**
```bash
cp -r skill/sdd-* ~/.config/opencode/skill/
cp commands/sdd-*.md ~/.config/opencode/commands/
```

**Workflow:**
```
# New features
/sdd-new           → Start
/sdd-artefact      → Build spec
/sdd-apply         → Implement (one task)
/sdd-apply-group N → Implement (group N)
/sdd-apply-all     → Implement (all groups)
/sdd-verify        → Verify implementation matches spec
/sdd-archive       → Finish (merge to specs/)

# Brownfield/existing code
/sdd-reverse       → Extract specs from code
/sdd-new           → Create change spec (references existing)
/sdd-verify --all  → Check for spec drift
```

---

## License

MIT
