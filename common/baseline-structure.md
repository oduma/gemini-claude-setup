# Baseline Structure Initialization (`baseline-structure.md`)

### 1. Universal Directory Initialization
Using filesystem tools or local shell commands, create the complete app-agnostic folder structure inside the target repository path (`{{args}}`):

- `{{args}}/.claude/agents/`
- `{{args}}/.claude/constitution/`
- `{{args}}/docs/constitution/`
- `{{args}}/docs/plans/`
- `{{args}}/docs/requirements/`
- `{{args}}/docs/specs/assets/`
- `{{args}}/.gemini/`

---

### 2. Copy Shared Baseline Agents
Execute shell commands or tool calls to copy the universal agent files from `~/.gemini/templates/.claude/agents/` to `{{args}}/.claude/agents/`:

**Files to copy:**
1. `back-end-architect.md`
2. `security-officer.md`
3. `senior-NET-developer.md`
4. `solution-architect.agent.md`
5. `SQL-developer.md`
6. `TDD-Specialist.md`

**Execution Command:**
```bash
!{cp ~/.gemini/templates/.claude/agents/back-end-architect.md "{{args}}/.claude/agents/" && cp ~/.gemini/templates/.claude/agents/security-officer.md "{{args}}/.claude/agents/" && cp ~/.gemini/templates/.claude/agents/senior-NET-developer.md "{{args}}/.claude/agents/" && cp ~/.gemini/templates/.claude/agents/solution-architect.agent.md "{{args}}/.claude/agents/" && cp ~/.gemini/templates/.claude/agents/SQL-developer.md "{{args}}/.claude/agents/" && cp ~/.gemini/templates/.claude/agents/TDD-Specialist.md "{{args}}/.claude/agents/" 2>/dev/null || copy "%USERPROFILE%\.gemini\templates\.claude\agents\back-end-architect.md" "{{args}}\.claude\agents\" && copy "%USERPROFILE%\.gemini\templates\.claude\agents\security-officer.md" "{{args}}\.claude\agents\" && copy "%USERPROFILE%\.gemini\templates\.claude\agents\senior-NET-developer.md" "{{args}}\.claude\agents\" && copy "%USERPROFILE%\.gemini\templates\.claude\agents\solution-architect.agent.md" "{{args}}\.claude\agents\" && copy "%USERPROFILE%\.gemini\templates\.claude\agents\SQL-developer.md" "{{args}}\.claude\agents\" && copy "%USERPROFILE%\.gemini\templates\.claude\agents\TDD-Specialist.md" "{{args}}\.claude\agents\"}
```

---

### 3. Copy Shared Baseline Constitutions
Execute shell commands or tool calls to copy the universal constitution files from `~/.gemini/templates/docs/constitution/` to `{{args}}/docs/constitution/`:

**Files to copy:**
1. `coding-principles.md`
2. `DDD-architecture.md`
3. `tdd.md`

**Execution Command:**
```bash
!{cp ~/.gemini/templates/docs/constitution/coding-principles.md "{{args}}/docs/constitution/" && cp ~/.gemini/templates/docs/constitution/DDD-architecture.md "{{args}}/docs/constitution/" && cp ~/.gemini/templates/docs/constitution/tdd.md "{{args}}/docs/constitution/" 2>/dev/null || copy "%USERPROFILE%\.gemini\templates\docs\constitution\coding-principles.md" "{{args}}\docs\constitution\" && copy "%USERPROFILE%\.gemini\templates\docs\constitution\DDD-architecture.md" "{{args}}\docs\constitution\" && copy "%USERPROFILE%\.gemini\templates\docs\constitution\tdd.md" "{{args}}\docs\constitution\"}
```