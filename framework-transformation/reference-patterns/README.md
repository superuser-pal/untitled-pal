# Reference Patterns

**Status:** Reference only - NOT part of core framework

These patterns are documented for awareness and potential future use but are **not considered canonical patterns** in the personal automation framework. They represent more specialized or BMAD-specific concepts that may be useful in specific scenarios but are not essential for most personal automation workflows.

## What Are Reference Patterns?

Reference patterns are:
- ✅ Documented for understanding BMAD architecture
- ✅ Available for advanced users who want to explore
- ✅ Potentially useful in specific edge cases
- ❌ NOT required for personal automation
- ❌ NOT actively maintained as core patterns
- ❌ NOT recommended for beginners

## Available Reference Patterns

### 1. Micro-Step Architecture Pattern
**File:** [micro-step-architecture.md](./micro-step-architecture.md)

Breaking down workflow steps into micro-steps (1a, 1b, 1c) for fine-grained control.

**Use when:**
- Building extremely complex workflows with many conditional branches
- Need precise control over step execution order
- Debugging complex workflow logic

**Not needed for:** Most personal automation workflows (simple step numbering is sufficient)

---

### 2. Config Loading Pattern
**File:** [config-loading.md](./config-loading.md)

BMAD's multi-level configuration system (project config, module config, workflow config).

**Use when:**
- Managing shared configuration across many workflows
- Building a large suite of interconnected workflows
- Need hierarchical config overrides

**Not needed for:** Small personal automation setups (direct variable assignment works fine)

---

### 3. Smart Input Discovery Protocol
**File:** [smart-input-discovery.md](./smart-input-discovery.md)

Advanced strategies for loading sharded vs. whole documents (already covered in workflow-engine-pattern.xml).

**Use when:**
- You've already read workflow-engine-pattern and want deeper details
- Building custom protocols beyond discover_inputs

**Not needed for:** Standard usage (workflow engine pattern covers this)

---

### 4. Excalidraw Helpers Pattern
**File:** [excalidraw-helpers.md](./excalidraw-helpers.md)

BMAD's helper functions for generating Excalidraw diagrams programmatically.

**Use when:**
- Generating visual diagrams automatically (flowcharts, architecture diagrams)
- Need programmatic diagram creation

**Not needed for:** Text-based personal automation (most use cases)

---

### 5. Technique Library Management Pattern
**File:** [technique-library-management.md](./technique-library-management.md)

Managing CSV-based technique libraries (already implemented in advanced-elicitation-pattern).

**Use when:**
- Creating your own custom method libraries beyond elicitation
- Building domain-specific technique collections

**Not needed for:** Using existing elicitation methods (advanced-elicitation-pattern covers this)

---

### 6. Master Agent Command Routing Pattern
**File:** [command-routing.md](./command-routing.md)

BMAD's bmad-master agent that routes commands to appropriate sub-agents.

**Use when:**
- Building a master orchestration agent
- Complex multi-agent command routing

**Not needed for:** Direct agent invocation (team pattern handles agent collaboration)

---

### 7. Frontmatter State Management Pattern
**File:** [frontmatter-state.md](./frontmatter-state.md)

Using YAML frontmatter in markdown files to track workflow state and progress.

**Use when:**
- Need to embed metadata in document files
- Want self-contained stateful documents

**Not needed for:** Separate state files work fine (like goal-status.yaml)

---

## When to Use Reference Patterns

**Use reference patterns if:**
- You've mastered the 12 canonical patterns
- You have a specific advanced use case
- You're curious about BMAD's internal architecture
- You want to build custom extensions

**Don't use reference patterns if:**
- You're new to personal automation
- The canonical patterns solve your needs
- You want simple, maintainable workflows

## Canonical Patterns (Recommended)

For most personal automation needs, use the **12 canonical patterns** in the [extracted-patterns](../extracted-patterns/) directory:

1. Agent Template Pattern
2. Workflow YAML Structure
3. Workflow Instructions Patterns
4. Team Pattern
5. Goal Tracking Pattern
6. Review Pattern
7. Adjustment Pattern
8. Documentation Pattern
9. Sharding Pattern
10. **Workflow Engine Pattern** (runtime system)
11. **Advanced Elicitation Pattern** (content enhancement)
12. **Validation Pattern** (checklist validation)

These 12 patterns provide everything needed for comprehensive personal automation.

---

## Summary

- **Canonical patterns (12):** Core framework - use these
- **Reference patterns (7):** Advanced/specialized - use only if needed
- **Default:** Start with canonical patterns, explore reference patterns later if curious

**Questions?**
- Canonical patterns are self-contained and well-documented
- Reference patterns assume familiarity with BMAD and canonical patterns
- When in doubt, stick to canonical patterns
