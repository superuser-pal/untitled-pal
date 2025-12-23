# Master Agent Command Routing Pattern

**Status:** Reference Only - Not Core Framework

**Source:** BMAD bmad-master agent

## Overview

BMAD's bmad-master agent acts as a command router that analyzes user requests and routes them to the appropriate sub-agent or workflow.

## Concept

**User input → Analysis → Route to specialist**

```
User: "I want to create a PRD"
bmad-master analyzes: "PRD creation task"
→ Routes to: pm agent (Product Manager)
→ pm agent executes: create-prd workflow
```

**Routing logic:**

```xml
<activation>
  <step n="1">Analyze user request</step>
  <step n="2">Determine which agent/workflow is best suited</step>
  <step n="3">Route to that agent with context</step>
  <step n="4">Monitor execution and provide updates</step>
</activation>
```

## When to Use

**Use master agent routing when:**
- Building a large multi-agent system (10+ agents)
- Users don't know which agent to invoke
- Need intelligent request classification
- Want unified entry point for all commands

**Don't use when:**
- You have < 5 agents (direct invocation is simpler)
- Users know which agent they need
- Routing adds unnecessary complexity

## Example from BMAD

**bmad-master menu:**

```
What would you like to do?

1. Create PRD → pm agent
2. Design architecture → architect agent
3. Create epics & stories → pm agent
4. Create tech spec → dev agent
5. Review code → tea agent (Test & Evaluation Architect)
6. Plan sprint → sm agent (Scrum Master)
```

**Smart routing:**

```
User: "I need to plan authentication"

bmad-master:
1. Detects: Technical planning
2. Routes to: architect agent
3. architect launches: create-architecture workflow
```

## Personal Automation Relevance

**Medium** - Useful if you build a large personal automation system with many specialized agents (Finance Manager, Health Coach, Career Advisor, Schedule Optimizer, etc.) and want a single entry point.

## Canonical Alternative

**Team pattern** (canonical #4) handles multi-agent collaboration without needing a master router.

**Simple approach:**
```
User directly invokes agents:
- "Sofia, review my budget" → Finance Manager
- "Dr. Ava, plan my workouts" → Health Coach
- "Morgan, advise on career" → Career Advisor
```

**Party mode approach:**
```
User: "Help me plan next month"
→ Launches party mode
→ All relevant agents join discussion
→ Collaborative planning
```

No master router needed - agents collaborate peer-to-peer.

## When Master Routing Makes Sense

If you have 10+ agents and users genuinely don't know which to invoke:

```
User: "I feel overwhelmed"

Master agent analyzes:
- Possible agents: Health Coach (stress), Schedule Optimizer (time management), Mental Health Support
- Routes to: Mental Health Support agent first
- Brings in others as needed
```

For most personal automation, direct agent invocation or party mode is simpler.
