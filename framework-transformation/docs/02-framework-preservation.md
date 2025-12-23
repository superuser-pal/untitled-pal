# Framework Preservation Guide: BMAD Core to Personal Automation

**Purpose:** Detailed documentation of what remains from BMAD and how to use it for personal automation

**Status:** 📘 ACTIVE REFERENCE
**Last Updated:** 2025-12-22

---

## Overview

After removing software development specifics, the BMAD core engine provides a **universal automation framework** with these preserved components:

1. **Workflow Execution Engine** - Runs any multi-step process
2. **Agent System** - Specialized AI personas with menus
3. **Configuration Management** - Cascading config with variable resolution
4. **Multi-Agent Collaboration** - Party mode for group discussions
5. **Manifest Registry** - Central tracking of agents and workflows

---

## Part 1: The Workflow Execution Engine

### File Location
`_bmad/core/tasks/workflow.xml` (233 lines)

### What It Does

The workflow engine is a **universal state machine** that executes any structured process defined in YAML + XML/Markdown instructions.

### How It Works

**Input:** A `workflow.yaml` configuration file
**Process:** Loads config → Resolves variables → Executes steps → Saves outputs
**Output:** Generated documents, updated files, or completed actions

### Workflow Structure

Every workflow consists of:

```
my-workflow/
├── workflow.yaml        # Configuration and variables
├── instructions.xml     # Step-by-step execution logic
├── template.md          # Optional output template
└── checklist.md         # Optional validation checklist
```

### workflow.yaml Format

```yaml
name: workflow-name
description: "What this workflow does"
author: "Your name"

# Config inheritance
config_source: "{project-root}/_personal-automation/life-management/config.yaml"

# Variables (resolved from config or user input)
user_name: "{config_source}:user_name"
output_folder: "{config_source}:output_folder"
custom_var: ""  # Will prompt user if empty

# Paths
installed_path: "{project-root}/_personal-automation/life-management/workflows/my-workflow"
instructions: "{installed_path}/instructions.xml"
template: "{installed_path}/template.md"

# Output
output:
  default_output_file: "{output_folder}/my-output-{date}.md"

# Optional: Input file discovery
input_file_patterns:
  budget:
    whole: "{output_folder}/*budget*.md"
    load_strategy: FULL_LOAD
```

### Variable Resolution Order

1. **System-generated:** `{date}`, `{project-root}`
2. **From config:** `{config_source}:variable_name`
3. **From workflow:** Defined directly in workflow.yaml
4. **From user:** Prompted if undefined when needed

### instructions.xml Format

```xml
<instructions>
  <!-- Basic Step -->
  <step n="1" goal="Load Data">
    <action>Read {data_file} to get current state</action>
    <ask>What month are we analyzing? (default: current)</ask>
  </step>

  <!-- Conditional Step -->
  <step n="2" goal="Process Data" optional="true">
    <check if="data_exists">
      <action>Analyze data patterns</action>
    </check>
  </step>

  <!-- Template Output Checkpoint -->
  <step n="3" goal="Generate Summary">
    <action>Calculate totals and averages</action>
    <template-output section="summary">
      ## Summary
      - Total: ${total}
      - Average: ${average}
    </template-output>
    <!-- User must confirm before continuing -->
  </step>

  <!-- Iteration -->
  <step n="4" goal="Process Each Item" for-each="items">
    <action>Process item: {current_item}</action>
  </step>

  <!-- Call Another Workflow -->
  <step n="5" goal="Delegate to Subworkflow">
    <invoke-workflow path="{project-root}/workflows/sub-workflow/workflow.yaml">
      <param name="input_data">{data}</param>
    </invoke-workflow>
  </step>
</instructions>
```

### Supported XML Tags

**Structural Tags:**
- `<step n="X" goal="...">` - Define numbered step
- `optional="true"` - Step can be skipped
- `if="condition"` - Conditional execution
- `for-each="collection"` - Iterate over items
- `repeat="n"` - Repeat n times

**Execution Tags:**
- `<action>` - Required action to perform
- `<ask>` - Get user input (ALWAYS waits)
- `<check if="...">...</check>` - Conditional block
- `<goto step="X">` - Jump to step
- `<invoke-workflow>` - Call another workflow
- `<invoke-protocol>` - Execute reusable protocol

**Output Tags:**
- `<template-output>` - Save content checkpoint (user must approve)
- `<critical>` - Cannot be skipped
- `<example>` - Show example output

### Execution Modes

**Normal Mode (Default):**
- User confirms EVERY template-output checkpoint
- User can trigger: [a] Advanced Elicitation, [c] Continue, [p] Party Mode, [y] YOLO

**YOLO Mode:**
- Skip all confirmations
- Minimize prompts
- Simulate expert user responses
- Generate entire workflow autonomously

### The discover_inputs Protocol

Built-in protocol for smart file loading:

```yaml
# In workflow.yaml
input_file_patterns:
  budget:
    whole: "{output_folder}/*budget*.md"
    sharded: "{output_folder}/*budget*/**.md"
    load_strategy: FULL_LOAD
```

```xml
<!-- In instructions.xml -->
<step n="1">
  <invoke-protocol name="discover_inputs"/>
  <!-- Automatically loads files and creates {budget_content} variable -->
</step>
```

**Load Strategies:**
- **FULL_LOAD:** Load all files in directory
- **SELECTIVE_LOAD:** Load specific file using template variable (e.g., `{{month}}`)
- **INDEX_GUIDED:** Load index.md, analyze, load relevant docs

### How to Execute a Workflow

**Method 1: Via Agent Menu**
Agent has menu item: `workflow="{path}/workflow.yaml"`

**Method 2: Direct Execution**
1. Load workflow.xml instructions
2. Pass workflow.yaml path as parameter
3. Follow workflow.xml execution steps

### Personal Automation Examples

**Example 1: Monthly Budget Workflow**

```yaml
# workflows/finance/monthly-budget/workflow.yaml
name: monthly-budget
description: "Create monthly budget with spending analysis"

config_source: "{project-root}/_personal-automation/life-management/config.yaml"
user_name: "{config_source}:user_name"
output_folder: "{config_source}:output_folder"

month: ""  # Will prompt user
year: system-generated

installed_path: "{project-root}/_personal-automation/life-management/workflows/finance/monthly-budget"
instructions: "{installed_path}/instructions.xml"

output:
  default_output_file: "{output_folder}/budgets/budget-{month}-{year}.md"

input_file_patterns:
  previous_budget:
    whole: "{output_folder}/budgets/budget-*-{year}.md"
    load_strategy: INDEX_GUIDED
```

**Example 2: Email Triage Workflow**

```yaml
# workflows/email/daily-triage/workflow.yaml
name: daily-triage
description: "Process inbox to zero with task extraction"

config_source: "{project-root}/_personal-automation/life-management/config.yaml"
output_folder: "{config_source}:output_folder"

installed_path: "{project-root}/_personal-automation/life-management/workflows/email/daily-triage"
instructions: "{installed_path}/instructions.xml"

output:
  default_output_file: "{output_folder}/email/triage-{date}.md"
```

---

## Part 2: The Agent System

### Agent Structure

Agents are defined in Markdown files with XML-embedded personas.

### Complete Agent Template

```xml
---
name: "agent-id"
description: "Agent role description"
---

You must fully embody this agent's persona and follow all activation instructions exactly as specified. NEVER break character until given an exit command.

<agent id="agent-id.agent.yaml" name="PersonaName" title="Agent Title" icon="🎭">

  <activation critical="MANDATORY">
    <step n="1">Load persona from this current agent file (already in context)</step>

    <step n="2">🚨 IMMEDIATE ACTION REQUIRED - BEFORE ANY OUTPUT:
      - Load and read {project-root}/_personal-automation/life-management/config.yaml NOW
      - Store ALL fields as session variables: {user_name}, {output_folder}, etc.
      - VERIFY: If config not loaded, STOP and report error to user
      - DO NOT PROCEED to step 3 until config is successfully loaded and variables stored
    </step>

    <step n="3">Remember: user's name is {user_name}</step>

    <step n="4">Show greeting using {user_name} from config, communicate in {communication_language}, then display numbered list of ALL menu items from menu section</step>

    <step n="5">STOP and WAIT for user input - do NOT execute menu items automatically - accept number or cmd trigger or fuzzy command match</step>

    <step n="6">On user input: Number → execute menu item[n] | Text → case-insensitive substring match | Multiple matches → ask user to clarify | No match → show "Not recognized"</step>

    <step n="7">When executing a menu item: Check menu-handlers section below - extract any attributes from the selected menu item (workflow, exec, action) and follow the corresponding handler instructions</step>

    <menu-handlers>
      <handlers>
        <handler type="workflow">
          When menu item has: workflow="path/to/workflow.yaml":
          1. CRITICAL: Always LOAD {project-root}/_bmad/core/tasks/workflow.xml
          2. Read the complete file - this is the CORE OS for executing workflows
          3. Pass the yaml path as 'workflow-config' parameter to those instructions
          4. Execute workflow.xml instructions precisely following all steps
          5. Save outputs after completing EACH workflow step
        </handler>

        <handler type="exec">
          When menu item has: exec="path/to/file.md":
          1. Actually LOAD and read the entire file at that path
          2. Read the complete file and follow all instructions within it
          3. If there is data="some/path/data.md" with same item, pass that data path as context
        </handler>

        <handler type="action">
          When menu item has: action="#id" → Find prompt with id="id" in current agent XML, execute its content
          When menu item has: action="text" → Execute the text directly as an inline instruction
        </handler>
      </handlers>
    </menu-handlers>

    <rules>
      <r>ALWAYS communicate in {communication_language}</r>
      <r>Stay in character until exit selected</r>
      <r>Display Menu items in the order given</r>
      <r>Load files ONLY when executing a user chosen workflow</r>
    </rules>
  </activation>

  <persona>
    <role>Primary capability + Secondary strength</role>
    <identity>Background, expertise, experience level, unique perspective</identity>
    <communication_style>How this agent talks, tone, patterns, quirks</communication_style>
    <principles>
      - Core belief 1: Detailed explanation
      - Core belief 2: Detailed explanation
      - Core belief 3: Detailed explanation
    </principles>
  </persona>

  <menu>
    <item cmd="*menu">[M] Redisplay Menu Options</item>
    <item cmd="*workflow1" workflow="{project-root}/workflows/workflow1/workflow.yaml">Workflow 1 Description</item>
    <item cmd="*workflow2" workflow="{project-root}/workflows/workflow2/workflow.yaml">Workflow 2 Description</item>
    <item cmd="*action1" action="#custom-action">Action 1 Description</item>
    <item cmd="*party" exec="{project-root}/_bmad/core/workflows/party-mode/workflow.md">Group chat with other agents</item>
    <item cmd="*dismiss">[D] Dismiss Agent</item>
  </menu>

  <!-- Optional: Custom action prompts -->
  <prompts>
    <prompt id="custom-action">
      Execute this inline action without a full workflow.
      Example: "List all your available workflows with descriptions"
    </prompt>
  </prompts>

</agent>
```

### Menu Item Types

**Type 1: Workflow Execution**
```xml
<item cmd="*budget" workflow="{path}/workflow.yaml">Create Monthly Budget</item>
```
Triggers: Load workflow.xml → Execute workflow

**Type 2: File Execution**
```xml
<item cmd="*analyze" exec="{path}/analyze.md">Analyze Data</item>
```
Triggers: Load file → Execute instructions in file

**Type 3: Inline Action**
```xml
<item cmd="*list" action="List all workflows from manifest">List Workflows</item>
```
Triggers: Execute action text directly

**Type 4: Prompt Reference**
```xml
<item cmd="*help" action="#help-prompt">Show Help</item>
```
Triggers: Find `<prompt id="help-prompt">` → Execute content

### Agent Interaction Flow

```
1. User loads agent file
   ↓
2. Agent activation sequence runs
   ↓
3. Config loaded, variables stored
   ↓
4. Menu displayed (numbered list)
   ↓
5. User selects option (number or fuzzy match)
   ↓
6. Menu handler executes (workflow/exec/action)
   ↓
7. Returns to menu (unless dismissed)
```

### Creating Personal Agents

**Finance Manager Example:**

```xml
<agent id="finance-manager" name="Sofia" title="Personal Finance Advisor" icon="💰">
  <persona>
    <role>Personal CFO + Investment Strategist + Budget Optimizer</role>
    <identity>15+ years wealth management, expert in budgeting and portfolio optimization. Specialized in helping individuals achieve financial clarity and long-term growth.</identity>
    <communication_style>Direct and data-driven. Asks probing questions about spending patterns. Uses analogies to explain complex finance concepts. Never judges, always explores the "why" behind financial decisions.</communication_style>
    <principles>
      - Track every dollar to understand true spending patterns and identify leaks
      - Automate savings before discretionary spending touches your account
      - Invest for long-term growth, not short-term market timing
      - Challenge unnecessary expenses: "Is this aligned with your goals?"
      - Financial freedom comes from conscious choices, not restriction
    </principles>
  </persona>

  <menu>
    <item cmd="*menu">[M] Redisplay Menu</item>
    <item cmd="*monthly" workflow="workflows/finance/monthly-budget/workflow.yaml">Create Monthly Budget</item>
    <item cmd="*analyze" workflow="workflows/finance/spending-analysis/workflow.yaml">Analyze Spending Patterns</item>
    <item cmd="*invest" workflow="workflows/finance/investment-review/workflow.yaml">Investment Review</item>
    <item cmd="*goals" workflow="workflows/finance/goal-tracking/workflow.yaml">Track Financial Goals</item>
    <item cmd="*party" exec="_bmad/core/workflows/party-mode/workflow.md">Financial Planning Party</item>
    <item cmd="*dismiss">[D] Dismiss Sofia</item>
  </menu>
</agent>
```

