# PromptPal-Agentic-Framework: Personal Automation

**Personal Automation OS for Digital Sovereigns**

A Markdown-first, open-source framework that empowers you to architect your own agentic life through domain-driven sovereignty and intelligent orchestration.

---

## Overview

This framework transforms you from an AI consumer into a **Digital Sovereign** - someone who controls their own automation logic through human-readable, auditable Markdown files. Built on the PromptPal Protocol, it provides a multi-domain agent system with strict data boundaries to prevent context poisoning.

**Core Philosophy:**
- Your IDE is your cockpit for direct control
- Markdown is the master - all logic is human-readable
- Domain-driven sovereignty prevents data leakage
- Privacy-first, local-by-default architecture

---

## Getting Started

### Loading the Master Orchestrator

The **Domains Master** is your central hub for navigating the personal automation framework.

To activate the Master Orchestrator:
1. Load the agent file: `_personal-automation/core/agents/master.md`
2. The agent will greet you with your name from config
3. Browse the menu to access domain agents or start Party Mode

**Master Orchestrator Menu:**
- Load individual domain agents (Sofia, Elena, Scribe, Proxy, The Architect)
- View available domains and their focus areas
- List all agents with their expertise
- Start Party Mode for multi-agent collaboration
- Check system status

### Customizing Agents

Each agent can be customized via YAML configuration files in `_config/agents/`:

**Available Customizations:**
- **Persona Override**: Replace role, identity, communication style, and principles
- **Persistent Memories**: Add agent-specific context that persists across sessions
- **Custom Menu Items**: Add domain-specific workflows or tasks to the agent's menu
- **Custom Prompts**: Define reusable prompts for action handlers

**Example:** To add a memory to Sofia (Finance agent), edit `_config/agents/sofia.customize.yaml`:
```yaml
memories:
  - "User's primary financial goal is saving for a house"
  - "User prefers weekly spending reports"
```

All agents automatically load their customization files on activation. See individual `.customize.yaml` files for detailed examples and options.

---

## Available Domain Agents

### 💰 Sofia (Finance Domain)
**Role:** Personal Finance Advisor
**Mission:** Automated audits and total financial visibility
**Expertise:** Expense tracking, budget monitoring, subscription audits
**Primary Workflow:** "Audit my spending and alert me to subscription bloat"

**Location:** `domains/finance/agent.md`

### 📧 Elena (Email Domain)
**Role:** Email Productivity Specialist
**Mission:** Ruthless inbox triage and task extraction
**Expertise:** Noise filtering, signal detection, task extraction
**Primary Workflow:** "Draft replies based on my `knowledge/tone-guide.md`"

**Location:** `domains/email/agent.md`

### ✍️ Scribe (Writing Domain)
**Role:** Writing Coach & Editor
**Mission:** Substack content creation and voice development
**Expertise:** Newsletter writing, research gathering, style refinement
**Primary Workflow:** "Review my Substack draft for clarity and voice consistency"

**Location:** `domains/writing/agent.md`

### 📱 Proxy (Social Media Domain)
**Role:** Social Media Strategist
**Mission:** Strategic growth of 'Lara Lou' Substack channel
**Expertise:** Content calendars, growth analytics, cross-platform promotion
**Primary Workflow:** "Create a content calendar aligned with audience psychology"

**Location:** `domains/social-media/agent.md`

### 🏗️ The Architect (Framework Lab Domain)
**Role:** Framework Meta-Manager
**Mission:** System oversight and OSS stewardship
**Expertise:** Framework architecture, version control, roadmap management
**Primary Workflow:** "Review proposed changes for architectural coherence"

**Location:** `domains/framework-lab/agent.md`

---

## Party Mode: Multi-Agent Collaboration

**Party Mode** enables collaborative discussions between multiple agents, bringing diverse expertise together for complex problems.

**How to Use Party Mode:**
1. Load the Master Orchestrator
2. Select `*party-mode` from the menu
3. All agents load and introduce themselves
4. Pose your question or topic for discussion
5. Agents collaborate, building on each other's perspectives
6. Exit with `*exit`, `goodbye`, or `end party`

**When to Use Party Mode:**
- Cross-domain questions (e.g., "Should I invest in paid Substack?")
- Complex decisions requiring multiple perspectives
- Brainstorming sessions across expertise areas
- Strategic planning that spans multiple life domains

**Location:** `core/workflows/party-mode.md`

---

## The PromptPal Protocol: 10 Commandments

These principles guide the framework's architecture and all contributed workflows:

