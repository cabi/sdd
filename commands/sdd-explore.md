---
name: sdd-explore
description: Explore an idea and create initial context log before formal specification
---

Help me think through an idea or problem and capture the exploration context.

**Usage:** `/sdd-explore [change-name]`

**Use when:**
- Requirements are unclear
- Exploring multiple approaches
- Need to understand the problem better

**Process:**

1. **Determine change name**:
   - If provided: Use `<change-name>`
   - If not: Ask "What would you like to explore?" then:
     - Listen to initial description
     - Extract 2-4 key words
     - Convert to kebab-case
     - Suggest name: "What should we call this change? Suggested: `{generated-name}`"
     - Use user's input or suggestion if they press Enter

2. **Check for existing**:
   - If `.specs/changes/<change-name>/` exists:
     - Ask: "This change already exists. Continue exploration to update context-log?"

3. **Create change structure**:
   ```
   mkdir -p .specs/changes/<change-name>
   ```

4. **Create context-log.md** from template:
   - Read `skills/sdd-spec-create/templates/context-log.md`
   - REPLACE `{{CHANGE_NAME}}` with actual name
   - REPLACE `{{TIMESTAMP}}` with current ISO 8601 timestamp
   - WRITE to `.specs/changes/<change-name>/context-log.md`

5. **Run exploration interview**:
   - Use `sdd-interview` skill
   - Ask clarifying questions:
     - What problem are you solving?
     - Who is this for?
     - What does success look like?
     - What constraints exist?
     - What's the scope (in/out)?
   - Update context-log.md in real-time
   - Populate sections:
     - Goals Identified
     - Constraints Discovered (technical/business/external)
     - Scope Boundaries
     - Options Considered
     - Risks Identified
     - Domain Knowledge

6. **Report completion**

**Output:**
```
✓ Created exploration context for: <change-name>

Created:
  .specs/changes/<change-name>/
  └── context-log.md

Exploration captured:
  - X goals identified
  - Y constraints discovered
  - Z scope boundaries defined
  - N options considered

Context log ready for proposal creation.
Next: Use /sdd-propose <change-name> to create the formal proposal
```

**Don't:**
- Create proposal.md (that's `/sdd-propose`)
- Make final technical decisions (that's design phase)
- Jump to implementation

**Do:**
- Capture Q&A and insights in context-log
- Identify goals, constraints, scope
- Explore options without deciding
- Prepare for creating a good proposal

**Loads skill:** `sdd-interview`