**Email Processor Example:**

```xml
<agent id="email-processor" name="Elena" title="Email Productivity Specialist" icon="📧">
  <persona>
    <role>Email Triage Expert + Task Extractor + Inbox Zero Advocate</role>
    <identity>10+ years as executive assistant to C-suite leaders, processed 500+ emails daily. Expert in GTD methodology and ruthless prioritization. Believes email is for communication, not storage.</identity>
    <communication_style>Efficient and actionable. Categorizes everything. Uses short, clear sentences. Never apologizes for being direct - time is valuable.</communication_style>
    <principles>
      - Inbox zero is achievable daily with a system
      - Every email gets ONE decision: action, read, archive, or delete
      - Extract tasks immediately - never re-read emails
      - Batch similar emails together for faster processing
      - Unsubscribe ruthlessly - protect your attention
    </principles>
  </persona>

  <menu>
    <item cmd="*menu">[M] Redisplay Menu</item>
    <item cmd="*triage" workflow="workflows/email/daily-triage/workflow.yaml">Daily Email Triage</item>
    <item cmd="*extract" workflow="workflows/email/extract-tasks/workflow.yaml">Extract Action Items</item>
    <item cmd="*summarize" workflow="workflows/email/newsletter-summary/workflow.yaml">Summarize Newsletter</item>
    <item cmd="*followup" workflow="workflows/email/follow-up-reminders/workflow.yaml">Set Follow-ups</item>
    <item cmd="*dismiss">[D] Dismiss Elena</item>
  </menu>
</agent>
```

---

## Part 3: Configuration Management

### Configuration Hierarchy

```
_personal-automation/
├── core/
│   └── config.yaml              # Level 1: Global user settings
│
└── life-management/
    ├── config.yaml              # Level 2: Module settings (inherits core)
    └── workflows/
        └── my-workflow/
            └── workflow.yaml    # Level 3: Workflow variables (inherits module)
```

### Level 1: Core Config

**Location:** `_personal-automation/core/config.yaml`

```yaml
# Core Module Configuration
# Generated: 2025-12-22

user_name: Rodrigo
communication_language: English
document_output_language: English
output_folder: "{project-root}/_automation-output"
timezone: America/Los_Angeles
```

**Purpose:** User preferences that apply to ALL modules

### Level 2: Module Config

**Location:** `_personal-automation/life-management/config.yaml`

```yaml
# Life Management Module Configuration
# Inherits from: {project-root}/_personal-automation/core/config.yaml

# Module specific paths
module_name: life-management
module_output: "{output_folder}/life-management"

# Financial settings
budget_folder: "{module_output}/budgets"
accounts_file: "{project-root}/_personal-data/accounts.yaml"
investment_file: "{project-root}/_personal-data/investments.yaml"

# Email settings
email_folder: "{module_output}/email"
task_export_format: markdown  # or todoist, things

# Health settings
health_folder: "{module_output}/health"
workout_log: "{health_folder}/workouts.md"

# Goal tracking
goals_folder: "{module_output}/goals"
weekly_review_day: Sunday
```

**Purpose:** Module-specific settings and paths

### Level 3: Workflow Config

**Location:** Workflow's `workflow.yaml`

```yaml
# Workflow Configuration
config_source: "{project-root}/_personal-automation/life-management/config.yaml"

# Pull from module config
user_name: "{config_source}:user_name"
output_folder: "{config_source}:module_output"
budget_folder: "{config_source}:budget_folder"

# Workflow-specific variables
month: ""  # Will prompt user
category: ""  # Will prompt user

# Computed variables
output_file: "{budget_folder}/budget-{month}-{year}.md"
```

**Purpose:** Workflow execution variables

### Variable Resolution

**Syntax:**
- `{config_source}:variable_name` - Pull from config
- `{variable_name}` - Use local or inherited variable
- `system-generated` - Auto-generated (date, year, etc.)

**Example:**
```yaml
# workflow.yaml
config_source: "{project-root}/config.yaml"
user: "{config_source}:user_name"       # Resolves to "Rodrigo"
folder: "{config_source}:output_folder" # Resolves to "_automation-output"
file: "{folder}/output-{date}.md"       # Resolves to "_automation-output/output-2025-12-22.md"
```

---

## Part 4: Multi-Agent Collaboration (Party Mode)

### What Is Party Mode?

Party mode enables **multiple agents to discuss** a topic together, combining their expertise.

### Location

`_bmad/core/workflows/party-mode/workflow.md`

### How It Works

1. User triggers party mode from any agent's menu
2. System loads ALL available agents from manifest
3. Agents take turns contributing to discussion
4. User moderates and asks questions
5. Agents respond in character, referencing each other's points

### Example Use Cases

**Personal Finance Decision:**
```
User: "Should I subscribe to this $50/month productivity tool?"

Sofia (Finance): "Let's look at your discretionary budget. You have $200/month
for tools. This would be 25%. What value does it provide?"

Elena (Email): "If it saves you 30 minutes daily on email, that's 15 hours/month.
What's your time worth?"

Marcus (Career): "Will this tool directly impact your promotion goals or skill
development?"
```

**Life Planning Discussion:**
```
User: "I want to run a marathon next year. Is this realistic?"

Dr. Ava (Health): "Current fitness level assessment needed. Can you run 5K now?
Marathon training is 16-20 weeks minimum."

Sofia (Finance): "Marathon entry is $150-200, plus gear ($200), nutrition ($50/month).
Budget impact: ~$500 total."

Alex (Schedule): "Training requires 4-5 runs/week, 6-12 hours/week. Your current
schedule has 3 free mornings. We'd need to optimize."
```

### Invoking Party Mode

**From Any Agent Menu:**
```xml
<item cmd="*party" exec="{project-root}/_bmad/core/workflows/party-mode/workflow.md">
  Bring all agents together
</item>
```

**Direct Command:**
Load party-mode workflow file and follow instructions

### Customizing Party Mode

Edit `party-mode/workflow.md` to:
- Change discussion format
- Add facilitation prompts
- Modify turn-taking rules
- Customize output format

---

## Part 5: Manifest Registry

### Purpose

Central CSV files that register all agents and workflows for discovery and management.

### Agent Manifest

**Location:** `_bmad/_config/agent-manifest.csv`

**Format:**
```csv
id,name,title,icon,description,module,file_path
finance-manager,Sofia,Personal Finance Advisor,💰,Manages budgets and investments,life-management,_personal-automation/life-management/agents/finance-manager.md
email-processor,Elena,Email Productivity Specialist,📧,Triages email and extracts tasks,life-management,_personal-automation/life-management/agents/email-processor.md
```

**Usage:**
- Master agent can list all agents
- Party mode loads agents from manifest
- Discovery commands use manifest

### Workflow Manifest

**Location:** `_bmad/_config/workflow-manifest.csv`

**Format:**
```csv
id,name,description,module,owner_agent,file_path
monthly-budget,Create Monthly Budget,Budget creation with spending analysis,life-management,finance-manager,_personal-automation/life-management/workflows/finance/monthly-budget/workflow.yaml
email-triage,Daily Email Triage,Process inbox to zero,life-management,email-processor,_personal-automation/life-management/workflows/email/daily-triage/workflow.yaml
```

**Usage:**
- Agents reference their workflows
- Status tracking uses manifest
- Documentation generation uses manifest

### Updating Manifests

**When adding new agent:**
1. Create agent .md file
2. Add row to agent-manifest.csv
3. Agent now discoverable

**When adding new workflow:**
1. Create workflow directory with workflow.yaml
2. Add row to workflow-manifest.csv
3. Reference in agent's menu

---

## Part 6: Preserved Optional Components

### Brainstorming Workflow

**Location:** `_bmad/core/workflows/brainstorming/`

**Purpose:** Creative ideation using diverse techniques

**Use Cases:**
- Personal goal brainstorming
- Life planning sessions
- Problem-solving discussions

**Keep if:** You want structured creative thinking sessions

### Advanced Elicitation

**Location:** `_bmad/core/tasks/advanced-elicitation.xml`

**Purpose:** Challenge the LLM to produce better responses

**Use Cases:**
- Improving workflow output quality
- Deeper analysis in any workflow
- Better recommendations

**Keep if:** You want to enhance AI response quality

### Excalidraw Resources

**Location:** `_bmad/core/resources/excalidraw/`

**Purpose:** Generate visual diagrams

**Use Cases:**
- Personal workflow flowcharts
- Goal mapping diagrams
- Process visualization

**Keep if:** You want to create visual diagrams

---

## Part 7: Framework Usage Patterns

### Pattern 1: Single Agent Workflow

```
1. Load agent: "Load finance-manager"
2. Agent shows menu
3. Select workflow: "1" or "*monthly-budget"
4. Workflow executes step-by-step
5. Confirm at each template-output
6. Final document saved
7. Return to agent menu
```

### Pattern 2: Multi-Agent Collaboration

```
1. Load agent: "Load finance-manager"
2. Trigger party: "*party"
3. All agents join discussion
4. Pose question: "Should I buy a house or keep renting?"
5. Agents contribute perspectives
6. Make decision based on collective input
```

### Pattern 3: YOLO Autonomous Execution

```
1. Load agent
2. Select workflow
3. When prompted at first template-output: "y" (YOLO mode)
4. Workflow completes automatically
5. Review final output
```

### Pattern 4: Workflow Chaining

```
Workflow A completes → Saves data
Workflow B loads data from A → Processes
Workflow C loads results from B → Generates report
```

**Example:**
1. `spending-analysis` → Generates spending-report.md
2. `budget-adjustment` → Loads spending-report.md → Proposes changes
3. `goal-impact` → Loads adjusted budget → Checks goal alignment

---

## Part 8: Migration Checklist

### Preserved Core Files

After deletion, verify these files exist:

- [ ] `_bmad/core/tasks/workflow.xml`
- [ ] `_bmad/core/workflows/party-mode/workflow.md`
- [ ] `_bmad/core/agents/bmad-master.md`
- [ ] `_bmad/core/config.yaml`
- [ ] `_bmad/_config/agent-manifest.csv` (structure only, cleared)
- [ ] `_bmad/_config/workflow-manifest.csv` (structure only, cleared)
- [ ] `_bmad/_config/manifest.yaml` (to be updated)

### Optional Components (Your Decision)

- [ ] `_bmad/core/workflows/brainstorming/` - Keep? Y/N
- [ ] `_bmad/core/tasks/advanced-elicitation.xml` - Keep? Y/N
- [ ] `_bmad/core/resources/excalidraw/` - Keep? Y/N
- [ ] `_bmad/core/tools/shard-doc.md` - Keep? Y/N

### Integration Points

These remain unchanged and work with new framework:

- [ ] `.claude/` - Claude settings and hooks
- [ ] `.agentvibes/` - TTS integration
- [ ] `.mcp.json` - MCP server config
- [ ] `claude-code-proxy/` - Monitoring tool

---

## Part 9: Building on the Foundation

### Step 1: Create New Module Structure

```bash
mkdir -p _personal-automation/life-management/{agents,workflows,knowledge}
mkdir -p _personal-automation/life-management/workflows/{finance,email,health,career}
```

### Step 2: Create Module Config

```bash
cp _bmad/core/config.yaml _personal-automation/core/config.yaml
# Edit with personal settings

cat > _personal-automation/life-management/config.yaml << 'EOF'
# Inherits from core/config.yaml
module_name: life-management
module_output: "{output_folder}/life-management"
# Add module-specific settings
EOF
```

### Step 3: Create First Agent

Use agent template from Part 2, customize persona and menu.

### Step 4: Create First Workflow

Use workflow.yaml format from Part 1, write instructions.xml.

### Step 5: Update Manifests

Add agent and workflow entries to manifest CSVs.

### Step 6: Test

1. Load agent
2. Execute workflow
3. Verify output
4. Test party mode with multiple agents

---

## Part 10: Quick Reference

### Loading Workflow Engine

```xml
<!-- From agent menu handler -->
<handler type="workflow">
  1. Load {project-root}/_bmad/core/tasks/workflow.xml
  2. Pass workflow.yaml path as parameter
  3. Execute workflow.xml instructions
</handler>
```

### Creating Workflow

**Minimum files:**
- `workflow.yaml` (config + variables)
- `instructions.xml` (step-by-step logic)

**Optional files:**
- `template.md` (output template)
- `checklist.md` (validation)

### Creating Agent

**Required sections:**
- `<activation>` - Load config, show menu, handle input
- `<persona>` - Role, identity, style, principles
- `<menu>` - List of available commands
- `<menu-handlers>` - How to execute menu items

### Variable Syntax

```yaml
from_config: "{config_source}:variable_name"
local_var: "value"
system_var: system-generated
composed: "{local_var}/path/{system_var}.md"
```

---

## Part 11: Document Sharding & Management System

### Overview

