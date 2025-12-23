# Workflow YAML Structure - Personal Automation Framework
# Extracted from BMAD workflows on 2025-12-22

This document describes the complete structure of workflow.yaml files used by the workflow.xml execution engine.

## Complete workflow.yaml Structure

```yaml
# Workflow Metadata
name: "workflow-identifier"
description: "Human-readable description of what this workflow does"
author: "Your Name"

# Configuration Inheritance
config_source: "{project-root}/_personal-automation/{module}/config.yaml"

# Variable Definitions - Inherit from Config
output_folder: "{config_source}:output_folder"
user_name: "{config_source}:user_name"
communication_language: "{config_source}:communication_language"
document_output_language: "{config_source}:document_output_language"
date: system-generated

# Custom Module-Specific Variables
custom_folder: "{config_source}:custom_folder_name"

# Workflow Paths
installed_path: "{project-root}/_personal-automation/{module}/workflows/{category}/{workflow-name}"
template: false  # Set to path if using template file, false otherwise
instructions: "{installed_path}/instructions.md"

# Required Input Files (Must exist for workflow to run)
required_inputs:
  - file_name: "{path/to/required/file.ext}"
  - another_file: "{path/to/another/file.ext}"

# Optional Input File Patterns (Smart loading with fallbacks)
input_file_patterns:
  pattern_name:
    description: "What this pattern loads"
    whole: "{output_folder}/single-file-pattern*.md"
    sharded_index: "{output_folder}/sharded-dir*/index.md"
    sharded_single: "{output_folder}/sharded-dir*/specific-file-{{variable}}.md"
    load_strategy: "SELECTIVE_LOAD | FULL_LOAD | INDEX_GUIDED"
  another_pattern:
    description: "Another pattern example"
    pattern: "{output_folder}/simple-glob-*.md"
    load_strategy: "SELECTIVE_LOAD"

# Output Paths
output_file: "{output_folder}/generated-output-{{date}}.md"
secondary_folder: "{output_folder}/subfolder"

# Standalone Flag
standalone: true  # Can be run independently
# OR
standalone: false  # Part of larger workflow chain
```

## Field Descriptions

### Metadata Fields

| Field | Required | Purpose | Example |
|-------|----------|---------|---------|
| `name` | ✅ Yes | Workflow identifier (kebab-case) | "monthly-budget" |
| `description` | ✅ Yes | Human-readable description | "Create monthly budget plan" |
| `author` | ⚠️ Optional | Creator name | "Rodrigo" |

### Configuration Fields

| Field | Required | Purpose | Example |
|-------|----------|---------|---------|
| `config_source` | ✅ Yes | Path to module config.yaml | "{project-root}/_personal-automation/life-management/config.yaml" |
| `output_folder` | ✅ Yes | Where outputs are saved | "{config_source}:output_folder" |
| `user_name` | ✅ Yes | User's name for personalization | "{config_source}:user_name" |
| `communication_language` | ✅ Yes | Language for interaction | "{config_source}:communication_language" |
| `document_output_language` | ✅ Yes | Language for documents | "{config_source}:document_output_language" |
| `date` | ⚠️ Optional | Auto-generated timestamp | "system-generated" |

### Path Fields

| Field | Required | Purpose | Example |
|-------|----------|---------|---------|
| `installed_path` | ✅ Yes | Workflow directory location | "{project-root}/_personal-automation/life-management/workflows/finance/monthly-budget" |
| `template` | ⚠️ Optional | Template file path or false | "{installed_path}/budget-template.md" or false |
| `instructions` | ✅ Yes | Main workflow logic file | "{installed_path}/instructions.md" |

### Input File Fields

#### required_inputs
List of files that MUST exist for workflow to run.

```yaml
required_inputs:
  - agent_manifest: "{project-root}/_bmad/_config/agent-manifest.csv"
  - previous_budget: "{output_folder}/budget-2024-11.md"
```

#### input_file_patterns
Smart file loading with multiple strategies:

```yaml
input_file_patterns:
  budget_history:
    description: "Previous budgets for comparison"
    whole: "{output_folder}/budget-*.md"
    load_strategy: "SELECTIVE_LOAD"
```

**Load Strategies:**
- `SELECTIVE_LOAD`: Ask user which files to load
- `FULL_LOAD`: Load all matching files automatically
- `INDEX_GUIDED`: Load based on index.md if exists

**Pattern Types:**
- `whole`: Single file glob pattern
- `sharded_index`: Index file for sharded documents
- `sharded_single`: Specific sharded file with variable
- `pattern`: Simple glob pattern

### Output Fields

| Field | Required | Purpose | Example |
|-------|----------|---------|---------|
| `output_file` | ⚠️ Optional | Primary output path | "{output_folder}/budget-{{date}}.md" |
| Custom folders | ⚠️ Optional | Additional output locations | "{output_folder}/reports" |

### Workflow Flags

| Field | Required | Purpose | Values |
|-------|----------|---------|--------|
| `standalone` | ⚠️ Optional | Can run independently? | true (yes) / false (part of chain) |

## Variable Resolution

Variables use `{variable_name}` syntax and resolve in this order:

