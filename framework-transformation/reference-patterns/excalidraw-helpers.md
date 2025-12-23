# Excalidraw Helpers Pattern

**Status:** Reference Only - Not Core Framework

**Source:** BMAD create-excalidraw-* workflows

## Overview

BMAD includes helper functions and workflows for generating Excalidraw diagrams programmatically (flowcharts, architecture diagrams, wireframes, data flows, ERDs).

## Concept

**Excalidraw diagrams as code:**

```xml
<step n="X" goal="Generate diagram">
  <action>Use Excalidraw helper functions to create elements</action>
  <action>Define boxes, arrows, text, relationships programmatically</action>
  <action>Export as .excalidraw JSON file</action>
</step>
```

**BMAD workflows:**
- `create-excalidraw-flowchart` - Process flowcharts
- `create-excalidraw-diagram` - System architecture, ERDs, UML
- `create-excalidraw-wireframe` - UI wireframes
- `create-excalidraw-dataflow` - Data flow diagrams

## When to Use

**Use Excalidraw helpers when:**
- Generating visual diagrams automatically from data/specs
- Need programmatic diagram creation (not manual drawing)
- Building architecture visualization workflows

**Don't use when:**
- Manual diagram creation is faster (most cases)
- Text-based documentation is sufficient
- No need for visual diagrams

## Example from BMAD

**Flowchart generation:**

```
Input: Process description
Output: .excalidraw file with boxes and arrows representing process flow
```

**Architecture diagram generation:**

```
Input: Architecture description, components, relationships
Output: .excalidraw file with system architecture visualization
```

## Personal Automation Relevance

**Low** - Most personal automation is text-based (goals, reviews, budgets). Visual diagrams are rarely needed.

**Potential use cases:**
- Visualizing career path options (flowchart)
- Life goal roadmaps (diagram)
- Budget flow visualization (data flow)

But these are edge cases - text works fine for 95% of personal automation.

## Canonical Alternative

Use text-based documentation (markdown) for personal automation. If you truly need a diagram:
1. Create it manually in Excalidraw
2. Save to your personal automation folder
3. Reference from markdown documents

Example:
```markdown
## Budget Flow

See: [budget-flow-diagram.excalidraw](./diagrams/budget-flow.excalidraw)

Income → Checking → Auto-split:
- 50% → Expenses
- 30% → Savings
- 20% → Investments
```

Text description is clearer and easier to maintain than programmatic diagram generation.