For complex professional work, single large documents become unwieldy. BMAD's sharding system splits documents into manageable sections while maintaining intelligent loading capabilities.

### The shard-doc Tool

**Location:** `_bmad/core/tools/shard-doc.xml`

**Purpose:** Automatically split large markdown documents into smaller, organized files based on level 2 headings (`##`).

**How It Works:**

1. Takes source document (e.g., `annual-financial-plan.md`)
2. Creates destination folder (e.g., `annual-financial-plan/`)
3. Executes: `npx @kayvan/markdown-tree-parser explode [source] [dest]`
4. Generates:
   - Individual section files (one per `##` heading)
   - `index.md` file as table of contents
5. Handles original document (delete, archive, or keep)

**Why Shard Documents:**

- **Selective Loading** - Load only relevant sections, not entire 5,000-line document
- **Token Efficiency** - Reduce LLM context usage by 70-90%
- **Better Organization** - Clear separation of concerns
- **Version Control** - Smaller diffs, easier merges
- **Navigation** - Find specific sections quickly

### Sharded Document Structure

**Before Sharding:**
```
annual-financial-plan.md (5,000 lines)
```

**After Sharding:**
```
annual-financial-plan/
├── index.md                          # Table of contents
├── 01-executive-summary.md           # Income & goals overview
├── 02-income-analysis.md             # Salary, bonuses, side income
├── 03-fixed-expenses.md              # Rent, utilities, subscriptions
├── 04-variable-expenses.md           # Groceries, entertainment
├── 05-savings-strategy.md            # Emergency fund, retirement
├── 06-investment-allocation.md       # Portfolio breakdown
├── 07-tax-optimization.md            # Deductions, credits, strategies
├── 08-debt-management.md             # Payoff plans
└── 09-quarterly-reviews.md           # Check-in schedule
```

**The index.md serves as:**
- Navigation hub with links to all sections
- Summary of document structure
- Discovery point for INDEX_GUIDED workflows

**Naming Conventions:**

- **Numbered shards** (`01-`, `02-`) - Sequential content, preserves reading order
- **Named shards** (`income-analysis`, `investment-strategy`) - Independent sections

### The discover_inputs Protocol

**Location:** `_bmad/core/tasks/workflow.xml` (lines 138-221)

The discover_inputs protocol intelligently loads documents using three strategies:

#### Strategy 1: FULL_LOAD

**Purpose:** Load ALL files in sharded directory

**Use Cases:**
- Annual financial reviews (need complete picture)
- Client project documentation (entire engagement history)
- Business plans (all sections for comprehensive analysis)
- Complete knowledge base topics

**Configuration:**
```yaml
input_file_patterns:
  annual_financial_plan:
    whole: "{output_folder}/*financial-plan*.md"
    sharded: "{output_folder}/*financial-plan*/*.md"
    load_strategy: "FULL_LOAD"
```

**Execution:**
1. Find ALL `.md` files in sharded directory
2. Load EVERY file completely
3. Concatenate in logical order (index.md first, then alphabetical)
4. Store in variable: `{annual_financial_plan_content}`

**Example Output:**
```
✓ Loaded {annual_financial_plan_content} from 9 sharded files:
  - index.md
  - 01-executive-summary.md
  - 02-income-analysis.md
  - ... (all sections)
```

#### Strategy 2: SELECTIVE_LOAD

**Purpose:** Load specific shard using template variable

**Use Cases:**
- Q3 budget analysis (load only Q3 section)
- Specific client proposal (load by client name)
- Single topic from knowledge base
- Particular quarter from annual plan

**Configuration:**
```yaml
input_file_patterns:
  quarterly_budget:
    whole: "{output_folder}/*budget*.md"
    sharded_index: "{output_folder}/*budget*/index.md"
    sharded_single: "{output_folder}/*budget*/quarter-{{quarter}}.md"
    load_strategy: "SELECTIVE_LOAD"
```

**Execution:**
1. Check for template variables (e.g., `{{quarter}}`)
2. If undefined, ask user: "Which quarter? (Q1/Q2/Q3/Q4)"
3. Resolve to specific file: `quarter-Q3.md`
4. Load only that file
5. Store in variable: `{quarterly_budget_content}`

**Example Output:**
```
✓ Loaded {quarterly_budget_content} from selective load:
  - quarter-Q3.md
```

#### Strategy 3: INDEX_GUIDED

**Purpose:** Load index, analyze context, then intelligently load relevant documents

**Use Cases:**
- Optional reference materials (load only if relevant)
- Large knowledge bases (filter by relevance)
- Brownfield documentation (context-dependent loading)
- Client archives (selective based on current work)

**Configuration:**
```yaml
input_file_patterns:
  client_history:
    description: "Past client engagement docs (optional)"
    sharded: "{output_folder}/clients/index.md"
    load_strategy: "INDEX_GUIDED"
```

**Execution:**
1. Load `index.md` from sharded directory
2. Parse table of contents, links, section headers
3. Analyze current workflow's purpose
4. Identify relevant documents (even if only 5% relevant)
5. Load all identified documents
6. Store combined content in variable

**Critical Mandate:**
> "DO NOT BE LAZY - use best judgment to load documents that might have relevant information, even if only a 5% chance"

**Example Decision:**
```
Current Workflow: Q3 budget review
Client History Index shows:
  - Acme Corp - Q1 consulting project
  - Beta Inc - Q2 retainer work
  - Gamma LLC - Q3 strategy engagement

Decision:
  ✓ Load Gamma LLC (Q3 engagement, directly relevant)
  ✓ Load Beta Inc (ongoing retainer, may inform budget)
  ✗ Skip Acme Corp (Q1 project, completed and unrelated)
```

### Professional Applications

#### Financial Planning Documents

**Annual Financial Plan (Sharded):**
```
financial-plan-2025/
├── index.md
├── income-sources.md           # Salary, bonuses, side income, passive
├── expense-analysis.md         # Fixed vs variable breakdown
├── savings-goals.md            # Emergency fund, home down payment
├── investment-strategy.md      # Asset allocation, risk tolerance
├── tax-optimization.md         # Deductions, credits, strategies
├── debt-payoff.md             # Student loans, credit cards
└── insurance-review.md         # Life, disability, health
```

**Workflow Usage:**
- `monthly-budget-update`: SELECTIVE_LOAD → expense-analysis.md
- `annual-review`: FULL_LOAD → All sections
- `tax-preparation`: SELECTIVE_LOAD → tax-optimization.md + income-sources.md

#### Client Documentation

**Client Engagement (Sharded):**
```
clients/acme-corp/
├── index.md
├── discovery-notes.md          # Initial interviews, pain points
├── proposal.md                 # Scope, timeline, pricing
├── contract.md                 # Legal agreement
├── deliverable-1.md            # Market analysis report
├── deliverable-2.md            # Strategy recommendations
└── retrospective.md            # Post-engagement learnings
```

**Workflow Usage:**
- `new-proposal`: INDEX_GUIDED → Load similar past proposals
- `deliverable-generation`: SELECTIVE_LOAD → Load specific deliverable template
- `client-retrospective`: FULL_LOAD → Complete engagement history

#### Knowledge Base

**Investment Knowledge Base (Sharded):**
```
knowledge-base/investing/
├── index.md
├── asset-classes.md            # Stocks, bonds, RE, crypto
├── portfolio-theory.md         # Diversification, rebalancing
├── tax-strategies.md           # Tax-loss harvesting, Roth conversions
├── real-estate.md              # REITs, rental properties, flipping
├── crypto-investing.md         # Bitcoin, Ethereum, DeFi
└── retirement-accounts.md      # 401k, IRA, HSA strategies
```

**Workflow Usage:**
- `investment-review`: INDEX_GUIDED → Load sections relevant to current portfolio
- `tax-planning`: SELECTIVE_LOAD → tax-strategies.md
- `portfolio-rebalance`: FULL_LOAD → Complete knowledge for comprehensive analysis

#### Business Plans

**Business Plan (Sharded):**
```
business-plan-saas-venture/
├── index.md
├── executive-summary.md        # Vision, mission, ask
├── market-analysis.md          # TAM, SAM, SOM, trends
├── competitive-landscape.md    # Competitors, differentiation
├── product-overview.md         # Features, roadmap
├── business-model.md           # Revenue streams, pricing
├── go-to-market.md            # Marketing, sales, channels
├── financial-projections.md    # 3-year forecast, unit economics
├── team.md                     # Founders, advisors, hiring plan
└── funding-requirements.md     # Capital needs, use of funds
```

**Workflow Usage:**
- `investor-pitch`: SELECTIVE_LOAD → executive-summary.md + financial-projections.md
- `quarterly-review`: FULL_LOAD → Complete plan for progress assessment
- `partnership-proposal`: INDEX_GUIDED → Load relevant sections for partner

### Example Workflow Configuration

**Monthly Budget Workflow with Sharding:**

```yaml
# workflows/finance/monthly-budget/workflow.yaml
name: monthly-budget
description: "Create monthly budget with previous data"

config_source: "{project-root}/_personal-automation/life-management/config.yaml"
user_name: "{config_source}:user_name"
output_folder: "{config_source}:output_folder"

month: ""  # Will prompt user
year: system-generated

installed_path: "{project-root}/_personal-automation/life-management/workflows/finance/monthly-budget"
instructions: "{installed_path}/instructions.xml"

output:
  default_output_file: "{output_folder}/budgets/budget-{month}-{year}.md"

# Load previous budget and annual plan
input_file_patterns:
  previous_budget:
    description: "Last month's budget for comparison"
    whole: "{output_folder}/budgets/budget-*-{year}.md"
    load_strategy: "SELECTIVE_LOAD"

  annual_plan:
    description: "Annual financial plan for context"
    whole: "{output_folder}/*financial-plan*.md"
    sharded: "{output_folder}/*financial-plan*/*.md"
    load_strategy: "INDEX_GUIDED"  # Only load relevant sections
```

### Benefits for Professional Work

1. **Context Efficiency** - Load only what you need, save 70-90% in tokens
2. **Faster Execution** - Less content to process = faster workflows
3. **Better Relevance** - Focused context produces better results
4. **Scalability** - Works with documents of any size
5. **Flexibility** - Same workflow works with whole or sharded documents
6. **Organization** - Clear structure makes documents navigable

---

## Part 12: Project Status & Progress Tracking

### Overview

Professional work involves managing multiple initiatives with different phases, milestones, and deliverables. BMAD's status tracking system provides project management discipline without bureaucratic overhead.

### The Workflow-Status System

**Location:** `_bmad/bmm/workflows/workflow-status/`

**Purpose:** Central routing and progress tracking system that answers "What should I do now?"

**Multi-Mode Service Architecture:**

| Mode | Purpose | Returns |
|------|---------|---------|
| **Interactive** | "What's next?" guidance | Next workflow, agent, current status |
| **Validate** | Pre-check before workflow runs | should_proceed, warnings |
| **Data** | Extract project config | project_name, type, level, track |
| **Init-Check** | Status file existence | status_exists boolean |
| **Update** | Centralized status updates | success, next_workflow |

**Service Call Pattern:**

Workflows call workflow-status as a service:

```xml
<invoke-workflow path="{project-root}/_personal-automation/life-management/workflows/status-tracker">
  <param>mode: interactive</param>
</invoke-workflow>
```

Returns structured data:
```yaml
current_phase: planning
next_workflow: financial-projections
next_agent: finance-manager
recommendation: "Start financial projections to complete business plan"
```

### Project Initialization (workflow-init)

**Location:** `_bmad/bmm/workflows/workflow-status/init/`

**Purpose:** Smart project setup that detects existing work and recommends appropriate planning track

**Project State Detection:**

Scans for existing work and categorizes:

- **CLEAN** - No artifacts (new project)
- **PLANNING** - Has plans/specs but no execution
- **ACTIVE** - Has milestones or tracking in progress
- **LEGACY** - Has deliverables but no BMM structure
- **UNCLEAR** - Mixed state, needs clarification

**Setup Modes:**

**Express Setup:**
- "I know what I need"
- Quick configuration
- Minimal questions

**Guided Setup:**
- Walks through options
- Explains each choice
- Tailored to skill level

**Project Configuration Steps:**

1. **Determine Project Type**
   - New venture (greenfield)
   - Existing project (brownfield)

2. **Select Planning Track**
   - Quick Flow (minimal planning, 1-3 days)
   - Structured Method (full planning, weeks/months)
   - Enterprise Method (comprehensive planning, months)

3. **Configure Phases**
   - Discovery workflows (research, brainstorming)
   - Planning workflows (business plan, strategy)
   - Execution workflows (milestones, deliverables)

4. **Generate Status File**
   - Creates `project-status.yaml`
   - Lists all workflows with status: required/optional/recommended
   - Tracks completion with file paths

### Status Tracking Files

#### project-status.yaml Structure

**For Business Venture:**

```yaml
generated: "2025-01-15"
project: "SaaS Product Launch"
project_type: "greenfield"
selected_track: "structured"
field_type: "greenfield"
workflow_path: "paths/structured-greenfield.yaml"

workflow_status:
  # Discovery Phase (Optional)
  market-research: optional
  competitive-analysis: optional
  customer-interviews: optional

  # Planning Phase (Required)
  business-plan: required
  financial-projections: required
  go-to-market-strategy: required

  # Execution Phase
  milestone-planning: required
  # (then milestone tracking begins)
```

