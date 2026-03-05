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
   - Invoke `sdd-interview` skill
   - Ask structured questions using `question` tool in 3 batches:
     - **Fast-path option**: "Use all recommended defaults?" (skip Batches 1-2 if yes)
     - **Batch 1**: Problem type, primary audience
     - **Batch 2**: Scope areas (multi-select), timeline
     - **Batch 3**: Breaking change risk, success criteria (open-ended)
   - After answers received, update context-log.md:
     - Map problem type → Goals Identified
     - Map audience → Domain Knowledge
     - Map scope areas → Scope Boundaries (In Scope/Out of Scope)
     - Map timeline → Business Constraints
     - Map breaking risk → Risks Identified
     - Map success criteria → Goals Identified (refined)
   - Populate all sections: Goals, Constraints, Scope, Options, Risks, Domain Knowledge
   - Show completion summary and prompt for /sdd-propose

6. **Done** - The sdd-interview skill handles the completion summary and prompts for /sdd-propose

**Output:**
Handled by the `sdd-interview` skill - it will:
- Save context-log.md with all exploration data
- Show completion summary
- Prompt user to run `/sdd-propose <change-name>`

**Don't:**
- Create proposal.md (that's `/sdd-propose`)
- Make final technical decisions (that's design phase)
- Jump to implementation

**Do:**
- Capture Q&A and insights in context-log
- Identify goals, constraints, scope
- Explore options without deciding
- Prepare for creating a good proposal

---

## Valid Next Commands

**After exploration complete:**
- `/sdd-propose <change-name>` - Create formal proposal from context-log

**Do NOT use these as commands (they are skills):**
- ❌ `/sdd-interview` (skill, loaded by this command)
- ❌ `/sdd-spec-create` (skill, loaded by /sdd-propose)

**Loads skill:** `sdd-interview`
