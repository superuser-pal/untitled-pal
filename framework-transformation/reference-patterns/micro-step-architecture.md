# Micro-Step Architecture Pattern

**Status:** Reference Only - Not Core Framework

**Source:** Multiple BMAD workflow instruction files

## Overview

The micro-step architecture pattern breaks down workflow steps into sub-steps using notation like `1a`, `1b`, `1c` for fine-grained control over execution flow.

## Concept

Instead of:
```xml
<step n="1" goal="Setup">
  <action>Load config</action>
  <action>Load templates</action>
  <action>Initialize output</action>
</step>
```

Use micro-steps:
```xml
<step n="1" goal="Setup">
  <substep n="1a" title="Load Configuration">
    <action>Load config</action>
  </substep>

  <substep n="1b" title="Load Templates">
    <action>Load templates</action>
  </substep>

  <substep n="1c" title="Initialize Output">
    <action>Initialize output</action>
  </substep>
</step>
```

## When to Use

**Use micro-steps when:**
- Complex conditional logic within a single step
- Need to `goto` specific sub-operations
- Debugging requires granular step identification
- Documentation benefits from detailed breakdown

**Don't use micro-steps when:**
- Simple linear workflows (overhead not worth it)
- Steps are already atomic (can't break down further)
- Readability suffers from too much nesting

## Example from BMAD

From `workflow.xml`:

```xml
<step n="1" title="Load and Initialize Workflow">
  <substep n="1a" title="Load Configuration and Resolve Variables">
    <phase n="1">Load external config from config_source path</phase>
    <phase n="2">Resolve all {config_source}: references</phase>
    <phase n="3">Resolve system variables</phase>
    <phase n="4">Ask user for unknown variables</phase>
  </substep>

  <substep n="1b" title="Load Required Components">
    <check>If template path → Read template</check>
    <check>If validation path → Note for later</check>
  </substep>

  <substep n="1c" title="Initialize Output">
    <action>Resolve output file path</action>
    <action>Create output directory</action>
  </substep>
</step>
```

## Personal Automation Relevance

**Low** - Most personal workflows don't need this level of granularity. Use simple step numbering (1, 2, 3) unless you have a genuinely complex workflow with many conditional branches.

## Canonical Alternative

Use the standard step structure from **workflow-instructions-patterns.xml**:

```xml
<step n="1" goal="Simple description">
  <action>Do thing</action>
  <check if="condition">
    <action>Conditional thing</action>
  </check>
</step>
```

This is sufficient for 95% of personal automation workflows.