**Status Values:**
- `required` - Must complete to progress
- `optional` - Can complete but not required
- `recommended` - Strongly suggested
- `conditional` - Required only if certain conditions met
- `{file-path}` - Workflow completed (e.g., "docs/business-plan.md")
- `skipped` - Optional workflow that was intentionally skipped

#### milestone-status.yaml Structure

**For Execution Tracking:**

```yaml
generated: "2025-01-20"
project: "SaaS Product Launch"
project_key: "saas-launch-2025"
tracking_system: "file-system"
milestone_location: "{output_folder}/milestones"

development_status:
  milestone-1-mvp: backlog
  1-1-landing-page: ready-for-dev
  1-2-user-auth: backlog
  1-3-core-feature: backlog
  1-4-payment-integration: backlog
  milestone-1-retrospective: optional

  milestone-2-beta: backlog
  2-1-user-testing: backlog
  2-2-feedback-integration: backlog
  2-3-performance-optimization: backlog

  milestone-3-launch: backlog
  3-1-marketing-campaign: backlog
  3-2-go-live: backlog
  3-3-post-launch-support: backlog
```

**Status Definitions:**
- **Milestone Status**: backlog, in-progress, done
- **Task Status**: backlog, ready-for-dev, in-progress, review, done

### "What Should I Do Now?" Router

**Interactive Mode Flow:**

1. **Check for status file**
   - If missing: Offer to run workflow-init
   - If exists: Parse and analyze

2. **Parse current state**
   - Read workflow_status section
   - Identify completed workflows (status = file path)
   - Identify pending workflows (status = required/optional)
   - Find NEXT workflow (first incomplete required item)

3. **Display current status**

```
## Current Status
Project: SaaS Product Launch (Structured Method)
Track: structured-greenfield

Progress:
Discovery:
- market-research: docs/market-research.md ✓
- competitive-analysis: skipped
- customer-interviews: docs/customer-interviews.md ✓

Planning:
- business-plan: docs/business-plan.md ✓
- financial-projections: required ⏸️
- go-to-market-strategy: required

Execution:
- milestone-planning: required

## Next Steps
Next Workflow: financial-projections
Agent: finance-manager
Command: /workflows:finance:financial-projections
```

4. **Offer actions**
   - Start next workflow
   - Run optional workflow
   - View full status YAML
   - Update workflow status manually
   - Exit

### Professional Applications

#### Business Venture Tracking

**Phases:**

```yaml
# Discovery (Optional but Recommended)
discovery:
  - market-research: optional
  - competitive-analysis: optional
  - customer-interviews: recommended
  - business-model-canvas: required

# Planning (Required)
planning:
  - business-plan: required
  - financial-projections: required
  - go-to-market-strategy: required
  - risk-assessment: recommended

# Pre-Launch (Required)
pre-launch:
  - mvp-development: required
  - beta-testing: required
  - marketing-prep: required
  - operations-setup: required

# Launch (Required)
launch:
  - go-live: required
  - monitoring-setup: required

# Growth (Ongoing)
growth:
  - milestone-planning: required
  # Then iterative milestone tracking
```

**Workflow Router Example:**

```
Current: Business plan completed
Next Options:
  1. financial-projections (required)
  2. risk-assessment (recommended)
  3. go-to-market-strategy (required)

Recommendation: Start financial-projections
Reason: Required for funding discussions, prerequisite for other planning
```

#### Client Engagement Tracking

**Phases:**

```yaml
# Discovery
discovery:
  - kickoff-meeting: required
  - stakeholder-interviews: required
  - current-state-analysis: required

# Planning
planning:
  - problem-analysis: required
  - solution-design: required
  - proposal-generation: required

# Delivery
delivery:
  - deliverable-planning: required
  # Then deliverable tracking

# Closure
closure:
  - final-presentation: required
  - retrospective: required
  - case-study-creation: optional
```

#### Financial Goal Tracking

**Phases:**

```yaml
# Annual Planning
annual:
  - annual-review: required
  - goal-setting: required
  - strategy-definition: required

# Quarterly Check-ins
quarterly:
  - q1-review: required
  - q2-review: required
  - q3-review: required
  - q4-review: required

# Monthly Execution
monthly:
  - budget-planning: required
  # Then monthly budget tracking
```

### Updating Status

**Centralized Update Pattern:**

When a workflow completes:

```xml
<step n="final" goal="Mark Complete">
  <invoke-workflow path="{project-root}/_personal-automation/status-tracker">
    <param>mode: update</param>
    <param>action: complete_workflow</param>
    <param>workflow_id: business-plan</param>
    <param>output_file: docs/business-plan.md</param>
  </invoke-workflow>
</step>
```

**Service returns:**
```yaml
success: true
next_workflow: financial-projections
next_agent: finance-manager
completed_workflow: business-plan
output_file: docs/business-plan.md
```

**Workflow then displays:**
```
✅ Business Plan Complete!

Saved to: docs/business-plan.md
Updated: project-status.yaml

📋 Next Recommended Workflow:
   financial-projections (finance-manager)

This will create 3-year financial projections including:
- Revenue forecasts
- Cost structure
- Cash flow analysis
- Funding requirements
```

### Benefits for Professional Work

1. **Always know what's next** - No guessing about priorities
2. **Flexible planning levels** - Choose ceremony appropriate to complexity
3. **Progress visibility** - See completed vs remaining work
4. **Quality gates** - Required checkpoints before progression
5. **Retrospectives** - Built-in learning opportunities
6. **Context preservation** - Status file tracks all artifacts
7. **Multi-project support** - Different status files for different ventures
8. **Resumability** - Always pick up where you left off

---

## Part 13: Step-File Architecture & Structured Workflows

### Overview

For high-stakes professional deliverables (financial plans, business strategies, client proposals), you need a methodical approach with approval gates. Step-file architecture breaks complex workflows into discrete phases with user checkpoints.

### Micro-File Design Pattern

**Principle:** One step = one file = one phase of work

**Structure:**
```
workflows/finance/annual-financial-plan/
├── workflow.yaml
├── workflow.md              # Orchestrator that loads steps
├── plan-template.md         # Output document template
└── steps/
    ├── step-01-init.md
    ├── step-02-income.md
    ├── step-03-fixed-expenses.md
    ├── step-04-variable-expenses.md
    ├── step-05-investments.md
    ├── step-06-tax-strategy.md
    └── step-07-complete.md
```

**Critical Rules (NO EXCEPTIONS):**

- 🛑 NEVER load multiple step files simultaneously
- 📖 ALWAYS read entire step file before execution
- 🚫 NEVER skip steps or optimize the sequence
- 💾 ALWAYS update frontmatter when writing output
- 📋 NEVER create mental todo lists from future steps

### Just-in-Time Loading

**Pattern:** Only load current step, never pre-load future steps

**Why:**
- Prevents premature optimization
- Enforces sequential execution
- Avoids making assumptions about future work
- Keeps agent focused on current phase

**Loading Flow:**
```
1. Complete current step fully
2. User selects [C] Continue
3. Update frontmatter with completion state
4. Load next step file
5. Read entire file before execution
6. Execute new step
```

**Example from step-01-init.md:**
```markdown
Step-Specific Rules:
- 🎯 Focus only on initialization - no content generation yet
- 🚫 FORBIDDEN to look ahead to future steps
- 📋 No mental todo lists from step descriptions
```

### Append-Only Building

**Pattern:** Documents build incrementally through append operations

**Process:**
1. Step 1: Initialize template with empty frontmatter
2. Step 2: Append "Executive Summary" section
3. Step 3: Append "Income Analysis" section
4. Step 4: Append "Investment Strategy" section
5. ...continues building section by section

**Benefits:**
- Clear audit trail of additions
- No accidental overwrites
- Supports resume/continuation
- Each step adds value incrementally

**Example:**

After Step 2:
```markdown
---
stepsCompleted: [1, 2]
lastStep: 2
---

# Annual Financial Plan 2025

## Executive Summary
[Content from step 2]
```

After Step 3:
```markdown
---
stepsCompleted: [1, 2, 3]
lastStep: 3
---

# Annual Financial Plan 2025

## Executive Summary
[Content from step 2]

## Income Analysis
[Content from step 3]
```

### State Tracking in Frontmatter

**YAML frontmatter tracks workflow state:**

```yaml
---
stepsCompleted: [1, 2, 3]
inputDocuments:
  - previous-year-plan.md
  - tax-returns-2024.pdf
documentCounts:
  previous_plans: 1
  tax_docs: 1
workflowType: 'annual-financial-plan'
lastStep: 3
project_name: 'Annual Financial Planning 2025'
user_name: 'Rodrigo'
date: '2025-01-15'
---
```

**Resume Logic:**

When resuming workflow:
```
If lastStep = 1 → Load step-02-income.md
If lastStep = 2 → Load step-03-fixed-expenses.md
If lastStep = 3 → Load step-04-variable-expenses.md
...
If lastStep = 7 → Workflow complete
```

### Template-Output Checkpoints

**A/P/C Menu Pattern:**

At each major section, present user with:
- **[A] Advanced Elicitation** - Use 50+ analytical methods to enhance content
- **[P] Party Mode** - Bring in other agents for multi-perspective input
- **[C] Continue** - Save content and proceed to next step

**Example from step-03-fixed-expenses.md:**

```markdown
### Present Content and Menu

"I've analyzed your fixed expenses and created a breakdown..."

[Display generated fixed expenses breakdown]

**Select an Option:**
[A] Advanced Elicitation - enhance with methods like Pre-mortem, 5 Whys
[P] Party Mode - get perspectives from other agents
[C] Continue - save and move to Variable Expenses (Step 4 of 7)
```

**User Approval Gates:**

- Content only saves when user selects [C]
- [A] and [P] enhance content, then return to same menu
- Forces deliberate review of each section
- Prevents runaway AI generation

### YOLO Mode

**Alternative to Interactive:**

At first template-output checkpoint, user can select:
- **[Y] YOLO Mode** - Complete remainder of workflow autonomously

**YOLO Behavior:**
- Skips all remaining confirmations
- Minimizes prompts
- Simulates expert user responses
- Generates entire workflow automatically

**Use Cases:**
- Routine monthly budgets
- Standard client reports
- Familiar annual reviews
- Time-sensitive deliverables

### Professional Applications

#### Financial Planning Workflow

**Annual Financial Plan (7 Steps):**

**Step 1: Initialize**
- Load previous year's plan
- Set up template with frontmatter
- Confirm goals for new year

**Step 2: Income Analysis** → [A/P/C]
- Salary and bonuses
- Side income sources
- Passive income
- Expected changes
- **Checkpoint:** Review income projections

**Step 3: Fixed Expenses Review** → [A/P/C]
- Housing (rent/mortgage)
- Utilities
- Insurance premiums
- Subscriptions
- Loan payments
- **Checkpoint:** Validate fixed costs

**Step 4: Variable Expense Allocation** → [A/P/C]
- Groceries budget
- Entertainment
- Dining out
- Shopping
- Travel
- **Checkpoint:** Approve spending allocations

**Step 5: Investment Strategy** → [A/P/C]
- Asset allocation
- Retirement contributions
- Taxable investments
- Real estate plans
- **Checkpoint:** Confirm investment approach

**Step 6: Tax Optimization** → [A/P/C]
- Deduction strategies
- Tax-loss harvesting
- Retirement account optimization
- Charitable giving
- **Checkpoint:** Review tax strategies

**Step 7: Complete**
- Final review of entire plan
- Set quarterly review dates
- Update project status
- Celebrate completion!

#### Client Proposal Workflow

**Consulting Proposal (6 Steps):**

**Step 1: Initialize**
- Load discovery notes
- Set up proposal template
- Confirm scope

**Step 2: Discovery Summary** → [A/P/C]
- Client situation
- Pain points identified
- Goals and objectives
- **Checkpoint:** Validate understanding

**Step 3: Problem Statement** → [A/P/C]
- Root cause analysis
- Impact quantification
- Urgency assessment
- **Checkpoint:** Confirm problem framing

**Step 4: Proposed Solution** → [A/P/C]
- Approach and methodology
- Deliverables breakdown
- Success criteria
- **Checkpoint:** Approve solution design

**Step 5: Pricing & Timeline** → [A/P/C]
- Fee structure
- Payment terms
- Project timeline
- Milestones
- **Checkpoint:** Review commercials

**Step 6: Risk Mitigation & Next Steps** → [A/P/C]
- Risk factors
- Mitigation strategies
- Assumptions
- Next steps
- **Checkpoint:** Final proposal review

#### Strategic Business Plan Workflow

**Business Plan for New Venture (8 Steps):**

**Step 1: Initialize**
- Load market research
- Set up plan template
- Define venture scope

**Step 2: Executive Summary** → [A/P/C]
- Vision and mission
- Market opportunity
- Unique value proposition
- Financial highlights
- Funding ask
- **Checkpoint:** Review exec summary

**Step 3: Market Analysis** → [A/P/C]
- Market size (TAM/SAM/SOM)
- Trends and drivers
- Customer segments
- Pain points
- **Checkpoint:** Validate market understanding

**Step 4: Competitive Landscape** → [A/P/C]
- Direct competitors
- Indirect competitors
- Differentiation
- Barriers to entry
- **Checkpoint:** Confirm competitive positioning

