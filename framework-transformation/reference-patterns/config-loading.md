# Config Loading Pattern

**Status:** Reference Only - Not Core Framework

**Source:** BMAD workflow.xml and bmm/config.yaml

## Overview

BMAD uses a multi-level configuration system with hierarchical resolution:
1. Project-level config (`_bmad/bmm/config.yaml`)
2. Module-level config (workflow-specific settings)
3. Runtime variables (user input, system-generated)

## Concept

**Config Source Resolution:**

```yaml
# workflow.yaml
config_source: "{project-root}/_bmad/bmm/config.yaml"

# Reference values from config using {config_source}: prefix
user_name: "{config_source}:user_name"
output_folder: "{config_source}:output_folder"
team_file: "{config_source}:team_file"
```

**Loaded at runtime:**

```
1. Load config.yaml from config_source path
2. Replace all {config_source}:key with values from config
3. Result: user_name = "Alice", output_folder = "/output", team_file = "/teams/team.yaml"
```

## When to Use

**Use config loading when:**
- Managing 10+ workflows that share common settings
- Building a large personal automation suite
- Need to change paths globally (one config, many workflows)

**Don't use config loading when:**
- You have < 5 workflows
- Direct variable assignment is simpler
- Shared config adds unnecessary complexity

## Example from BMAD

**config.yaml:**
```yaml
user_name: "Alice"
output_folder: "/Users/alice/bmad-output"
team_file: "/Users/alice/teams/team-fullstack.yaml"
communication_language: "English"
document_output_language: "English"
```

**workflow.yaml:**
```yaml
config_source: "{project-root}/_bmad/bmm/config.yaml"
user_name: "{config_source}:user_name"
output_folder: "{config_source}:output_folder"
```

**Runtime resolution:**
```
user_name: "Alice"
output_folder: "/Users/alice/bmad-output"
```

## Personal Automation Relevance

**Medium** - Useful if building a large personal automation system with many workflows, but overkill for most personal use cases.

## Canonical Alternative

Define variables directly in workflow.yaml:

```yaml
# Simple approach (no config loading)
user_name: "Alice"
output_folder: "/Users/alice/personal-automation"
```

Or create a shared config if you truly need it:

```yaml
# personal-config.yaml
user_name: "Alice"
base_path: "/Users/alice/personal-automation"
```

```yaml
# workflow.yaml
config_source: "{project-root}/personal-config.yaml"
user_name: "{config_source}:user_name"
```

Only add config loading complexity if you genuinely have dozens of workflows sharing settings.
