# Skill Registration Pattern

## Overview

This document defines the MANDATORY pattern for registering agents, workflows, tasks, and tools as Claude Code skills.

## Critical Pattern: Thin Wrapper Files

**Rule:** Files in `.claude/commands/` MUST be thin wrapper files that LOAD implementations from `_personal-automation/`. They should NEVER contain the actual implementation.

### ❌ WRONG: Symbolic Links
```bash
# DO NOT DO THIS
ln -s ../../../../../_personal-automation/domains/finance/agent.md .claude/commands/personal-automation/domains/finance/sofia.md
```

### ❌ WRONG: Inline Implementation
```markdown
<!-- DO NOT PUT FULL AGENT LOGIC IN .claude/commands/ -->
---
name: 'sofia'
---

<agent id="sofia">
  <persona>...</persona>
  <menu>...</menu>
  <!-- hundreds of lines of agent implementation -->
</agent>
```

### ✅ CORRECT: Agent Activation Wrapper

**Pattern for Agents:**
```markdown
---
name: 'agent-id'
description: 'Brief description of agent role'
---

You must fully embody this agent's persona and follow all activation instructions exactly as specified. NEVER break character until given an exit command.

<agent-activation CRITICAL="TRUE">
1. LOAD the FULL agent file from @_personal-automation/path/to/agent.md
2. READ its entire contents - this contains the complete agent persona, menu, and instructions
3. Execute ALL activation steps exactly as written in the agent file
4. Follow the agent's persona and menu system precisely
5. Stay in character throughout the session
</agent-activation>
```

**Pattern for Workflows/Tasks/Tools:**
```markdown
---
description: 'Brief description of what this does'
---

# Task/Workflow/Tool Name

LOAD and execute the [task|workflow|tool] at: _personal-automation/path/to/file.xml

Follow all instructions in the file exactly as written.
```

## Architecture Principles

### Single Source of Truth
- **Implementation lives ONLY in `_personal-automation/`**
- **`.claude/commands/` contains ONLY registration wrappers**
- Changes to logic happen in ONE place (the implementation file)

### Two-Layer Architecture

```
Layer 1: .claude/commands/personal-automation/
├── Purpose: Claude Code skill registration
├── Content: Thin wrapper files with LOAD instructions
└── Pattern: <agent-activation> or LOAD commands

Layer 2: _personal-automation/
├── Purpose: Actual implementation
├── Content: Full agent personas, workflow logic, tasks, tools
└── Pattern: Complete XML/MD files with all logic
```

### Benefits

1. **Discoverability**: Skills appear in Claude Code's skill system
2. **Maintainability**: Update implementation in one place
3. **Consistency**: All skills follow the same activation pattern
4. **Auditability**: Clear separation between registration and logic
5. **Version Control**: Easy to see what changed (logic vs registration)

## Examples

### Master Orchestrator
**Location:** `.claude/commands/personal-automation/core/domains-master.md`
**Loads:** `_personal-automation/core/agents/master.md`

### Domain Agents (all 5 follow same pattern)
**Sofia (Finance):**
- **Command:** `.claude/commands/personal-automation/domains/finance/sofia.md`
- **Implementation:** `_personal-automation/domains/finance/agent.md`

**Elena (Email):**
- **Command:** `.claude/commands/personal-automation/domains/email/elena.md`
- **Implementation:** `_personal-automation/domains/email/agent.md`

**Scribe (Writing):**
- **Command:** `.claude/commands/personal-automation/domains/writing/scribe.md`
- **Implementation:** `_personal-automation/domains/writing/agent.md`

**Proxy (Social Media):**
- **Command:** `.claude/commands/personal-automation/domains/social-media/proxy.md`
- **Implementation:** `_personal-automation/domains/social-media/agent.md`

**The Architect (Framework Lab):**
- **Command:** `.claude/commands/personal-automation/domains/framework-lab/architect.md`
- **Implementation:** `_personal-automation/domains/framework-lab/agent.md`

## When Adding New Components

### Adding a New Domain Agent
1. Create full agent in `_personal-automation/domains/{domain-name}/agent.md`
2. Create wrapper in `.claude/commands/personal-automation/domains/{domain-name}/{agent-id}.md`
3. Follow the `<agent-activation>` pattern exactly
4. Update `_personal-automation/_config/agent-manifest.csv`

### Adding a New Workflow/Task/Tool
1. Create implementation in `_personal-automation/core/[workflows|tasks|tools]/`
2. Create wrapper in `.claude/commands/personal-automation/core/[workflows|tasks|tools]/`
3. Use LOAD pattern: `LOAD and execute the [type] at: _personal-automation/path/to/file`
4. Update appropriate manifest in `_personal-automation/_config/`

## Verification Checklist

Before committing changes, verify:
- [ ] `.claude/commands/` files are thin wrappers (< 20 lines)
- [ ] All wrappers use LOAD or `<agent-activation>` pattern
- [ ] No symlinks in `.claude/commands/`
- [ ] Implementation files exist in `_personal-automation/`
- [ ] Paths in wrapper files use `@_personal-automation/` prefix
- [ ] Manifest files updated (if applicable)

## Common Mistakes to Avoid

1. **Creating symlinks instead of wrapper files**
   - Symlinks bypass the skill registration system
   - They don't show up properly in Claude Code

2. **Putting implementation in `.claude/commands/`**
   - Violates single source of truth
   - Makes maintenance harder
   - Duplicates code

3. **Forgetting the `@` prefix in paths**
   - Should be: `@_personal-automation/path/to/file.md`
   - Not: `_personal-automation/path/to/file.md`

4. **Not updating manifests**
   - Agent changes need `agent-manifest.csv` updates
   - Workflow/task/tool changes need respective manifest updates

## Migration Note

If you encounter symlinks or inline implementations in `.claude/commands/`:
1. Remove the symlink or delete the inline implementation
2. Create a proper wrapper file using the patterns above
3. Ensure the implementation exists in `_personal-automation/`
4. Test the skill works correctly after migration

## References

- See BMAD backup in `_bmad/.claude-backup-bmad-20251223/` for reference pattern
- Original implementation pattern from BMAD framework
- Personal Automation architecture: `_personal-automation/_docs/architecture.md`
