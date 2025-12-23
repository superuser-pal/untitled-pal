# Personal Automation Framework Documentation

Welcome to your personal automation system! This framework helps you manage all aspects of your life through AI-powered agents and workflows.

## Quick Start
1. Load the master agent: [core/agents/master.md](../core/agents/master.md)
2. Explore available domains and agents
3. Run your first workflow

See [getting-started.md](getting-started.md) for detailed instructions.

## Structure
- **core/** - Master controller and execution engine
- **_config/** - Framework configuration
- **_docs/** - This documentation
- **[domain]/** - Self-contained life areas (finance, health, etc.)

## Documentation
- [Getting Started](getting-started.md) - First steps
- [Architecture](architecture.md) - How the system works
- [Guides](guides/) - How-to create domains, agents, workflows
- [Patterns](patterns/) - Reusable patterns from BMAD
- [Templates](templates/) - Templates for new items
- [Reference](reference/) - Technical reference docs

## Domains
The framework includes 8 self-contained domains:
- **finance/** - Budget tracking, spending analysis, financial planning
- **email/** - Email triage, task extraction, newsletter processing
- **health/** - Fitness tracking, meal planning, health goals
- **schedule/** - Time management, calendar optimization, energy mapping
- **career/** - Skill tracking, career goals, professional development
- **goals/** - Goal setting, habit tracking, progress reviews
- **relationships/** - Birthday tracking, gift planning, connection maintenance
- **home/** - Maintenance schedules, cleaning plans, home projects

Each domain is completely self-contained with its own agents, workflows, data, output, and knowledge.