**Step 5: Product Overview** → [A/P/C]
- Features and benefits
- Product roadmap
- Technology stack
- IP and defensibility
- **Checkpoint:** Review product strategy

**Step 6: Business Model** → [A/P/C]
- Revenue streams
- Pricing strategy
- Unit economics
- Sales channels
- **Checkpoint:** Validate business model

**Step 7: Go-to-Market** → [A/P/C]
- Marketing strategy
- Sales approach
- Customer acquisition
- Partnerships
- **Checkpoint:** Approve GTM plan

**Step 8: Financial Projections & Milestones** → [A/P/C]
- 3-year forecast
- Key assumptions
- Funding requirements
- Use of funds
- Milestones
- **Checkpoint:** Final plan review

### Example Step File Structure

**Step 02: Income Analysis**

```markdown
---
step: 2
title: "Income Analysis"
goal: "Document all income sources and projections"
prerequisites: ["step-01-init"]
---

# Step 2: Income Analysis

## Goal
Comprehensively document all current and projected income sources for the year.

## Prerequisites Check
- [ ] Template initialized (step 1 complete)
- [ ] Previous year's plan loaded (if available)
- [ ] User ready to discuss income

## Instructions

### 1. Load Context
- Read {previous_plan} if available
- Note last year's income sources
- Identify changes since last year

### 2. Primary Income Discovery
<ask>
What is your primary employment income for this year?
- Salary: $
- Expected bonuses: $
- Expected raises: $
</ask>

### 3. Secondary Income Discovery
<ask>
Do you have additional income sources?
- Freelance/consulting: $
- Side business: $
- Rental income: $
- Investment income: $
- Other: $
</ask>

### 4. Income Projections
<action>
Calculate total projected income:
- Monthly: ${total / 12}
- Quarterly: ${total / 4}
- Annual: ${total}
</action>

### 5. Generate Income Analysis Section
<template-output section="income-analysis">

## Income Analysis

### Primary Income
- **Salary**: $X from [Company Name]
- **Bonuses**: $Y (expected [timing])
- **Total Primary**: $Z

### Secondary Income
- **Freelancing**: $A ([number] clients)
- **Side Business**: $B ([business name])
- **Investments**: $C (dividends, interest)
- **Total Secondary**: $D

### Annual Projections
- **Total Annual Income**: $[Z + D]
- **Monthly Average**: $[total / 12]
- **After-Tax Estimate**: $[total * 0.75] (assuming 25% effective rate)

### Year-over-Year Comparison
- 2024 Income: $[previous]
- 2025 Income: $[current]
- **Change**: +$[difference] (+[percentage]%)

### Key Assumptions
- Salary remains stable
- Bonus payout at [percentage]% of target
- Freelance client retention at [percentage]%
- No major career changes

</template-output>

### 6. Present Content and Menu

"I've analyzed your income sources and projections for 2025..."

[Display generated income analysis]

**Select an Option:**
[A] Advanced Elicitation - dig deeper with methods like:
    - First Principles: Challenge income assumptions
    - Pre-mortem: What if income drops?
    - 5 Whys: Understand income growth strategies
[P] Party Mode - get perspectives from career advisor, tax specialist
[C] Continue - save and move to Fixed Expenses (Step 3 of 7)

### 7. Handle User Selection

**If A (Advanced Elicitation):**
- Execute advanced-elicitation.xml
- Return to step 6 menu with enhanced content

**If P (Party Mode):**
- Execute party-mode workflow
- Agents discuss income strategy
- Return to step 6 menu with insights

**If C (Continue):**
- Update frontmatter: stepsCompleted: [1, 2], lastStep: 2
- Save document
- Load step-03-fixed-expenses.md

## Step Completion
✅ Income analysis documented
✅ Projections validated
✅ Saved to output file
➡️  Ready for step 3
```

### Benefits for Professional Work

1. **Methodical Approach** - No rushing through important decisions
2. **Quality Gates** - Review each section before proceeding
3. **Enhancement Options** - A/P available at every checkpoint
4. **Clear Progress** - Know exactly where you are (Step 3 of 7)
5. **Resumable** - Can stop and resume at any step
6. **Audit Trail** - Frontmatter tracks all inputs and completions
7. **Flexibility** - YOLO mode when appropriate
8. **Professional Polish** - Multiple review opportunities ensure quality

---

## Part 14: Advanced Elicitation for Professional Quality

### Overview

Advanced Elicitation is a meta-cognitive enhancement protocol with 50+ analytical methods that deepen content quality through multiple perspectives. It's available at any template-output checkpoint via the [A] option.

### What Is Advanced Elicitation?

**Location:** `_bmad/core/tasks/advanced-elicitation.xml`

**Purpose:** Challenge and enhance generated content using diverse analytical methods from various domains (business, technical, creative, risk analysis, etc.)

**How It Works:**
1. Available at any A/P/C checkpoint
2. Smart-selects 5 contextually relevant methods
3. Presents interactive menu
4. User selects method to apply
5. AI applies method to enhance content
6. User reviews and approves/rejects changes
7. Loops until user exits
8. Returns enhanced content to workflow

### Method Categories (50 Methods)

**Collaboration (10 methods):**
- Stakeholder Round Table - Multiple stakeholder perspectives
- Expert Panel - Domain expert consultation
- Debate Club - Pros vs cons discussion
- Peer Review Circle - Colleague feedback simulation
- User Focus Group - End-user perspective

**Advanced (6 methods):**
- Tree of Thoughts - Explore decision branches
- Graph of Thoughts - Map interconnected ideas
- Self-Consistency - Multiple reasoning paths
- Chain of Thought - Step-by-step reasoning
- Least-to-Most - Build from simple to complex

**Competitive (3 methods):**
- Red Team vs Blue Team - Attack/defend strategies
- Shark Tank Pitch - Tough investor questions
- Dragon's Den - Critical business evaluation

**Technical (5 methods):**
- Architecture Decision Records - Document tech choices
- Algorithm Olympics - Compare approaches
- Code Review Simulation - Quality assessment
- Technical Debt Assessment - Identify shortcuts
- Performance Profiling - Optimization opportunities

**Creative (6 methods):**
- SCAMPER - Substitute, Combine, Adapt, Modify, Put to other use, Eliminate, Reverse
- What-If Scenarios - Explore alternatives
- Reverse Engineering - Work backwards from goal
- Random Word Association - Creative connections
- Morphological Analysis - Systematic combinations
- Analogy Thinking - Transfer solutions from other domains

**Research (3 methods):**
- Literature Review - Survey existing knowledge
- Comparative Analysis - Benchmark alternatives
- Thesis Defense - Challenge assumptions

**Risk (5 methods):**
- Pre-mortem Analysis - Imagine failure, work backwards
- Failure Mode Analysis - Identify potential failures
- Risk-Reward Matrix - Evaluate trade-offs
- Scenario Planning - Plan for different futures
- Black Swan Analysis - Prepare for unlikely events

**Core (6 methods):**
- First Principles - Break down to fundamental truths
- 5 Whys - Dig into root causes
- Socratic Questioning - Challenge assumptions
- Devil's Advocate - Argue against position
- Lateral Thinking - Unconventional approaches
- Systems Thinking - Understand interconnections

**Retrospective (2 methods):**
- Hindsight Reflection - Learn from past
- Lessons Learned - Extract key takeaways

**Philosophical (2 methods):**
- Occam's Razor - Simplest explanation wins
- Trolley Problem - Ethical dilemmas

**Learning (2 methods):**
- Feynman Technique - Explain simply
- Active Recall - Test understanding

### Interactive Menu

When user selects [A] at checkpoint:

```
Advanced Elicitation - 50+ Methods

Recommended for your content:
[1] Pre-mortem Analysis - Imagine failure scenarios
[2] First Principles - Challenge fundamental assumptions
[3] Stakeholder Round Table - Multiple perspectives
[4] SCAMPER - Creative innovation techniques
[5] 5 Whys - Dig into root causes

Other Options:
[r] Reshuffle recommendations
[a] List all 50 methods
[x] Exit back to workflow

Select method (1-5, r, a, or x):
```

### Professional Applications

#### Financial Planning Enhancement

**Original Budget Allocation:**
```markdown
### Retirement Savings
- Contribute 15% of income to 401(k)
- Target: $500,000 by age 65
```

**After Pre-mortem Analysis (Method: Risk):**
```markdown
### Retirement Savings Strategy

**Target:**
- Contribute 15% of income to 401(k)
- Goal: $500,000 by age 65 (age 35 now, 30 years)

**Pre-mortem: What if this fails?**

Failure Scenarios:
1. **Market Downturn** - Portfolio loses 40% value in crash
   - Mitigation: Dollar-cost averaging, diversification across asset classes

2. **Job Loss** - Contributions pause for 6-12 months
   - Mitigation: 6-month emergency fund fully funded first
   - Have Roth IRA as secondary savings vehicle (withdraw contributions anytime)

3. **Health Crisis** - Early retirement forced
   - Mitigation: Disability insurance coverage
   - Health Savings Account (HSA) as triple tax-advantaged backup

4. **Lifestyle Inflation** - Spending increases prevent 15% savings
   - Mitigation: Automate transfers on payday (before seeing money)
   - Annual raises: 50% to retirement, 50% to lifestyle

5. **Underperformance** - Returns lower than projected 7%
   - Mitigation: Conservative 6% assumption in planning
   - Annual review and rebalancing
   - Consider increasing contribution rate if behind

**Adjusted Strategy:**
- Priority 1: Build 6-month emergency fund
- Priority 2: Max employer 401(k) match (free money)
- Priority 3: HSA contributions (triple tax advantage)
- Priority 4: Roth IRA (flexibility)
- Priority 5: Additional 401(k) beyond match
```

#### Business Strategy Enhancement

**Original Go-to-Market Plan:**
```markdown
### Customer Acquisition
- Content marketing (blog, SEO)
- Social media presence
- Paid advertising on Google/Facebook
```

**After Red Team vs Blue Team (Method: Competitive):**
```markdown
### Customer Acquisition Strategy

**Blue Team (Our Plan):**
- Content marketing (blog, SEO) for organic growth
- Social media presence to build community
- Paid advertising for immediate reach

**Red Team (Attack):**
- "Content marketing takes 6-12 months to gain traction. You'll run out of runway before seeing results."
- "Every competitor is doing social media. How will you differentiate?"
- "Paid advertising is expensive for B2B SaaS. CAC could exceed LTV."

**Blue Team (Defense & Adaptation):**

**Content Marketing - Enhanced:**
- Start with guest posts on established industry blogs (immediate audience access)
- Focus on bottom-of-funnel content first (buyers, not browsers)
- Repurpose every piece across 5 channels (blog → LinkedIn → Twitter → email → video)
- Partner with micro-influencers for amplification

**Social Media - Differentiated:**
- Instead of generic posts: Share real customer stories with metrics
- Weekly AMAs with founders (build trust + authority)
- Create proprietary research reports (shareable, link-worthy)
- Engage in relevant communities, don't just broadcast

**Paid Advertising - Optimized:**
- Start with retargeting only (lower cost, higher intent)
- Test LinkedIn ads for precise B2B targeting
- Cap initial spend at $2K/month until CAC:LTV ratio proven
- Focus on high-intent keywords (comparison, alternative searches)

**Additional Channels Identified:**
- Strategic partnerships with complementary tools
- Referral program for early customers (lower CAC)
- Product-led growth: Free tier as acquisition engine
- Conference speaking for credibility + leads
```

#### Client Proposal Enhancement

**Original Problem Statement:**
```markdown
### Problem
The client's sales team is underperforming and missing quotas.
```

**After 5 Whys (Method: Core):**
```markdown
### Root Cause Analysis

**Surface Problem:**
Sales team missing quotas (averaging 60% of target)

**5 Whys Analysis:**

1. **Why are they missing quotas?**
   → Reps aren't generating enough qualified leads

2. **Why aren't they generating qualified leads?**
   → They're spending 70% of time on unqualified prospects

3. **Why are they targeting unqualified prospects?**
   → No clear ideal customer profile (ICP) defined

4. **Why is there no clear ICP?**
   → Marketing and sales teams operate in silos with different definitions of "qualified"

5. **Why do they operate in silos?**
   → No shared CRM data, different KPIs, separate meetings

**Root Cause:**
Organizational misalignment between marketing and sales creates inefficient lead qualification, resulting in wasted sales effort on poor-fit prospects.

**Impact Quantification:**
- 70% of sales time wasted = $420K in annual salary cost (6 reps × $70K × 0.7)
- Lost opportunity cost: If redirected to qualified leads, could close 40% more deals
- At $50K average deal size: $1.2M additional annual revenue

**True Problem Statement:**
Misalignment between marketing and sales teams costs the company $1.6M annually ($420K wasted + $1.2M lost revenue) due to lack of shared ICP definition and unified lead qualification process.

This reveals the proposal should focus on:
1. Marketing-sales alignment (not just sales training)
2. Unified ICP development
3. Shared CRM and qualification criteria
4. Joint KPIs and accountability
```

### Example Elicitation Session

**Context:** Annual financial plan, Investment Strategy section

**User selects:** [A] Advanced Elicitation

**System presents:** 5 smart-selected methods
- [1] Pre-mortem Analysis
- [2] First Principles
- [3] 5 Whys
- [4] Black Swan Analysis
- [5] Comparative Analysis

