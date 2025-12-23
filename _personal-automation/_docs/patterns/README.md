# Extracted Patterns from BMAD Framework

**Extraction Date:** 2025-12-22
**Source:** BMAD (Business Methodology for Agentic Development)
**Purpose:** Preserve reusable patterns before deleting software-specific BMM module

---

## Overview

This directory contains **12 core canonical patterns** extracted from the BMAD framework. These patterns are **domain-agnostic** and will be used to build the personal automation framework.

Additionally, **7 reference patterns** are documented in [reference-patterns/](../reference-patterns/) for advanced users and specific edge cases.

## Extracted Patterns

### 1. Agent Template
**File:** [agent-template.xml](./agent-template.xml)
**Size:** 7.9 KB
**Purpose:** Complete structure for creating new agents

**Contains:**
- Agent file structure (XML + front matter)
- Activation protocol (config loading, menu display, user input handling)
- Menu handlers (job execution, file execution)
- Persona structure (role, identity, communication style, principles)
- Party mode integration
- Example: Finance Manager agent

**Use When:** Creating a new agent for any domain

---

### 2. Workflow YAML Structure
**File:** [workflow-yaml-structure.md](./workflow-yaml-structure.md)
**Size:** 10 KB
**Purpose:** Complete reference for workflow.yaml configuration files

**Contains:**
- All workflow.yaml fields with descriptions
- Configuration inheritance pattern
- Variable resolution (system, config, workflow, runtime)
- Input file patterns (required, optional, selective load strategies)
- Output file configuration
- 3 complete examples (simple, with required inputs, complex with multiple patterns)
- Best practices

**Use When:** Creating a new workflow configuration

---

### 3. Workflow Instructions Patterns
**File:** [workflow-instructions-patterns.xml](./workflow-instructions-patterns.xml)
**Size:** 13 KB
**Purpose:** XML patterns for workflow instructions.md files

**Contains:**
- All core XML tags: `<workflow>`, `<step>`, `<action>`, `<output>`, `<ask>`, `<check>`, `<template-output>`, `<invoke-protocol>`, `<note>`
- Common patterns:
  - Load previous data (optional)
  - Collect information
  - Iterative list building
  - Review and confirmation
  - Template output with YOLO
  - Multi-agent dialogue
  - Error handling
- Variable naming conventions (single vs double braces)
- Complete workflow example
- Migration notes from BMM

**Use When:** Writing workflow instructions.md logic

---

### 4. Team Collaboration Pattern
**File:** [team-pattern.yaml](./team-pattern.yaml)
**Size:** 10 KB
**Purpose:** Multi-agent team configuration and party mode integration

**Contains:**
- Team YAML structure
- 5 personal automation team examples:
  - Financial Planning Team
  - Life Balance Team
  - Career Development Team
  - Health & Wellness Team
  - Productivity Team
- Party mode dialogue format
- default-party.csv structure
- Team composition guidelines (small/medium/large teams)
- Usage patterns (loading teams, dialogue, decision framework)
- Complete example: "Should I quit my job?" multi-agent discussion

**Use When:**
- Creating pre-configured agent teams
- Setting up party mode for complex decisions

---

### 5. Goal Tracking Pattern
**File:** [goal-tracking-pattern.yaml](./goal-tracking-pattern.yaml)
**Size:** 11 KB
**Purpose:** Personal goal/habit tracking - adapted from sprint-status.yaml

**Contains:**
- Complete goal-status.yaml structure
- Sections:
  - Metadata (period, dates, owner)
  - Goals (with categories, status, progress, notes)
  - Tasks (linked to parent goals)
  - Habits (daily/weekly with streaks)
  - Blockers (with impact and mitigation)
  - Retrospective notes (wins, struggles, adjustments)
- 3 examples:
  - Monthly goal tracking
  - Weekly goal status
  - Quarterly financial goals
- Integration with workflows (check status, update status, retrospective)
- Agent integration (how different agents use goal data)
- Migration mapping from sprint-status