1. **Markdown is the Master** - Logic must be human-readable and auditable
2. **Domain-Driven Sovereignty** - Data is siloed to prevent context poisoning
3. **The IDE is the Cockpit** - Direct file control via Cursor/Claude Code
4. **Variables are Truth** - Decouple logic from local paths for portability
5. **Cost-Efficiency by Design** - Use Micro-Step Patterns to minimize token spend
6. **Human-in-the-Loop** - Agents require explicit validation via `<ask>` tags
7. **Privacy-First** - Local-first data architecture; no silent API leaks
8. **Composable Utility** - Workflows are "Lego bricks" that stack across domains
9. **The Master Orchestrator** - One central agent expert in the Protocol, not the data
10. **Open Contribution** - Scale through verified architectural standards

---

## The Contributor Credo

For a workflow to be accepted into the PromptPal core, it must:

✅ Follow the `workflow.xml` execution standard
✅ Use standardized data schemas (e.g., `balance.md` for Finance)
✅ Include `instructions.md` with clear `<step>`, `<ask>`, and `<check>` logic
✅ Respect domain boundaries and prevent context poisoning
✅ Be auditable, portable, and privacy-first

---

## Project Structure

```
_personal-automation/
├── _config/                    # Central registries
│   ├── agents/                 # Agent customization files
│   │   ├── domains-master.customize.yaml
│   │   ├── sofia.customize.yaml
│   │   ├── elena.customize.yaml
│   │   ├── scribe.customize.yaml
│   │   ├── proxy.customize.yaml
│   │   └── architect.customize.yaml
│   ├── agent-manifest.csv      # All agents with metadata
│   ├── manifest.yaml           # Framework metadata
│   └── *-manifest.csv          # Task, workflow, tool registries
│
├── _docs/                      # Documentation & guides
│   ├── architecture.md         # Domain-driven architecture
│   ├── getting-started.md      # Framework introduction
│   ├── skill-registration-pattern.md  # Skill registration guide
│   ├── guides/                 # How-to guides
│   ├── patterns/               # Extracted patterns
│   └── templates/              # Component templates
│
├── core/                       # Master orchestration layer
│   ├── agents/
│   │   └── master.md           # Domains Master agent
│   ├── config.yaml             # User configuration
│   └── workflows/              # Party Mode workflow
│
└── domains/                    # 5 domain agents
    ├── finance/                # Sofia
    ├── email/                  # Elena
    ├── writing/                # Scribe
    ├── social-media/           # Proxy
    └── framework-lab/          # The Architect
```

---

## Configuration

Your personal settings are stored in `core/config.yaml`:

```yaml
user_name: Rodrigo
communication_language: English
timezone: Belgium/Brussels
date_format: DD-MM-YYYY
framework_version: 1.0.0

domains:
  finance: "{project-root}/_personal-automation/domains/finance"
  email: "{project-root}/_personal-automation/domains/email"
  writing: "{project-root}/_personal-automation/domains/writing"
  social-media: "{project-root}/_personal-automation/domains/social-media"
  framework-lab: "{project-root}/_personal-automation/domains/framework-lab"

agents:
  sofia: "finance"
  elena: "email"
  scribe: "writing"
  proxy: "social-media"
  the-architect: "framework-lab"
```

**Customization:**
- Update `user_name` to personalize agent interactions
- Change `communication_language` for multilingual support
- Adjust `timezone` and `date_format` for your locale
- Add new domains by extending the `domains` section

---

## Strategic Roadmap

**Phase 1 (Current):** ✅ Complete - Master Orchestrator finalized
**Phase 2 (Next):** Build logic for Starter 3 (Finance, Email, Project Manager)
**Phase 3:** Launch Contributor Portal and community templates
**Phase 4:** Enable HUD templates for Streamlit/React visualization

---

## Architecture Principles

### Domain-Driven Sovereignty
Each domain operates as an independent jurisdiction with:
- Isolated data storage (no cross-contamination)
- Domain-specific knowledge bases
- Dedicated workflows for domain-specific tasks
- Specialized agent persona and expertise

### The IDE as Cockpit
Primary control through your IDE (Cursor/Claude Code):
- Direct file editing for full transparency
- Version control for all changes
- No hidden automation logic
- Complete audit trail

### Privacy-First Design
All data stays local by default:
- No silent API calls
- No external data leaks
- User-controlled data sharing
- Transparent data handling

---

## Next Steps

1. **Explore the Domains:** Load each domain agent to understand their capabilities
2. **Try Party Mode:** Experience multi-agent collaboration with a complex question
3. **Review the Strategy:** Read `framework-transformation/docs/STRATEGY.md` for the full vision
4. **Customize Your Config:** Update `core/config.yaml` with your preferences

---

## Support & Contribution

**Questions:** Review the documentation in `_docs/`
**Contributions:** Follow the Contributor Credo for workflow submissions
**Strategy:** See `framework-transformation/docs/STRATEGY.md` for architectural direction

---

**Status:** Phase 1 Complete - Master Orchestrator Active
**Version:** 1.0.0
**Last Updated:** 2025-12-27
