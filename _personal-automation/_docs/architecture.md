# Personal Automation Framework Architecture

## Overview
This framework uses a domain-driven architecture where each life area is completely self-contained, orchestrated by a master controller.

## Core Components

### 1. Master Controller (core/)
The central orchestration layer that coordinates all domains.

**Components:**
- **workflow.xml** - Execution engine that interprets all workflow YAML files
- **master.md** - Master agent that provides the main user interface
- **party-mode/** - Multi-agent collaboration workflow
- **config.yaml** - Framework-wide configuration

**Responsibilities:**
- Load and coordinate domain agents
- Execute workflows across domains
- Facilitate multi-agent discussions
- Maintain framework configuration

### 2. Domain Architecture
Each domain follows identical structure for consistency:

```
[domain-name]/
├── agents/          # Domain expert agent(s)
├── workflows/       # Automation workflows for this domain
├── data/           # Your personal data
├── output/         # Generated reports, plans, logs
├── knowledge/      # Best practices, tips, guides
└── config.yaml     # Domain-specific settings
```

**Benefits:**
- Everything related to one life area in one place
- Easy to navigate and maintain
- Independent evolution of each domain
- Clear ownership and organization

### 3. Configuration Layer (_config/)
Central registry of all framework components.

**Files:**
- **manifest.yaml** - Framework metadata and domain registry
- **agent-manifest.csv** - All agents indexed
- **workflow-manifest.csv** - All workflows indexed
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

### 1. Workflow Execution Pattern
All workflows follow the same execution model:

1. User loads master agent or domain agent
2. Selects workflow from menu
3. Agent loads workflow.yaml configuration
4. Passes to workflow.xml execution engine
5. Engine interprets instructions.md step-by-step
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
2. Master loads party-mode workflow
3. User selects agents from different domains
4. Agents discuss and provide perspectives
5. User makes informed decision

**Example:**
- Question: "Should I cancel my gym membership to save money?"
- Participants: Sofia (finance) + Dr. Ava (health)
- Result: Balanced financial + health perspective

### 4. Runtime Loading Pattern
Resources loaded only when needed:

- Agents don't pre-load all workflows
- Workflows don't pre-load all data
- Manifests queried dynamically
- Efficient memory usage

## Data Flow

### Input Flow
```
User → Master Agent → Domain Agent → Workflow → Data Files
```

### Output Flow
```
Workflow → Template → Generated Output → domain/output/
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
1. Copy domain-template/
2. Create domain config.yaml
3. Create domain agent
4. Build initial workflows
5. Register in manifests

### Adding New Workflows
1. Copy workflow-template.yaml
2. Create instructions.md
3. Define variables and inputs
4. Create output template
5. Register in workflow-manifest.csv

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
- First complete workflow
- Real automation example

### Phase 5
- 4 more domain agents
- Multiple workflows per domain
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
| Workflows | SDLC phases | Personal automations |
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