**Use When:**
- Tracking personal goals (weekly/monthly/quarterly)
- Building goal management workflows

---

### 6. Review/Retrospective Pattern
**File:** [review-pattern.xml](./review-pattern.xml)
**Size:** 12 KB
**Purpose:** Personal review and reflection - adapted from epic retrospective

**Contains:**
- 4 review types: Weekly (15-30 min), Monthly (45-60 min), Quarterly (2-3 hrs), Annual (half day)
- Complete monthly review workflow:
  - Load goal status and determine period
  - Review goal completion (completed, in-progress, not-started)
  - Identify what went well (proudest moments, successful habits, surprises)
  - Identify what struggled (biggest struggles, failed habits, drains)
  - Extract lessons learned (self-insight, stop-start-continue)
  - Plan adjustments for next period
  - Generate review document
- Review template (markdown format)
- Integration with agents (multi-agent reviews)
- Variations (quick weekly, deep quarterly, life area reviews)

**Use When:**
- End of period reviews (weekly/monthly/quarterly/annual)
- Extracting lessons learned
- Planning next period

---

### 7. Mid-Cycle Adjustment Pattern
**File:** [adjustment-pattern.xml](./adjustment-pattern.xml)
**Size:** 14 KB
**Purpose:** Course correction when things go off track - adapted from correct-course workflow

**Contains:**
- When to use (trigger events: major life change, persistent struggle, new information, feeling stuck)
- Complete adjustment workflow:
  - Identify trigger event
  - Load current goal status
  - Analyze root cause (TIME, ENERGY, MOTIVATION, EXTERNAL BLOCKER)
  - Generate solution options (4 options per problem type)
  - Create action plan
  - Update goal status
  - Document adjustment