**User selects:** [1] Pre-mortem Analysis

**AI applies method:**
```
Pre-mortem: Imagine it's December 2025 and your investment strategy failed spectacularly.
What happened?

Failure Scenario 1: Concentrated risk
- Had 60% in tech stocks
- Tech sector crashed -40%
- Portfolio lost $120K

Prevention:
- Diversify across sectors (tech max 30%)
- Add bonds, international, real estate
- Target correlation < 0.7 between asset classes
```

**System asks:** "Apply these enhancements? (y/n)"

**User:** y

**Content enhanced, returns to menu**

**User selects:** [4] Black Swan Analysis

**AI applies method:**
```
Black Swan Analysis: Low probability, high impact events

Unlikely Events to Prepare For:

1. **Hyperinflation** (< 5% probability, extreme impact)
   - Current plan assumes 2-3% inflation
   - If 10%+ inflation: Real returns devastated
   - Hedge: 5-10% gold/commodities, I-Bonds, TIPS

2. **Prolonged Market Depression** (< 10% probability)
   - Current plan assumes recovery within 3 years
   - If Japan-style lost decade: Retirement delayed 5-10 years
   - Hedge: Skills development, side income diversification

3. **Personal Health Crisis** (15% probability over 30 years)
   - Disability, chronic illness forcing early retirement
   - Hedge: Disability insurance, HSA max funding, accessible savings

4. **Currency Crisis** (< 5% probability)
   - Dollar loses reserve status
   - Hedge: 10-15% international investments, hard assets
```

**System asks:** "Apply these enhancements? (y/n)"

**User:** y

**User selects:** [x] Exit

**Returns to workflow with enhanced content**

### Benefits for Professional Work

1. **Deeper Analysis** - 50 methods ensure thorough examination
2. **Multiple Perspectives** - See content through different lenses
3. **Risk Identification** - Uncover hidden vulnerabilities
4. **Creative Enhancement** - Generate innovative approaches
5. **Quality Assurance** - Challenge assumptions systematically
6. **Stakeholder Alignment** - Consider diverse viewpoints
7. **Professional Polish** - Elevate content quality significantly

---

## Part 15: Validation & Quality Assurance Patterns

### Overview

Professional deliverables require validation checklists to ensure completeness, accuracy, and quality before finalization.

### Definition of Done Checklists

Each workflow type has specific validation criteria:

#### Financial Plan Validation

```markdown
## Financial Plan - Definition of Done

### Income Documentation
- [ ] All primary income sources documented with amounts
- [ ] Secondary income sources identified
- [ ] Income projections include growth assumptions
- [ ] After-tax income calculated
- [ ] Year-over-year comparison shown

### Expense Analysis
- [ ] Fixed expenses categorized and totaled
- [ ] Variable expenses budgeted by category
- [ ] Expenses as % of income calculated
- [ ] Fixed expenses under 50% of income (or justified if higher)
- [ ] Discretionary spending identified

### Savings & Investment
- [ ] Emergency fund target defined (3-6 months expenses)
- [ ] Retirement savings rate specified (minimum 15%)
- [ ] Investment allocation matches risk tolerance
- [ ] Asset diversification across classes
- [ ] Rebalancing strategy defined

### Tax Optimization
- [ ] Available deductions identified
- [ ] Tax-advantaged accounts maximized
- [ ] Tax-loss harvesting strategy documented
- [ ] Estimated tax liability calculated

### Risk Management
- [ ] Insurance coverage reviewed (life, disability, health)
- [ ] Estate planning documents current
- [ ] Beneficiaries updated
- [ ] Risk scenarios considered (job loss, health crisis)

### Implementation
- [ ] Action items list with deadlines
- [ ] Quarterly review dates scheduled
- [ ] Accountability measures defined
- [ ] Success metrics specified
```

#### Client Proposal Validation

```markdown
## Client Proposal - Definition of Done

### Discovery & Understanding
- [ ] Problem statement aligns with discovery notes
- [ ] Root cause analysis documented
- [ ] Impact quantified (financial, operational, strategic)
- [ ] Client's goals and success criteria captured

### Solution Design
- [ ] Proposed approach clearly described
- [ ] Methodology explained (why this approach?)
- [ ] Deliverables list is specific and measurable
- [ ] Timeline is realistic with milestones
- [ ] Success criteria are measurable

### Commercial Terms
- [ ] Pricing justified with value proposition
- [ ] Payment terms clearly stated
- [ ] Expenses and out-of-pocket costs noted
- [ ] Scope boundaries defined (what's excluded)
- [ ] Change order process described

### Risk & Assumptions
- [ ] Key assumptions documented
- [ ] Risk factors identified
- [ ] Mitigation strategies proposed
- [ ] Dependencies on client noted
- [ ] Contingency plans outlined

### Professional Quality
- [ ] No spelling/grammar errors
- [ ] Consistent formatting throughout
- [ ] Professional tone maintained
- [ ] Client-specific customization (not template language)
- [ ] Contact information current

### Legal & Compliance
- [ ] Legal terms reviewed (if applicable)
- [ ] Confidentiality provisions included
- [ ] IP ownership addressed
- [ ] Liability limitations stated
- [ ] Governing law specified
```

#### Business Plan Validation

```markdown
## Business Plan - Definition of Done

### Market Analysis
- [ ] Market size data-driven with sources cited
- [ ] TAM/SAM/SOM calculations shown
- [ ] Market trends supported with evidence
- [ ] Customer segments clearly defined
- [ ] Pain points validated with research

### Competitive Analysis
- [ ] Direct competitors identified (minimum 3)
- [ ] Indirect competitors considered
- [ ] Competitive differentiation clear and defensible
- [ ] Barriers to entry analyzed
- [ ] Competitive response anticipated

### Business Model
- [ ] Revenue streams clearly defined
- [ ] Pricing strategy justified
- [ ] Unit economics calculated (CAC, LTV, margins)
- [ ] Revenue model validated (not just assumptions)
- [ ] Path to profitability shown

### Financial Projections
- [ ] 3-year forecast included (minimum)
- [ ] Revenue assumptions documented and justified
- [ ] Cost structure detailed by category
- [ ] Cash flow projection included
- [ ] Funding requirements specified with use of funds
- [ ] Key financial metrics tracked (burn rate, runway, break-even)

### Go-to-Market
- [ ] Marketing strategy specific (not generic)
- [ ] Sales approach defined with CAC targets
- [ ] Customer acquisition channels prioritized
- [ ] Partnership strategy outlined
- [ ] Launch timeline with milestones

### Team & Execution
- [ ] Founder backgrounds relevant to venture
- [ ] Key roles identified with hiring timeline
- [ ] Advisory board or mentors named
- [ ] Gaps in team acknowledged with mitigation
- [ ] Org chart for year 1-3 shown

### Risk Assessment
- [ ] Major risks identified (market, execution, financial, competitive)
- [ ] Mitigation strategies for each risk
- [ ] Contingency plans outlined
- [ ] Assumptions tested with sensitivity analysis
- [ ] Exit strategy considered
```

### Implementation Readiness Checks

Before executing on any plan:

```markdown
## Implementation Readiness Checklist

### Prerequisites
- [ ] All required planning documents completed
- [ ] Stakeholders reviewed and approved plan
- [ ] Budget allocated and approved
- [ ] Resources available (people, tools, time)
- [ ] Dependencies identified and addressed

### Execution Preparation
- [ ] Action items list created with owners
- [ ] Timeline with milestones defined
- [ ] Success metrics and KPIs established
- [ ] Monitoring and reporting cadence set
- [ ] Risk mitigation plans activated

### Communication
- [ ] Key stakeholders identified and informed
- [ ] Communication plan established
- [ ] Escalation paths defined
- [ ] Status reporting format agreed

### Contingency
- [ ] Alternative approaches considered
- [ ] Rollback plan if needed
- [ ] Emergency contacts identified
- [ ] Decision authority clarified
```

### Validation Workflow Pattern

**Example from workflow completion:**

```xml
<step n="final" goal="Validate & Complete">
  <!-- 1. Load validation checklist -->
  <action>Read {installed_path}/checklist.md</action>

  <!-- 2. Review against checklist -->
  <action>Review generated document against each checklist item</action>

  <!-- 3. Identify gaps -->
  <check if="gaps_found">
    <ask>The following items are incomplete:
    - [ ] Item 1
    - [ ] Item 2

    Options:
    [f] Fix now (go back and complete)
    [a] Accept gaps (document as known limitations)
    [c] Cancel (don't finalize yet)
    </ask>
  </check>

  <!-- 4. Final approval -->
  <ask>
  All validation items checked. Ready to finalize?
  [y] Yes - mark complete and update status
  [r] Review - show full checklist results
  [e] Enhance - run Advanced Elicitation on weak sections
  </ask>
</step>
```

### Benefits for Professional Work

1. **Completeness** - Nothing important overlooked
2. **Consistency** - Same quality standards every time
3. **Confidence** - Know deliverable meets criteria
4. **Professional Standards** - Industry best practices applied
5. **Risk Mitigation** - Gaps identified before they become problems
6. **Stakeholder Trust** - Thorough validation demonstrates diligence

---

## Part 16: Index Generation & Knowledge Organization

### Overview

As professional documentation grows, organization becomes critical. The index-docs workflow generates navigable index files for document libraries.

### The index-docs Workflow

**Location:** `_bmad/core/tasks/index-docs.xml`

**Purpose:** Generate or update `index.md` file for directory with links and descriptions

**Process:**

1. **Scan Directory** - List all files and subdirectories
2. **Read Files** - Open each file to understand actual content
3. **Generate Descriptions** - Create brief (3-10 word) summaries based on content
4. **Organize** - Group by type, purpose, or category
5. **Create Index** - Write formatted index.md with links

**Critical Rule:**
> Read file contents to generate accurate descriptions - don't guess from filenames

### Professional Applications

#### Financial Document Library

```markdown
# Financial Planning Library
*Last Updated: 2025-01-15*

## Annual Plans
- [2025 Financial Plan](./2025-financial-plan/) - Complete annual strategy with investment allocation
- [2024 Financial Plan](./2024-financial-plan/) - Prior year for comparison and learning
- [2023 Financial Plan](./2023-financial-plan/) - Historical reference

## Investment Strategy
- [Portfolio Allocation Strategy](./portfolio-allocation.md) - Asset class targets and rebalancing rules
- [Tax-Loss Harvesting Guide](./tax-loss-harvesting.md) - Annual tax optimization workflow
- [Retirement Account Optimization](./retirement-accounts.md) - 401k, IRA, HSA strategies

## Monthly Budgets

### 2025
- [January 2025](./budgets/budget-january-2025.md) - First month with new savings targets
- [February 2025](./budgets/budget-february-2025.md) - Adjusted for annual bonus allocation

### 2024 Archive
- [2024 Budget Archive](./budgets/2024/) - Complete year for analysis

## Tax Planning
- [2024 Tax Return Analysis](./taxes/2024-tax-analysis.md) - Deductions claimed and optimization opportunities
- [Estimated Tax Payments 2025](./taxes/estimated-payments-2025.md) - Quarterly payment schedule

## Knowledge Base
- [Investment Principles](./kb/investment-principles.md) - Core beliefs and decision framework
- [Financial Independence Strategy](./kb/fi-strategy.md) - Path to FI with milestones
- [Real Estate Investing Notes](./kb/real-estate.md) - Rental property and REIT strategies

---

## Quick Navigation

**For Monthly Budget Reviews:** Start with current month in Budgets section
**For Annual Planning:** Reference last year's Annual Plan + Portfolio Allocation
**For Tax Season:** Tax Planning section + relevant Annual Plan
**For Investment Decisions:** Knowledge Base + Portfolio Allocation Strategy
```

#### Client Project Archives

```markdown
# Client Project Archive
*Last Updated: 2025-01-15*

## Active Engagements

### Acme Corporation
**Engagement:** Q1 2025 Digital Transformation Strategy
**Status:** In Progress - Discovery Phase Complete

- [Discovery Notes](./clients/acme-corp/discovery-2025.md) - Stakeholder interviews and current state
- [Proposal](./clients/acme-corp/proposal-2025.md) - Scope, timeline, pricing (approved)
- [Project Plan](./clients/acme-corp/project-plan-2025.md) - Detailed milestone breakdown

### Beta Inc
**Engagement:** Ongoing Strategic Advisory (Retainer)
**Status:** Active - Monthly Strategy Sessions

- [Retainer Agreement](./clients/beta-inc/retainer-2025.md) - Terms and deliverables
- [Monthly Reports](./clients/beta-inc/reports/) - Strategy session summaries

## Completed Projects (2024)

### Gamma LLC
**Engagement:** Market Entry Strategy
**Completed:** December 2024
**Outcome:** Successful - Client launched in new market Q1 2025

- [Final Deliverable](./clients/gamma-llc/market-entry-strategy.md) - Complete strategy document
- [Case Study](./clients/gamma-llc/case-study.md) - Results and learnings (approved for portfolio)
- [Retrospective](./clients/gamma-llc/retrospective.md) - Internal lessons learned

### Delta Co
**Engagement:** Sales Process Optimization
**Completed:** October 2024
**Outcome:** Exceeded Goals - 40% improvement in conversion

- [Final Report](./clients/delta-co/sales-optimization-report.md) - Analysis and recommendations
- [Implementation Guide](./clients/delta-co/implementation-guide.md) - Handed to client team
- [Testimonial](./clients/delta-co/testimonial.md) - Client feedback (LinkedIn approved)

## Templates & Resources

### Proposal Templates
- [Standard Consulting Proposal](./templates/proposal-template.md) - Base template for new engagements
- [Strategic Advisory Retainer](./templates/retainer-template.md) - Monthly retainer structure

### Deliverable Templates
- [Market Analysis Template](./templates/market-analysis.md) - Standard framework
- [Strategy Document Template](./templates/strategy-template.md) - Executive summary + recommendations

### Process Documents
- [Discovery Interview Guide](./resources/discovery-guide.md) - Standard questions by domain
- [Retrospective Template](./resources/retrospective-template.md) - Post-project review structure

---

## Quick Navigation

**Starting New Client:** Use Proposal Templates
**During Discovery:** Reference Discovery Interview Guide
**Creating Deliverables:** Use relevant template from Deliverable Templates
**Project Complete:** Run Retrospective Template, consider Case Study
**Finding Similar Work:** Search Completed Projects by engagement type
```