1. **System Variables**
   - `{project-root}`: Repository root directory
   - `{date}`: Current date (YYYY-MM-DD)

2. **Config Variables**
   - `{config_source}:field_name`: Read from config.yaml
   - Example: `{config_source}:user_name` → reads `user_name` from config

3. **Workflow Variables**
   - `{{variable}}`: Set during workflow execution
   - Example: `{{epic_number}}`: Determined at runtime

4. **Custom Variables**
   - Any field defined in workflow.yaml becomes available
   - Example: `custom_folder: "{output_folder}/custom"` → use `{custom_folder}`

## Examples

### Example 1: Simple Workflow (Finance - Monthly Budget)

```yaml
name: "monthly-budget"
description: "Create monthly budget plan"
author: "Rodrigo"

config_source: "{project-root}/_personal-automation/life-management/config.yaml"
output_folder: "{config_source}:output_folder"
user_name: "{config_source}:user_name"
communication_language: "{config_source}:communication_language"
document_output_language: "{config_source}:document_output_language"
date: system-generated

budget_folder: "{config_source}:budget_folder"
finance_data: "{config_source}:finance_data"

installed_path: "{project-root}/_personal-automation/life-management/workflows/finance/monthly-budget"
template: "{installed_path}/budget-template.md"
instructions: "{installed_path}/instructions.md"

input_file_patterns:
  previous_budget:
    description: "Last month's budget for reference"
    pattern: "{budget_folder}/budget-*.md"
    load_strategy: "SELECTIVE_LOAD"

output_file: "{budget_folder}/budget-{{date}}.md"

standalone: true
```

### Example 2: Workflow with Required Inputs (Email Task Extraction)

```yaml
name: "extract-tasks"
description: "Extract action items from emails"
author: "Rodrigo"

config_source: "{project-root}/_personal-automation/life-management/config.yaml"
output_folder: "{config_source}:output_folder"
user_name: "{config_source}:user_name"
communication_language: "{config_source}:communication_language"
document_output_language: "{config_source}:document_output_language"
date: system-generated

email_folder: "{config_source}:email_folder"
task_export_format: "{config_source}:task_export_format"

installed_path: "{project-root}/_personal-automation/life-management/workflows/email/extract-tasks"
template: false
instructions: "{installed_path}/instructions.md"

required_inputs:
  - email_export: "{email_folder}/inbox-export-{{date}}.txt"

output_file: "{email_folder}/tasks-{{date}}.md"

standalone: true
```

### Example 3: Complex Workflow with Multiple Patterns (Health Weekly Review)

```yaml
name: "weekly-health-review"
description: "Review weekly health metrics and progress"
author: "Rodrigo"

config_source: "{project-root}/_personal-automation/life-management/config.yaml"
output_folder: "{config_source}:output_folder"
user_name: "{config_source}:user_name"
communication_language: "{config_source}:communication_language"
document_output_language: "{config_source}:document_output_language"
date: system-generated

health_folder: "{config_source}:health_folder"

installed_path: "{project-root}/_personal-automation/life-management/workflows/health/weekly-review"
template: "{installed_path}/review-template.md"
instructions: "{installed_path}/instructions.md"

input_file_patterns:
  workout_logs:
    description: "This week's workout logs"
    whole: "{health_folder}/workouts/workout-*.md"
    load_strategy: "FULL_LOAD"
  meal_logs:
    description: "This week's meal logs"
    whole: "{health_folder}/meals/meal-*.md"
    load_strategy: "FULL_LOAD"
  previous_review:
    description: "Last week's review for comparison"
    pattern: "{health_folder}/reviews/review-*.md"
    load_strategy: "SELECTIVE_LOAD"

output_file: "{health_folder}/reviews/review-{{date}}.md"

standalone: true
```

## Best Practices

1. **Always inherit from config.yaml**
   - Don't hardcode user_name, output_folder, languages
   - Use `{config_source}:field_name` pattern

2. **Use descriptive names**
   - Workflow name: kebab-case, clear purpose
   - Variables: descriptive, not abbreviated

3. **Set load strategies appropriately**
   - SELECTIVE_LOAD: When user should choose (previous budgets)
   - FULL_LOAD: When all files needed (weekly logs)
   - INDEX_GUIDED: When sharded docs have index

4. **Template vs No Template**
   - `template: "{path}"`: For structured outputs (budgets, reports)
   - `template: false`: For dynamic outputs (task extraction)

5. **Standalone Flag**
   - `true`: Can be run anytime independently
   - `false`: Part of a sequence (rare in personal automation)

## Integration with workflow.xml

The workflow.xml engine:
1. Loads this YAML file
2. Resolves all variables
3. Loads config_source
4. Reads instructions.md
5. Executes workflow steps
6. Saves outputs to specified paths

## Migration Notes

When adapting from BMM workflows:
- Change config paths from `_bmad/bmm/` to `_personal-automation/{module}/`
- Remove software-specific variables (sprint_artifacts, user_skill_level)
- Keep core variables (user_name, communication_language, output_folder)
- Simplify input_file_patterns (remove complex sharded patterns unless needed)