- Adjustment template
- 4 common scenarios:
  - Overcommitted (too many goals)
  - Wrong approach (method doesn't fit)
  - External blocker (schedule change)
  - Energy mismatch (wrong timing)
- Integration with goal status and retrospectives
- Best practices (don't overuse, be honest, document why, follow through)

**Use When:**
- Mid-period course corrections
- Persistent goal failures
- Major life changes affecting goals
- Need to pivot strategy

---

### 8. Documentation Generation Pattern
**File:** [documentation-pattern.xml](./documentation-pattern.xml)
**Size:** 13 KB
**Purpose:** Automatically generate index.md files for directories

**Contains:**
- Complete index-docs task structure
- Flow breakdown (4 steps: Scan → Group → Generate Descriptions → Create Index)
- Output format with examples
- Use cases for personal automation:
  - Organizing budget documents
  - Indexing health logs
  - Creating navigation for goal reviews
  - Organizing career learning materials
- Integration with workflows (auto-index after completion)
- Best practices (when to index, grouping strategies, description quality)
- Agent integration (Finance, Health, Schedule agents using indexes)

**Use When:**
- Folders with 5+ documents
- Regular document additions (weekly/monthly)
- Need quick navigation of outputs
- Agents need to search/reference documents

---

### 9. Document Sharding Pattern
**File:** [sharding-pattern.xml](./sharding-pattern.xml)
**Size:** 18 KB
**Purpose:** Split large markdown documents into smaller, organized files

**Contains:**
- Complete shard-doc tool structure
- Flow breakdown (6 steps: Get Source → Determine Destination → Execute → Verify → Report → Handle Original)
- Tool dependency: `npx @kayvan/markdown-tree-parser`
- When to shard (500+ lines, 10+ sections)
- Use cases for personal automation:
  - Split annual reviews into quarterly sections
  - Break apart comprehensive goal documents
  - Organize large research documents
  - Manage extensive career development plans
- Document structure guidelines (level 2 headings)
- Best practices (sharding threshold, original document handling)
- Integration with documentation pattern (shard → index)
- Agent integration (selective section loading)

**Use When:**
- Documents exceed 500 lines
- Documents have 10+ logical sections
- Need to reference only parts of document
- Want to enable selective loading for agents

---

### 10. Workflow Execution Engine Pattern
**File:** [workflow-engine-pattern.xml](./workflow-engine-pattern.xml)
**Size:** 24 KB
**Purpose:** Complete workflow runtime system that powers all workflows

**Contains:**
- Three-step execution flow (Load & Initialize → Process Steps → Complete)
- Variable resolution system (config, system, user-provided)
- Execution modes (Normal vs YOLO)
- Supported XML tags reference (structural, execution, output)
- **discover_inputs Protocol** - Smart file loading with 3 strategies:
  - FULL_LOAD (load all sharded files)
  - SELECTIVE_LOAD (load specific shard using template variable)
  - INDEX_GUIDED (load index, analyze, intelligently load relevant docs)
- Template-output checkpoint system
- Integration with workflow.yaml and instructions
- Personal automation examples (monthly budget review, weekly health check-in)
- Best practices (variable resolution, load strategies, template-output placement)
- Advanced features (goto control flow, for-each iteration, nested workflows)

**Use When:**
- Building custom workflows for any domain
- Need to understand how workflows execute
- Debugging workflow behavior
- Designing complex multi-step processes
- Want to use advanced features (protocols, YOLO, conditionals)

---

### 11. Advanced Elicitation Pattern
**File:** [advanced-elicitation-pattern.xml](./advanced-elicitation-pattern.xml)
**Size:** 21 KB
**Purpose:** Content enhancement through 50+ structured thinking techniques

**Contains:**
- Three-step flow (Method Registry Loading → Present Options → Execute)
- CSV-driven method library (50+ techniques)
- Nine method categories:
  - Core (First Principles, Root Cause, SWOT, Cost-Benefit)
  - Collaboration (Stakeholder Round Table, Devil's Advocate)
  - Scenario (Scenario Planning, Pre-Mortem, Future Wheel)
  - Structural (Idea Pyramids, Mind Mapping, Gap Analysis)
  - Advanced (Thread of Thought, Pattern Recognition, Systems Thinking)
  - Risk (Risk Matrix, Failure Mode Analysis)
  - Learning (Feynman Technique, Socratic Questioning)
  - Creative (Brainstorming, SCAMPER, Idea Synthesis)
  - Foundational (5 Whys, Pros/Cons, Prioritization Matrix)
- Smart selection algorithm (context-aware method selection)
- Interactive menu with reshuffle, list all, exit
- Iterative enhancement loop
- Integration with workflow checkpoints
- Personal automation use cases (goal refinement, monthly reviews, budget planning, career decisions)
- Best practices (when to use, method selection strategy, balancing depth and time)

**Use When:**
- Enhancing important documents
- Making significant decisions
- Stuck on a problem
- Want deeper insights
- Brainstorming options
- Reviewing progress
- Planning major changes

---

### 12. Validation Pattern
**File:** [validation-pattern.xml](./validation-pattern.xml)
**Size:** 19 KB
**Purpose:** Checklist-based validation with evidence collection and quality gates

**Contains:**
- Four-step flow (Setup → Validate → Generate Report → Summary)
- Four validation marks:
  - ✓ PASS - Requirement fully met (with evidence)
  - ⚠ PARTIAL - Some coverage but incomplete (explain gaps)
  - ✗ FAIL - Not met or severely deficient (explain why)
  - ➖ N/A - Not applicable (explain reason)
- Evidence collection with line numbers
- Comprehensive validation report format
- Critical validation rules (never skip, always provide evidence, think deeply, auto-save, halt after summary)
- Personal automation use cases:
  - Monthly goal validation
  - Habit streak verification
  - Quarterly review completeness
  - Annual goal quality gates
- Integration with workflows (post-workflow validation, standalone validation, iterative fix-validate loop)
- Best practices (writing good checklists, collecting strong evidence, actionable recommendations, quality thresholds, iteration)
- Example checklists for personal automation

**Use When:**
- Validating monthly/quarterly/annual goals
- Verifying habit streaks
- Checking review completeness
- Quality-gating important decisions
- Ensuring plan thoroughness
- Before committing to major changes

---

## Pattern Relationships

```
Agent Template (#1)
    ↓ (uses)
Team Pattern (#4) ← Multi-agent collaboration
    ↓ (uses)
Workflow YAML Structure (#2)
    ↓ (references)
Workflow Instructions Patterns (#3)
    ↓ (executed by)
Workflow Engine Pattern (#10) ← Runtime system, discover_inputs protocol
    ↓ (manipulates)
Goal Tracking Pattern (#5)
    ↓ (reviewed by)
Review Pattern (#6) & Adjustment Pattern (#7)
    ↓ (enhanced by)
Advanced Elicitation Pattern (#11) ← Content enhancement at checkpoints
    ↓ (validated by)
Validation Pattern (#12) ← Quality gates, checklist validation
    ↓ (generates outputs)
Documentation Pattern (#8) ← Organizes workflow outputs
    ↑ (works with)
Sharding Pattern (#9) ← Splits large documents
```

**Flow:**
1. **Agents (#1)** use **Teams (#4)** for collaboration
2. **Agents** execute **Workflows** (YAML #2 + Instructions #3) via **Workflow Engine (#10)**
3. **Workflow Engine** loads files via discover_inputs protocol
4. **Workflows** read/write **Goal Status (#5)** data
5. **Review Workflow (#6)** analyzes Goal Status at end of period
6. **Adjustment Workflow (#7)** modifies Goal Status mid-period
7. **Advanced Elicitation (#11)** enhances content at workflow checkpoints
8. **Validation (#12)** verifies outputs meet checklists
9. **Documentation Pattern (#8)** indexes workflow output folders
10. **Sharding Pattern (#9)** splits large documents created by workflows
11. **Documentation + Sharding** work together (shard → index)

---

## Validation Checklist

- [x] **1. Agent Template** - Complete structure with persona, menu, handlers
- [x] **2. Workflow YAML** - All fields documented with examples
- [x] **3. Workflow Instructions** - All XML tags and patterns documented
- [x] **4. Team Pattern** - Multi-agent setup with party mode integration
- [x] **5. Goal Tracking** - Complete goal-status.yaml structure
- [x] **6. Review Pattern** - Retrospective workflow for all time periods
- [x] **7. Adjustment Pattern** - Course correction workflow with root cause analysis
- [x] **8. Documentation Pattern** - Index generation for directory organization
- [x] **9. Sharding Pattern** - Document splitting for large files
- [x] **10. Workflow Engine** - Runtime system with discover_inputs protocol
- [x] **11. Advanced Elicitation** - 50+ content enhancement techniques
- [x] **12. Validation** - Checklist-based validation framework

**Status:** ✅ All 12 canonical patterns extracted and validated
**Reference Patterns:** ✅ 7 additional patterns documented in reference-patterns/

---

## Usage Guide

### Building a New Agent

1. Copy `agent-template.xml`
2. Replace placeholders: `{agent-id}`, `{Agent Name}`, `{emoji}`, etc.
3. Define persona (role, identity, communication_style, principles)
4. Add menu items pointing to workflows
5. Update config path to personal automation module

### Building a New Workflow

1. Create `workflow.yaml` using `workflow-yaml-structure.md`
2. Set config_source, variables, paths
3. Define input/output files
4. Create `instructions.md` using `workflow-instructions-patterns.xml`
5. Write step-by-step logic with XML tags
6. Create template.md if needed

### Setting Up Goal Tracking

1. Create `goal-status.yaml` using `goal-tracking-pattern.yaml`
2. Define goals with categories, status, progress
3. Add tasks linked to goals
4. Track habits (daily/weekly)
5. Document blockers and notes

### Creating a Review Workflow

1. Use `review-pattern.xml` as guide
2. Decide review frequency (weekly/monthly/quarterly)
3. Build workflow to load goal-status.yaml
4. Extract completion, wins, struggles, lessons
5. Generate review document from template

### Setting Up Course Correction

1. Use `adjustment-pattern.xml` as guide
2. Create workflow triggered by user request
3. Analyze root cause (time/energy/motivation/blocker)
4. Present solution options
5. Update goal-status.yaml with changes

### Organizing Outputs with Documentation Pattern

1. Use `documentation-pattern.xml` as guide
2. Run index-docs task on output folders (budgets, reviews, logs)
3. Auto-generate index.md with file descriptions
4. Integrate with workflows (auto-index after completion)
5. Agents can now navigate folders easily

### Managing Large Documents with Sharding Pattern

1. Use `sharding-pattern.xml` as guide
2. Identify large documents (500+ lines)
3. Run shard-doc tool to split by level 2 headings
4. Generates individual section files + index.md
5. Handle original document (delete/archive/keep)
6. Optionally re-index sharded folder with index-docs

### Understanding the Workflow Engine

1. Use `workflow-engine-pattern.xml` as reference
2. Understand the three-step execution flow (Load & Initialize → Process Steps → Complete)
3. Learn variable resolution (config_source, system variables, user input)
4. Study discover_inputs protocol for smart file loading
5. Understand execution modes (Normal vs YOLO)
6. Use template-output checkpoints for user interaction
7. Apply to building custom workflows

### Using Advanced Elicitation for Content Enhancement

1. Use `advanced-elicitation-pattern.xml` as guide
2. Integrate with workflow checkpoints (user selects [a] at template-output)
3. Review 50+ techniques across 9 categories
4. Apply context-appropriate methods (goal setting, decision making, problem solving)
5. Iterate multiple methods for deep enhancement
6. Exit when satisfied with content quality
7. Extend CSV with custom techniques if needed

### Validating Outcomes with Checklists

1. Use `validation-pattern.xml` as guide
2. Create checklist for your domain (goals, reviews, plans, habits)
3. Run validation task against document
4. Review validation report (PASS/PARTIAL/FAIL/N/A marks)
5. Fix critical (FAIL) items first
6. Address important (PARTIAL) items
7. Re-validate until quality gate passes

---

## Migration Notes

When adapting these patterns from BMM to personal automation:

### Terminology Changes
| BMM (Software) | Personal Automation |
|----------------|---------------------|
| Epic | Goal |
| Story | Task |
| Sprint | Week/Month |
| Velocity | Progress % |
| Sprint Status | Goal Status |
| Retrospective | Review |
| Course Correction | Adjustment |

### Removals
- ❌ Software-specific: PRD, Architecture, Code Review, Testing
- ❌ Team roles: PM, Dev, Architect, QA
- ❌ Agile jargon: Sprint, Backlog, Story Points

### Keeps
- ✅ Agent structure (activation, persona, menu)
- ✅ Workflow engine (YAML + instructions.xml)
- ✅ Party mode (multi-agent collaboration)
- ✅ Status tracking pattern
- ✅ Review/retrospective pattern
- ✅ Course correction pattern
- ✅ Documentation generation (index-docs)
- ✅ Document sharding (shard-doc)

---

## Next Steps

With these patterns extracted, you can now:

1. **Phase 2:** Safely delete BMM module (software-specific components)
2. **Phase 3:** Set up core personal automation framework
3. **Phase 4:** Build first agent (Finance Manager) using these patterns
4. **Phase 5:** Build additional agents (Email, Health, Career, Schedule)
5. **Phase 6:** Test multi-agent collaboration with party mode

---

## Pattern Ownership

**Original Framework:** BMAD (Business Methodology for Agentic Development)
**Extraction:** Rodrigo, 2025-12-22
**Adaptation:** Personal Automation Framework (Life Management)
**License:** Personal use

---

**Extraction Status:** ✅ COMPLETE
**Ready for Phase 2:** YES
**All Patterns Validated:** YES