#### Personal Knowledge Base

```markdown
# Knowledge Base Index
*Your Second Brain*
*Last Updated: 2025-01-15*

## Business & Entrepreneurship

### Marketing
- [Content Marketing Playbook](./business/marketing/content-marketing.md) - SEO, blogging, distribution strategies
- [Social Media Strategy](./business/marketing/social-media.md) - Platform-specific tactics and scheduling
- [Email Marketing Guide](./business/marketing/email-marketing.md) - List building, segmentation, campaigns

### Sales
- [Consultative Selling Framework](./business/sales/consultative-selling.md) - Discovery-based sales approach
- [Objection Handling](./business/sales/objection-handling.md) - Common objections with responses
- [Closing Techniques](./business/sales/closing-techniques.md) - Trial closes and commitment patterns

### Product Development
- [Lean Startup Methodology](./business/product/lean-startup.md) - Build-Measure-Learn cycle
- [Product-Market Fit](./business/product/pmf.md) - How to know when you have it
- [Pricing Strategies](./business/product/pricing.md) - Value-based, cost-plus, competitive

## Finance & Investing

### Personal Finance
- [Emergency Fund Strategy](./finance/personal/emergency-fund.md) - How much, where to keep it
- [Debt Payoff Methods](./finance/personal/debt-payoff.md) - Avalanche vs Snowball with examples
- [Credit Score Optimization](./finance/personal/credit-score.md) - Factors and improvement tactics

### Investing
- [Index Fund Investing](./finance/investing/index-funds.md) - Bogleheads philosophy and implementation
- [Real Estate Investing](./finance/investing/real-estate.md) - Rental properties, REITs, flipping
- [Cryptocurrency Basics](./finance/investing/crypto.md) - Bitcoin, Ethereum, portfolio allocation

### Tax Strategy
- [Tax-Loss Harvesting](./finance/tax/tax-loss-harvesting.md) - Annual workflow and wash sale rules
- [Retirement Account Optimization](./finance/tax/retirement-accounts.md) - 401k vs IRA vs Roth strategy
- [Small Business Tax Deductions](./finance/tax/business-deductions.md) - Common deductions for consultants

## Technology

### Programming
- [Python Best Practices](./tech/programming/python-best-practices.md) - Code style, testing, documentation
- [API Design Principles](./tech/programming/api-design.md) - REST, GraphQL, versioning
- [Database Design](./tech/programming/database-design.md) - Normalization, indexing, performance

### Tools & Productivity
- [CLI Tools](./tech/tools/cli-tools.md) - Favorite command-line utilities
- [VS Code Setup](./tech/tools/vscode-setup.md) - Extensions and configuration
- [Git Workflows](./tech/tools/git-workflows.md) - Branching strategies and best practices

## Professional Development

### Career
- [Networking Strategy](./career/networking.md) - Building relationships and maintaining connections
- [Personal Branding](./career/personal-branding.md) - LinkedIn, Twitter, blogging for visibility
- [Salary Negotiation](./career/salary-negotiation.md) - Research, timing, tactics

### Skills
- [Learning How to Learn](./skills/learning.md) - Effective study techniques and retention
- [Public Speaking](./skills/public-speaking.md) - Preparation, delivery, handling nerves
- [Writing Clearly](./skills/writing.md) - Structure, clarity, editing process

---

## Quick Navigation by Use Case

**Starting New Business:** Business → Product Development + Marketing + Sales
**Improving Finances:** Finance → Personal Finance + Tax Strategy
**Learning to Code:** Technology → Programming
**Career Advancement:** Professional Development → Career + Skills
**Investment Decisions:** Finance → Investing + Tax Strategy
```

### Index Generation Workflow

```yaml
# workflows/utilities/generate-index/workflow.yaml
name: generate-index
description: "Generate or update index.md for directory"

target_directory: ""  # Will prompt user

installed_path: "{project-root}/_personal-automation/utilities/generate-index"
instructions: "{installed_path}/instructions.xml"
```

```xml
<!-- instructions.xml -->
<instructions>
  <step n="1" goal="Scan Directory">
    <ask>Which directory should I index?</ask>
    <action>List all files and subdirectories in {target_directory}</action>
    <action>Exclude hidden files (.* files) and system files</action>
  </step>

  <step n="2" goal="Read and Understand Files">
    <action>For each markdown file:</action>
    <action>- Read first 200 lines or complete file if shorter</action>
    <action>- Extract title (# heading or filename)</action>
    <action>- Generate 3-10 word description based on content</action>
    <action>- Note last modified date</action>
  </step>

  <step n="3" goal="Organize Content">
    <action>Group files by:</action>
    <action>- Subdirectory (if exists)</action>
    <action>- Type (plans, reports, templates, etc.)</action>
    <action>- Date (if chronological like budgets)</action>
    <action>Sort within groups alphabetically or chronologically</action>
  </step>

  <step n="4" goal="Generate Index">
    <template-output>
# {Directory Name} Index
*Last Updated: {date}*

{for each group}
## {Group Name}

{for each file in group}
- [{File Title}](./{relative_path}) - {Description}
{end for}
{end for}

---

## Quick Navigation
{Smart suggestions based on directory type}
    </template-output>
  </step>

  <step n="5" goal="Save and Confirm">
    <action>Write index.md to {target_directory}/index.md</action>
    <ask>Index generated! Review? [y/n]</ask>
  </step>
</instructions>
```

### Benefits for Professional Work

1. **Findability** - Quickly locate relevant documents
2. **Organization** - Clear structure reduces chaos
3. **Context** - Descriptions explain purpose at a glance
4. **Navigation** - Quick navigation sections speed workflows
5. **Onboarding** - New team members/future you understand structure
6. **Maintenance** - Easy to keep current with periodic regeneration

---

## Part 17: Workflow Chaining & Composability

### Overview

Professional workflows rarely operate in isolation. Workflow chaining enables complex multi-step processes where outputs from one workflow become inputs to the next.

### Three Chaining Patterns

#### Pattern 1: invoke-workflow (Synchronous Sub-Workflow)

**Purpose:** Call another workflow as a subroutine, wait for completion

**Syntax:**
```xml
<invoke-workflow>
  <workflow>{project-root}/workflows/sub-workflow/workflow.yaml</workflow>
  <inputs>
    <param name="client_name">{client_name}</param>
    <param name="analysis_type">financial</param>
  </inputs>
</invoke-workflow>
```

**Use Cases:**
- Run validation workflow before finalizing
- Generate supporting analysis mid-workflow
- Execute common subroutines (e.g., load-client-data)

**Example:**
```xml
<step n="5" goal="Generate Financial Projections">
  <!-- First, run analysis workflow -->
  <invoke-workflow>
    <workflow>{project-root}/workflows/finance/analyze-historical/workflow.yaml</workflow>
    <inputs>
      <param name="years">3</param>
      <param name="client">{client_name}</param>
    </inputs>
  </invoke-workflow>

  <!-- Analysis complete, use results -->
  <action>Load {analysis_output} from sub-workflow</action>
  <action>Use historical trends to project forward</action>
</step>
```

#### Pattern 2: Protocol Integration (Return to Caller)

**Purpose:** Enhance content, then return control to calling workflow

**Available Protocols:**
- **Advanced Elicitation** - Enhance content with 50+ methods
- **Party Mode** - Multi-agent discussion on topic
- **Discover Inputs** - Smart document loading

**Pattern:**
```xml
<step n="3" goal="Draft Budget Allocation">
  <action>Generate initial allocation based on priorities</action>

  <template-output section="allocation">
  ## Budget Allocation
  [Generated content]
  </template-output>

  <!-- User can select [A] or [P] to enhance -->
  <!-- Protocol executes, returns enhanced content -->
  <!-- Workflow continues with enhanced version -->
</step>
```

**Flow:**
1. Workflow reaches template-output checkpoint
2. User selects [A] Advanced Elicitation or [P] Party Mode
3. Protocol executes enhancement
4. Protocol returns enhanced content
5. Workflow continues from same checkpoint with new content

#### Pattern 3: Routing Recommendations (Manual Next Workflow)

**Purpose:** Suggest logical next steps at workflow completion

**Pattern:**
```xml
<step n="final" goal="Complete & Recommend">
  <action>Save final document</action>
  <action>Update project status</action>

  <message>
  ✅ Business Plan Complete!

  📋 Typical Next Workflows:
  1. financial-projections - Create detailed 3-year forecast
  2. pitch-deck - Build investor presentation
  3. go-to-market-plan - Detail marketing & sales strategy

  Which would you like to start? (1/2/3/none)
  </message>

  <check if="user_selects_option">
    <action>Load selected workflow</action>
  </check>
</step>
```

### Professional Workflow Chains

#### Financial Workflow Chain

**Complete Financial Planning Cycle:**

```
1. monthly-spending-analysis
   ↓ Output: spending-report.md
   ↓ Insights: Category trends, anomalies, savings opportunities

2. budget-adjustment
   ↓ Input: spending-report.md
   ↓ Output: adjusted-budget.md
   ↓ Insights: Recommended category reallocations

3. goal-impact-assessment
   ↓ Input: adjusted-budget.md + annual-financial-plan.md
   ↓ Output: goal-impact-report.md
   ↓ Insights: How budget changes affect long-term goals

4. implementation-plan
   ↓ Input: adjusted-budget.md + goal-impact-report.md
   ↓ Output: action-items.md
   ↓ Deliverable: Specific actions with deadlines
```

**Workflow Configuration:**

```yaml
# workflows/finance/budget-adjustment/workflow.yaml
input_file_patterns:
  spending_report:
    whole: "{output_folder}/spending-report*.md"
    load_strategy: "SELECTIVE_LOAD"  # Latest report

  annual_plan:
    sharded: "{output_folder}/*financial-plan*/*.md"
    load_strategy: "INDEX_GUIDED"  # Load relevant sections
```

**Chaining Logic:**

```xml
<!-- budget-adjustment/instructions.xml -->
<step n="final" goal="Complete & Chain">
  <action>Save adjusted-budget.md</action>

  <ask>
  Budget adjusted! Next steps:

  [g] Goal Impact Assessment - See how this affects your financial goals
  [i] Implementation Plan - Create action items to implement changes
  [n] None - I'll handle next steps manually

  Select option:
  </ask>

  <check if="g_selected">
    <invoke-workflow>
      <workflow>{project-root}/workflows/finance/goal-impact/workflow.yaml</workflow>
      <inputs>
        <param name="adjusted_budget">{output_file}</param>
      </inputs>
    </invoke-workflow>
  </check>

  <check if="i_selected">
    <invoke-workflow>
      <workflow>{project-root}/workflows/finance/implementation-plan/workflow.yaml</workflow>
      <inputs>
        <param name="budget_file">{output_file}</param>
      </inputs>
    </invoke-workflow>
  </check>
</step>
```

#### Client Engagement Chain

**Complete Client Project Flow:**

```
1. discovery-interview
   ↓ Output: discovery-notes.md
   ↓ Content: Pain points, goals, context, stakeholders

2. problem-analysis
   ↓ Input: discovery-notes.md
   ↓ Output: problem-analysis.md
   ↓ Content: Root cause, impact quantification, urgency

3. solution-design
   ↓ Input: problem-analysis.md
   ↓ Output: solution-design.md
   ↓ Content: Approach, methodology, deliverables

4. proposal-generation
   ↓ Input: discovery-notes.md + problem-analysis.md + solution-design.md
   ↓ Output: client-proposal.md
   ↓ Deliverable: Complete proposal ready for client
```

**Chaining with INDEX_GUIDED:**

```yaml
# workflows/clients/proposal-generation/workflow.yaml
input_file_patterns:
  client_docs:
    description: "All discovery and analysis documents"
    sharded: "{output_folder}/clients/{client_name}/*.md"
    load_strategy: "FULL_LOAD"  # Load everything for this client

  similar_proposals:
    description: "Past proposals for similar engagements"
    sharded: "{output_folder}/clients/*/proposal*.md"
    load_strategy: "INDEX_GUIDED"  # Load only relevant past work
```

#### Business Venture Chain

**Launch Preparation Sequence:**

