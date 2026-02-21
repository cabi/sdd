---
name: sdd-design
description: Technical design documentation for SDD. Creates architecture documents, documents decisions with rationale, and plans implementation approach. Focuses on the HOW of building features.
license: MIT
compatibility: OpenCode, Claude Code, Cursor, Windsurf
metadata:
  category: methodology
  complexity: intermediate
  author: OpenCode
  version: "1.0.0"
---

# SDD Design Documentation

Create technical design documents that explain HOW to implement the spec.

## When to Write Design Docs

Write design.md when ANY of these apply:

- Cross-cutting change (multiple services/modules)
- New external dependency or integration
- Significant data model changes
- Security implications
- Performance considerations
- Migration complexity
- Ambiguity needing upfront decisions

For simple changes, minimal design is fine.

## Design Document Structure

```markdown
# Design: <spec-name>

## Context
## Goals / Non-Goals
## Architecture
## Decisions
## Components
## Data Models
## API Changes
## Risks / Trade-offs
## Migration Plan
## Open Questions
```

## Section Details

### Context

Background information needed to understand the design:

```markdown
## Context

**Current State:**
<How things work now>

**Problem:**
<Why we need to change>

**Constraints:**
- Technical: <language, framework, infrastructure>
- Business: <deadlines, resources>
- External: <APIs, compliance requirements>

**Stakeholders:**
<Who cares about this change>
```

### Goals / Non-Goals

Explicitly define scope:

```markdown
## Goals / Non-Goals

### Goals
- <What this design achieves>
- <Specific outcomes>

### Non-Goals
- <Explicitly excluded>
- <Future considerations>
```

**Example:**
```markdown
### Goals
- Implement user authentication with email/password
- Support session management
- Enable password reset flow

### Non-Goals
- OAuth integration (future phase)
- Two-factor authentication
- Single sign-on (SSO)
```

### Architecture

High-level system design:

```markdown
## Architecture

<Diagram or description of system structure>

### Flow
1. User submits credentials
2. API validates against auth service
3. Session token generated
4. Token returned to client
```

**Diagram formats:**
- Mermaid diagrams in markdown
- ASCII diagrams for simplicity
- Reference to external diagrams

### Decisions

Document key technical choices:

```markdown
## Decisions

### Decision: Session Storage

**Context:** Need to store user sessions across requests

**Options Considered:**
1. **In-memory** - Pros: Fast / Cons: Lost on restart, doesn't scale
2. **Redis** - Pros: Fast, persistent, scalable / Cons: Additional infrastructure
3. **Database** - Pros: Simple, no new infrastructure / Cons: Slower

**Decision:** Redis

**Rationale:** 
- Need persistence across restarts
- Expect high session volume
- Already have Redis for caching
```

**Decision Template:**
```markdown
### Decision: <title>

**Context:** <situation requiring decision>
**Options Considered:**
1. <Option 1> - Pros: <benefits> / Cons: <drawbacks>
2. <Option 2> - Pros: <benefits> / Cons: <drawbacks>
**Decision:** <chosen option>
**Rationale:** <why selected>
```

### Components

Detail key components:

```markdown
## Components

### AuthService
**Responsibility:** Handle authentication logic
**Interface:** `authenticate(credentials) -> token`
**Dependencies:** UserRepository, TokenService

### TokenService
**Responsibility:** Generate and validate tokens
**Interface:** 
- `generate(userId) -> token`
- `validate(token) -> userId | null`
```

### Data Models

Schema changes and new models:

```markdown
## Data Models

### User (modified)
```typescript
interface User {
  id: string;
  email: string;          // new
  passwordHash: string;   // new
  createdAt: Date;
}
```

### Session (new)
```typescript
interface Session {
  id: string;
  userId: string;
  token: string;
  expiresAt: Date;
  createdAt: Date;
}
```

**Migration:** Add email, passwordHash columns with null constraint
```

### API Changes

Document API modifications:

```markdown
## API Changes

### POST /auth/login (new)
**Request:**
```json
{
  "email": "user@example.com",
  "password": "secret"
}
```

**Response (200):**
```json
{
  "token": "jwt-token",
  "expiresIn": 3600
}
```

**Errors:**
- 401: Invalid credentials
- 400: Missing fields
```

### Risks / Trade-offs

Identify and mitigate risks:

```markdown
## Risks / Trade-offs

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Password database leak | High | Low | Bcrypt hashing, encryption at rest |
| Session token theft | High | Medium | Short expiry, HTTPS only, refresh tokens |
| Brute force attacks | Medium | High | Rate limiting, account lockout |
```

### Migration Plan

Steps to deploy:

```markdown
## Migration Plan

### Phase 1: Add Schema (week 1)
1. Add email, passwordHash columns (nullable)
2. Deploy backward compatible

### Phase 2: Migrate Users (week 2)
1. Add login UI
2. Send password setup emails
3. Users set passwords

### Phase 3: Enforce Auth (week 3)
1. Require authentication for protected routes
2. Remove legacy auth

### Rollback Plan
1. Revert to legacy auth
2. Keep password data for retry
```

### Open Questions

Track unresolved decisions:

```markdown
## Open Questions

- [ ] Should we support email change?
- [ ] What's the password reset email copy?
- [ ] Session timeout duration?

**Resolution needed by:** <date or milestone>
```

## Design Review Checklist

- [ ] All requirements addressed
- [ ] Key decisions documented with rationale
- [ ] Alternatives considered
- [ ] Risks identified and mitigated
- [ ] Migration plan complete
- [ ] Open questions tracked
- [ ] Diagrams where helpful
- [ ] Consistent with existing architecture

## Best Practices

### Keep It Focused
- Design for current requirements
- Don't over-engineer for hypothetical future
- But leave hooks for likely extensions

### Be Explicit
- State assumptions clearly
- Document why, not just what
- Include alternatives considered

### Make It Reviewable
- Use clear structure
- Include diagrams
- Write for future maintainers

### Stay Current
- Update design as you learn
- Document deviations
- Mark decisions as superseded
