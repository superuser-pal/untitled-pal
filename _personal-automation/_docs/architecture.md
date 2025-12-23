# Personal Automation Framework Architecture

## Overview
This framework uses a domain-driven architecture where each life area is completely self-contained, orchestrated by a master controller.

## Directory Structure
```
_personal-automation/
├── _config/          # Central registry and manifests
├── _docs/           # Documentation and templates
├── core/            # Master controller and framework core
└── domains/         # All domain folders organized together
    ├── finance/
    ├── email/
    ├── health/
    ├── schedule/
    ├── career/
    ├── goals/
    ├── relationships/
    └── home/
```

## Core Components

### 1. Master Controller (core/)
The central orchestration layer that coordinates all domains.

**Components:**
- **workflow.xml** - Execution engine that interprets all job configuration files
- **master.md** - Master agent that provides the main user interface
- **party-mode/** - Multi-agent collaboration job
- **config.yaml** - Framework-wide configuration

**Responsibilities:**
- Load and coordinate domain agents
- Execute jobs-to-be-done across domains
- Facilitate multi-agent discussions
- Maintain framework configuration

### 2. Domain Architecture
All domains live under `domains/` for better organization. Each domain follows identical structure for consistency:

```
domains/[domain-name]/
├── agents/          # Domain expert agent(s)
├── jtbd/           # Jobs-to-be-Done for this domain
├── data/           # Your personal data
├── output/         # Generated reports, plans, logs
├── knowledge/      # Best practices, tips, guides
└── config.yaml     # Domain-specific settings
```

**Agent + JTBD Hierarchy:**
- **Agents**: Domain experts who understand your life context
- **Jobs-to-be-Done**: Outcome-driven tasks agents help you accomplish
- Each job captures: situation, motivation, and expected outcome
- Agents execute jobs based on your specific needs

**Benefits:**
- Everything related to one life area in one place
- Easy to navigate and maintain
- Independent evolution of each domain
- Clear ownership and organization
- Outcome-focused rather than process-focused

### 3. Configuration Layer (_config/)
Central registry of all framework components.

**Files:**
- **manifest.yaml** - Framework metadata and domain registry
- **agent-manifest.csv** - All agents indexed
- **jtbd-manifest.csv** - All jobs-to-be-done indexed
- **task-manifest.csv** - All tasks indexed
- **tool-manifest.csv** - All tools indexed

### 4. Documentation Layer (_docs/)
Self-documenting framework with guides, patterns, and templates.

**Structure:**
- **guides/** - How-to documentation
- **patterns/** - Extracted patterns from BMAD
- **templates/** - Templates for creating new components
- **reference/** - Technical reference docs

## Key Design Patterns

### 1. Job Execution Pattern
All jobs-to-be-done follow the same execution model:

1. User loads master agent or domain agent
2. Selects job from menu (based on desired outcome)
3. Agent loads job-config.yaml configuration
4. Passes to workflow.xml execution engine
5. Engine interprets execution.md step-by-step
6. Generates output using templates
7. Saves to domain/output/

### 2. Variable Resolution Pattern
Dynamic path resolution using template variables:

- `{project-root}` - Root of the repository
- `{domain_root}` - Root of the current domain folder
- `{user_name}` - User's name from config
- `{current_date}` - Current date in configured format
- `{output_folder}` - Domain-specific output path

### 3. Party Mode Pattern
Multi-agent collaboration for cross-domain decisions:

1. User requests party mode from master agent
2. Master loads party-mode job
3. User selects agents from different domains
4. Agents discuss and provide perspectives
5. User makes informed decision

**Example:**
- Job: "Should I cancel my gym membership to save money?"
- Participants: Sofia (finance) + Dr. Ava (health)
- Result: Balanced financial + health perspective

### 4. Runtime Loading Pattern
Resources loaded only when needed:

- Agents don't pre-load all jobs
- Jobs don't pre-load all data
- Manifests queried dynamically
- Efficient memory usage

## Data Flow

### Input Flow
```
User → Master Agent → Domain Agent → Job-to-be-Done → Data Files
```

### Output Flow
```
Job-to-be-Done → Template → Generated Output → domain/output/
```

### Cross-Domain Flow
```
Domain A Agent ←→ Party Mode ←→ Domain B Agent
                       ↓
                  User Decision
```

## Security & Privacy

### Principles
1. **Data Locality**: Personal data stays in domain folders
2. **No External Calls**: Framework runs entirely locally
3. **User Control**: Explicit approval for all automations
4. **Privacy by Design**: Agents never expose data without permission

### Data Storage
- Personal data: `domain/data/` (version controlled separately)
- Generated outputs: `domain/output/` (gitignored)
- Knowledge: `domain/knowledge/` (version controlled)

## Extensibility

### Adding New Domains
1. Copy domain-template/ into domains/
2. Create domain config.yaml
3. Create domain agent
4. Build initial jobs-to-be-done
5. Register in manifests

### Adding New Jobs-to-be-Done
1. Copy jtbd-template.yaml
2. Define situation, motivation, and expected outcome
3. Create execution.md with step-by-step instructions
4. Define variables and inputs
5. Create output template
6. Register in jtbd-manifest.csv

### Adding New Agents
1. Copy agent-template.md
2. Define persona and role
3. Create menu items
4. Register in agent-manifest.csv

## Technology Stack

- **Language**: Markdown, YAML, XML
- **Execution**: Claude Code / Claude API
- **Storage**: Local filesystem
- **Version Control**: Git (framework code only)
- **Configuration**: YAML files

## Framework Evolution

### Phase 3 (Current)
- Core framework structure
- Master controller
- 8 empty domain folders
- Documentation and templates

### Phase 4 (Next)
- First domain agent (Finance)
- First complete job-to-be-done
- Real automation example

### Phase 5
- 4 more domain agents
- Multiple jobs per domain
- Cross-domain examples

### Phase 6
- Advanced scenarios
- System refinement
- Additional domains as needed

## Comparison with BMAD

| Aspect | BMAD | Personal Automation |
|--------|------|---------------------|
| Purpose | Software development | Personal life management |
| Structure | Module-based | Domain-driven |
| Agents | Development roles (PM, Dev, QA) | Life areas (Finance, Health) |
| Tasks | Workflows (SDLC phases) | Jobs-to-be-Done (outcome-focused) |
| Output | Code, docs, tests | Plans, reports, logs |
| Data | Project requirements | Personal information |

## Benefits of This Architecture

✅ **Clear Organization**: Everything for a life area in one folder
✅ **Easy Navigation**: No hunting across multiple directories
✅ **Independent Domains**: Work on health without touching finance
✅ **Scalable**: Add new domains without restructuring
✅ **Self-Documenting**: Guides and patterns included
✅ **Template-Driven**: Easy to create new components
✅ **Privacy-Focused**: Local-first design
✅ **Maintainable**: Consistent structure across all domains
