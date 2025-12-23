# Getting Started with Personal Automation Framework

## Introduction
This framework helps you automate and manage all aspects of your personal life using AI-powered agents and workflows.

## First Steps

### 1. Load the Master Agent
The master agent is your entry point to the entire system.

```
Load file: _personal-automation/core/agents/master.md
```

The master agent will:
- Greet you by name
- Show you available commands
- Help you navigate domains, agents, and workflows

### 2. Explore Available Domains
From the master agent menu, select "List Domains" to see all available life areas:
- Finance
- Email
- Health
- Schedule
- Career
- Goals
- Relationships
- Home

### 3. View Available Agents
Select "List Available Agents" to see all specialized agents across all domains.

### 4. Browse Workflows
Select "List Workflows" to see all automation workflows available in the system.

### 5. Run Your First Workflow
Once domains are populated with workflows (Phase 4+), you can:
1. Load a domain-specific agent
2. Select a workflow from their menu
3. Follow the workflow steps
4. Review generated output

## Understanding the Structure

### Master Controller (core/)
- Contains the workflow execution engine
- Hosts the master agent (your main interface)
- Provides party-mode for multi-agent collaboration

### Domains (finance/, email/, etc.)
Each domain is self-contained with:
- **agents/** - Domain expert agent
- **workflows/** - Automation workflows
- **data/** - Your personal data for that domain
- **output/** - Generated reports and plans
- **knowledge/** - Best practices and tips
- **config.yaml** - Domain settings

### Configuration (_config/)
- Manifest files that register all agents, workflows, tasks, and tools
- Central registry for the entire framework

### Documentation (_docs/)
- Guides for creating domains, agents, workflows
- Patterns extracted from BMAD framework
- Templates for new components
- Technical reference documentation

## Next Steps

### Phase 4 (Coming Next)
- First domain agent will be created (Finance Manager - Sofia)
- Monthly budget workflow will be built
- You'll be able to run your first complete automation

### Phase 5
- Four more domain agents (Email, Health, Schedule, Career)
- Multiple workflows per domain
- Cross-domain collaboration examples

### Phase 6
- Advanced multi-agent scenarios
- System refinement based on real usage
- Additional domains as needed

## Tips

1. **Start Small**: Begin with one domain (finance recommended)
2. **Use Party Mode**: When decisions span multiple domains, use party-mode to get perspectives from multiple agents
3. **Customize Configs**: Edit domain config.yaml files to match your preferences
4. **Follow Patterns**: Use the patterns in _docs/patterns/ as templates for new workflows
5. **Stay Organized**: Keep your personal data in domain/data/ folders

## Need Help?

- Check [guides/](guides/) for how-to documentation
- Review [patterns/](patterns/) for examples
- Use [templates/](templates/) to create new components
- See [reference/](reference/) for technical details