```
1. market-research
   ↓ Output: market-research.md
   ↓ Data: Market size, trends, competitors, customers

2. business-model-canvas
   ↓ Input: market-research.md
   ↓ Output: business-model-canvas.md
   ↓ Content: 9 building blocks validated

3. business-plan
   ↓ Input: market-research.md + business-model-canvas.md
   ↓ Output: business-plan.md (sharded)
   ↓ Content: Complete 20-page plan

4. financial-projections
   ↓ Input: business-plan.md (load business-model, market sections)
   ↓ Output: financial-projections.xlsx + summary.md
   ↓ Content: 3-year forecast with assumptions

5. pitch-deck
   ↓ Input: business-plan.md + financial-projections.md
   ↓ Output: pitch-deck.md (later → slides)
   ↓ Content: 10-slide investor deck

6. go-to-market-plan
   ↓ Input: business-plan.md (load GTM, competitive sections)
   ↓ Output: gtm-plan.md
   ↓ Content: 90-day launch plan with tactics
```

**Automatic Chaining:**

```xml
<!-- business-plan/steps/step-08-complete.md -->
<step n="8" goal="Complete & Route">
  <action>Mark business-plan complete in project-status.yaml</action>

  <invoke-workflow path="{project-root}/workflows/status-tracker">
    <param>mode: update</param>
    <param>workflow_id: business-plan</param>
    <param>output_file: {output_file}</param>
  </invoke-workflow>

  <!-- Status tracker returns next recommended workflow -->
  <!-- Based on project-status.yaml logic -->

  <message>
  ✅ Business Plan Complete!

  Status tracker recommends: financial-projections
  Reason: Required next step for funding discussions

  Auto-start financial-projections? [y/n]
  </message>
</step>
```

### Scale-Adaptive Routing

**Quick-Dev Escalation Pattern:**

Small tasks stay in quick-dev, complex ones escalate to structured workflows.

```xml
<!-- quick-dev/instructions.md -->
<step n="assess" goal="Evaluate Complexity">
  <action>Analyze user request against escalation thresholds:</action>
  <check>
    - Multiple components involved?
    - System-level language ("architecture", "integrate")?
    - Uncertainty or ambiguity?
    - Estimated > 3 files or > 200 lines?
  </check>

  <check if="escalation_signals >= 2">
    <action>Load {project-root}/config/project-levels.yaml</action>
    <action>Determine complexity level (0-4)</action>

    <check if="level >= 3">
      <ask>
      This looks complex (Level {level}). Recommendations:

      [w] Workflow-Init - Start BMad Method with full planning
      [t] Tech-Spec - Create specification first
      [e] Execute Directly - I'll handle it (not recommended)

      Your choice:
      </ask>

      <check if="w_selected">
        <invoke-workflow>
          <workflow>{project-root}/workflows/workflow-status/init/workflow.yaml</workflow>
        </invoke-workflow>
      </check>
    </check>
  </check>

  <!-- If no escalation, continue with quick-dev -->
</step>
```

### Workflow Composition Benefits

1. **Modularity** - Build complex processes from simple workflows
2. **Reusability** - Common workflows (analysis, validation) used everywhere
3. **Flexibility** - Users choose chaining vs manual progression
4. **Intelligence** - Status tracker routes automatically
5. **Audit Trail** - Each workflow documents inputs/outputs
6. **Resumability** - Can stop/start at any workflow in chain
7. **Adaptability** - INDEX_GUIDED loading pulls relevant context

---

## Part 18: Professional Framework Quick Reference

### Professional Feature Matrix

| Feature | Purpose | Use For | Complexity | Keep? |
|---------|---------|---------|------------|-------|
| **Document Sharding** | Split large docs into sections | Financial plans, Business plans, Knowledge bases | Medium | ✅ YES |
| **FULL_LOAD** | Load all document sections | Annual reviews, Complete documentation | Low | ✅ YES |
| **SELECTIVE_LOAD** | Load specific section by variable | Q3 budget, Specific client proposal | Medium | ✅ YES |
| **INDEX_GUIDED** | Smart contextual document loading | Optional references, Large knowledge bases | Medium | ✅ YES |
| **Project Status Tracking** | Progress management & routing | Business ventures, Client engagements | Medium | ✅ YES |
| **Step-File Workflows** | Structured document building | Financial plans, Strategic docs, Proposals | High | ✅ YES |
| **Append-Only Building** | Incremental construction | All structured workflows | Low | ✅ YES |
| **A/P/C Checkpoints** | User approval gates | Critical documents | Low | ✅ YES |
| **Advanced Elicitation** | Content enhancement (50+ methods) | High-stakes deliverables | Medium | ✅ YES |
| **YOLO Mode** | Autonomous workflow execution | Routine workflows | Low | ✅ YES |
| **Workflow Chaining** | Multi-step process automation | Complex analyses, Project flows | Medium | ✅ YES |
| **Validation Checklists** | Quality assurance | All professional outputs | Low | ✅ YES |
| **Index Generation** | Knowledge organization | Documentation libraries | Low | ✅ YES |
| **Party Mode** | Multi-agent collaboration | Strategic decisions | Low | ✅ YES |
| **Brainstorming** | Creative ideation | New ventures, Problem-solving | Medium | ⚠️ OPTIONAL |
| **Excalidraw Diagrams** | Visual diagram generation | Process flows, Business models | Medium | ⚠️ OPTIONAL |

### When to Use What

#### Simple Task (< 1 hour)
**Characteristics:**
- Single workflow execution
- Familiar process
- Low stakes

**Approach:**
- Normal mode with checkpoints
- No chaining needed
- Basic validation

**Example:** Monthly budget update using previous month as template

#### Medium Task (1-4 hours)
**Characteristics:**
- Workflow with 3-5 steps
- Moderate complexity
- Some dependencies

**Approach:**
- Step-file workflow with checkpoints
- Consider elicitation at key sections
- Use validation checklist
- Optional chaining to next workflow

**Example:** Client proposal (discovery → problem → solution → pricing → finalize)

#### Complex Project (days/weeks)
**Characteristics:**
- Multiple chained workflows
- High interdependencies
- Significant planning needed

**Approach:**
- Project status tracking (workflow-init)
- Multiple chained workflows
- Step-file architecture for critical docs
- Document sharding for large deliverables
- Workflow-status router for "what's next?"

**Example:** Business plan for new venture
1. Market research (1 day)
2. Business model canvas (2 hours)
3. Business plan (1 week, step-file workflow, sharded output)
4. Financial projections (2 days)
5. Pitch deck (1 day)

#### High-Stakes Deliverable
**Characteristics:**
- Career/business impact
- External stakeholders
- Zero tolerance for errors

**Approach:**
- Step-file architecture (approval at each section)
- Advanced Elicitation at every checkpoint
- Validation checklist before finalization
- Party Mode for key decisions
- Multiple review cycles

**Example:**
- Investment strategy for retirement savings
- Client SOW for 6-figure engagement
- Pitch deck for Series A fundraising
- Annual strategic plan

### Quick Decision Tree

```
Is this routine? (monthly budget, standard report)
├─ YES → Single workflow, consider YOLO mode
└─ NO ↓

Is this high-stakes? (financial strategy, client proposal)
├─ YES → Step-file workflow + Advanced Elicitation + Validation
└─ NO ↓

Is this complex? (business plan, multi-phase project)
├─ YES → Project status tracking + Chained workflows + Sharding
└─ NO ↓

Default: Single workflow, normal mode, checkpoints
```

### Professional Use Case Examples

#### Weekly Financial Review (Simple)

**Tools Used:**
- Single workflow (weekly-financial-review)
- SELECTIVE_LOAD (this week's transactions)
- Normal mode with A/P/C checkpoints

**Duration:** 30 minutes
**Output:** Weekly summary with insights

---

#### Client Consulting Proposal (Medium)

**Tools Used:**
- Step-file workflow (6 steps)
- INDEX_GUIDED (load similar past proposals)
- Advanced Elicitation (Pre-mortem on solution)
- Validation checklist

**Duration:** 3-4 hours
**Output:** Professional proposal ready for client

---

#### Launch New Business Venture (Complex)

**Tools Used:**
- Project status tracking (workflow-init)
- Chained workflows (7 workflows)
- Document sharding (business plan)
- Step-file architecture (business plan, financial projections)
- Advanced Elicitation (market analysis, GTM strategy)
- Party Mode (validate business model with multiple perspectives)
- Validation checklists (each deliverable)

**Duration:** 2-3 weeks
**Outputs:**
- Market research report
- Business model canvas
- Business plan (sharded, 20+ pages)
- Financial projections (3 years)
- Pitch deck (10 slides)
- Go-to-market plan (90 days)

**Workflow Sequence:**
1. workflow-init → Set up project tracking
2. market-research → Understand market
3. business-model-canvas → Validate model
4. business-plan → Comprehensive plan (step-file, 8 steps)
5. financial-projections → Build forecast
6. pitch-deck → Investor presentation
7. go-to-market-plan → Launch strategy

---

#### Annual Financial Planning (High-Stakes)

**Tools Used:**
- Step-file workflow (7 steps)
- Document sharding (output sharded by section)
- FULL_LOAD (previous year's plan)
- Advanced Elicitation (Pre-mortem on investment strategy, 5 Whys on spending)
- Party Mode (Finance agent + Career agent + Tax specialist discuss strategy)
- Validation checklist
- Quarterly review workflow (chained follow-ups)

**Duration:** 6-8 hours (spread over 2-3 days)
**Output:** Comprehensive annual financial plan (sharded)

**Step-by-Step with Enhancements:**
1. Initialize → Load previous year
2. Income Analysis → A/P/C → [P] Party with Career agent
3. Fixed Expenses → A/P/C → [A] Pre-mortem: "What if expenses spike?"
4. Variable Expenses → A/P/C
5. Investment Strategy → A/P/C → [A] Black Swan Analysis + [P] Party (Finance + Tax)
6. Tax Optimization → A/P/C → [A] 5 Whys: "Why this tax strategy?"
7. Complete → Validation checklist → Shard output → Schedule quarterly reviews

---

### Feature Combination Patterns

**Pattern 1: Research → Plan → Execute**
```
Tools: INDEX_GUIDED + Step-File + Validation
Use: Client projects, business initiatives
Flow: Load past work → Build plan methodically → Validate before execution
```

**Pattern 2: Routine with Intelligence**
```
Tools: SELECTIVE_LOAD + YOLO + Chaining
Use: Monthly budgets, weekly reviews
Flow: Load last period → Auto-generate → Chain to analysis if anomalies
```

**Pattern 3: High-Stakes Strategic**
```
Tools: Step-File + Advanced Elicitation + Party Mode + Validation + Sharding
Use: Annual plans, major decisions, business strategies
Flow: Build incrementally → Enhance at each step → Multiple perspectives → Rigorous validation
```

**Pattern 4: Complex Multi-Workflow Project**
```
Tools: Project Status + Chaining + INDEX_GUIDED + Sharding
Use: Business launches, major initiatives
Flow: Initialize tracking → Execute chained workflows → Smart context loading → Organized outputs
```

---

### Getting Started Checklist

Before using advanced features, ensure:

**Core Setup:**
- [ ] Core engine files in place (workflow.xml, party-mode, master agent)
- [ ] Configuration files created (core/config.yaml, module/config.yaml)
- [ ] Manifest files initialized (agent-manifest.csv, workflow-manifest.csv)
- [ ] Output directory structure created

**For Document Sharding:**
- [ ] shard-doc tool available (npx @kayvan/markdown-tree-parser)
- [ ] Workflows configured with input_file_patterns
- [ ] Load strategies selected (FULL_LOAD, SELECTIVE_LOAD, INDEX_GUIDED)

**For Project Tracking:**
- [ ] workflow-status system in place
- [ ] workflow-init workflow available
- [ ] project-status.yaml template ready
- [ ] Phase/track definitions adapted for personal use

**For Step-File Workflows:**
- [ ] Workflow orchestrator (workflow.md) created
- [ ] Step files created (step-01-init.md, step-02-xxx.md, etc.)
- [ ] Template file with frontmatter ready
- [ ] A/P/C menu handlers configured

**For Advanced Elicitation:**
- [ ] advanced-elicitation.xml in core/tasks/
- [ ] Method categories CSV available
- [ ] Agent manifest includes agents for party mode

---

## Next Steps

Now that you understand what remains and how it works:

1. Review this preservation guide
2. Approve deletion plan (01-deletion-plan.md)
3. Execute pattern extraction
4. Execute deletions
5. Begin building personal agents (see task-tracker.md)

---

**Document Status:** 📘 ACTIVE REFERENCE - COMPREHENSIVE EDITION
**Use For:** Understanding preserved components and building new framework
**Related Docs:**
- [01-deletion-plan.md](01-deletion-plan.md) - What to remove
- [task-tracker.md](../tasks/task-tracker.md) - Execution tasks

**New in this Edition:**
- Part 11: Document Sharding & Management System
- Part 12: Project Status & Progress Tracking
- Part 13: Step-File Architecture & Structured Workflows
- Part 14: Advanced Elicitation for Professional Quality
- Part 15: Validation & Quality Assurance Patterns
- Part 16: Index Generation & Knowledge Organization
- Part 17: Workflow Chaining & Composability
- Part 18: Professional Framework Quick Reference
