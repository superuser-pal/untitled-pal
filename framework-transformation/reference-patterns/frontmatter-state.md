# Frontmatter State Management Pattern

**Status:** Reference Only - Not Core Framework

**Source:** BMAD workflow documents with YAML frontmatter

## Overview

Using YAML frontmatter in markdown files to embed workflow state, progress tracking, and metadata directly in document headers.

## Concept

**Markdown file with state:**

```markdown
---
status: in_progress
started: 2025-12-15
last_updated: 2025-12-22
completion: 60%
sections_complete:
  - executive_summary
  - problem_statement
sections_remaining:
  - solution
  - roadmap
---

# My Document

Content here...
```

**Benefits:**
- Self-contained stateful documents
- No external state file needed
- Git-trackable (state changes visible in diffs)

**Drawbacks:**
- State mixed with content
- Harder to query across multiple documents
- Manual updates required

## When to Use

**Use frontmatter state when:**
- Single document with self-contained state
- Want state version-controlled with content
- Sharing documents (state travels with file)

**Don't use when:**
- Managing state across multiple documents (use separate state file)
- Need to query/aggregate state (separate YAML is easier)
- State updates are complex (better in dedicated file)

## Example from BMAD

**Workflow output with frontmatter:**

```markdown
---
workflow: create-prd
status: draft
version: 0.1
author: John (PM Agent)
created: 2025-12-15
sections_complete: 5
sections_total: 10
---

# Product Requirements Document

## Executive Summary
...
```

## Personal Automation Relevance

**Low-Medium** - Separate state files (like goal-status.yaml) are cleaner for most personal automation.

## Canonical Alternative

Use **goal-tracking-pattern.yaml** (canonical #5) for state management:

**Separate state file:**

```yaml
# goal-status.yaml
goals:
  - id: "weight-loss"
    status: "in_progress"
    progress: 60%
    started: "2025-01-01"
    target_date: "2025-06-30"
```

**Content file:**

```markdown
# Weight Loss Goal

Target: Lose 20 pounds by June 30, 2025

## Progress
See: [goal-status.yaml](../goal-status.yaml)

## Plan
...
```

**Why separate files are better:**
- Query all goals: Load one YAML
- Update state: Edit one file
- Content stays clean: Markdown has no metadata clutter
- Aggregate progress: Easy to sum across goals

## When Frontmatter Makes Sense

**For shared documents:**

```markdown
---
shared_with: ["Alice", "Bob", "Charlie"]
last_reviewed: 2025-12-20
next_review: 2026-01-20
---

# Family Budget 2025

Income: $10,000/month
...
```

State travels with the document when shared - no external dependencies.

**For most personal automation:** Separate state files are cleaner and more maintainable.
