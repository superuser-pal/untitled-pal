# How to Create a New Domain

Follow these steps to add a new life area to your personal automation system.

## Step 1: Copy the Domain Template

```bash
cp -r _personal-automation/_docs/templates/domain-template/ _personal-automation/[new-domain-name]/
```

This creates the complete folder structure with all necessary subdirectories.

## Step 2: Create Domain Configuration

Edit `[domain]/config.yaml` with your domain-specific settings:

```yaml
# [Domain Name] Domain Configuration
domain_name: [domain-name]
domain_version: 1.0.0

# Paths
agents_path: "{domain_root}/agents"
workflows_path: "{domain_root}/workflows"
data_path: "{domain_root}/data"
output_path: "{domain_root}/output"
knowledge_path: "{domain_root}/knowledge"

# Domain-specific settings
[setting_1]: [value]
[setting_2]: [value]
```

**Example Settings by Domain Type:**
- **Finance**: currency, budget_frequency, fiscal_year_start
- **Health**: units (metric/imperial), fitness_goals, dietary_preferences
- **Schedule**: timezone, work_hours, default_meeting_duration

## Step 3: Create Domain Agent

Use `_docs/templates/agent-template.md` as your starting point.

**Key sections to customize:**
1. **Name & Icon**: Give your agent a personality
2. **Persona**: Define their expertise and communication style
3. **Menu Items**: List the workflows they can execute
4. **Activation**: Configure config loading path

See `_docs/patterns/agent-template.xml` for complete pattern structure.

## Step 4: Create Initial Workflows

For each workflow you want to create:

1. Create workflow directory: `[domain]/workflows/[workflow-name]/`
2. Copy `_docs/templates/workflow-template.yaml` → `workflow.yaml`
3. Create `instructions.md` with step-by-step logic
4. (Optional) Create `template.md` for output format
5. (Optional) Create `checklist.md` for pre-flight checks

See `_docs/patterns/workflow-yaml-structure.md` for details.

## Step 5: Register in Manifests

### Add to _config/agent-manifest.csv
```csv
"agent-name","Display Name","Agent Title","🎯","Role","Identity","Communication Style","Principles","domain-name","_personal-automation/[domain]/agents/[agent].md"
```

### Add to _config/workflow-manifest.csv
For each workflow:
```csv
"workflow-name","Workflow description","domain-name","_personal-automation/[domain]/workflows/[workflow]/workflow.yaml"
```

### Update _config/manifest.yaml
Add domain entry:
```yaml
domains:
  - name: [domain-name]
    config: "_personal-automation/[domain]/config.yaml"
    agents_count: 1
    workflows_count: [number]
```

### Update core/config.yaml
Add domain path:
```yaml
domains:
  [domain-name]: "{project-root}/_personal-automation/[domain]"
```

## Step 6: Add Initial Data

Create foundational data files in `[domain]/data/`:

**Examples:**
- Finance: `income-sources.md`, `expense-categories.md`, `accounts.md`
- Health: `baseline-metrics.md`, `fitness-goals.md`, `dietary-preferences.md`
- Schedule: `recurring-commitments.md`, `priorities.md`, `energy-levels.md`

## Step 7: Add Knowledge Resources

Create helpful reference files in `[domain]/knowledge/`:

**Examples:**
- Finance: `budget-tips.md`, `saving-strategies.md`
- Health: `workout-routines.md`, `nutrition-guide.md`
- Schedule: `time-blocking-templates.md`, `productivity-frameworks.md`

## Step 8: Test the Domain

1. Load the master agent: `_personal-automation/core/agents/master.md`
2. Select "List Domains" - verify your new domain appears
3. Select "List Available Agents" - verify your domain agent is listed
4. Load your domain agent directly
5. Test each workflow menu item

## Example: Creating a "Travel" Domain

### 1. Create Structure
```bash
cp -r _personal-automation/_docs/templates/domain-template/ _personal-automation/travel/
```

### 2. Create Config
`_personal-automation/travel/config.yaml`:
```yaml
domain_name: travel
domain_version: 1.0.0

agents_path: "{domain_root}/agents"
workflows_path: "{domain_root}/workflows"
data_path: "{domain_root}/data"
output_path: "{domain_root}/output"
knowledge_path: "{domain_root}/knowledge"

# Travel-specific settings
default_currency: USD
preferred_airlines: ["Delta", "United"]
loyalty_programs: true
travel_insurance_default: true
```

### 3. Create Agent
`_personal-automation/travel/agents/travel-planner.md`:
```markdown
---
name: "travel-planner"
description: "Travel Planning and Trip Coordination Expert"
---

# Travel Planner Agent

**Icon:** ✈️
**Role:** Travel Planning Expert
**Persona:** Atlas - Your knowledgeable travel companion

## Menu
- *menu - Redisplay menu
- *plan-trip - Plan a new trip
- *packing-list - Generate packing checklist
- *itinerary - Create trip itinerary
- *budget-trip - Create travel budget
- *dismiss - Dismiss agent
```

### 4. Create Workflow
`_personal-automation/travel/workflows/plan-trip/workflow.yaml`:
```yaml
name: plan-trip
description: Comprehensive trip planning workflow
domain: travel
version: 1.0.0

variables:
  workflow_name: "plan-trip"
  output_file: "{domain_root}/output/{current_date}-trip-plan.md"

inputs:
  - name: destination
    description: Where are you traveling?
    required: true
  - name: dates
    description: Travel dates (start - end)
    required: true
  - name: budget
    description: Total budget for the trip
    required: true
```

### 5. Register in Manifests

Add to `_config/agent-manifest.csv`:
```csv
"travel-planner","Travel Planner","Travel Planning Expert","✈️","Travel Planning Expert","Expert in trip planning, itinerary creation, and travel logistics","Enthusiastic and detail-oriented","Help users create amazing travel experiences","travel","_personal-automation/travel/agents/travel-planner.md"
```

Add to `_config/workflow-manifest.csv`:
```csv
"plan-trip","Comprehensive trip planning workflow","travel","_personal-automation/travel/workflows/plan-trip/workflow.yaml"
```

Update `_config/manifest.yaml`:
```yaml
  - name: travel
    config: "_personal-automation/travel/config.yaml"
    agents_count: 1
    workflows_count: 1
```

Update `core/config.yaml`:
```yaml
domains:
  travel: "{project-root}/_personal-automation/travel"
```

### 6. Add Data Files

`_personal-automation/travel/data/travel-preferences.md`:
```markdown
# Travel Preferences

## Accommodation
- Preferred: Hotels with kitchen
- Budget: Mid-range ($100-200/night)
- Must-haves: WiFi, workspace

## Transportation
- Preferred airlines: Delta, United
- Seat preference: Window
- Loyalty programs: Delta SkyMiles #123456

## Dietary
- Restrictions: Vegetarian
- Allergies: None
```

### 7. Add Knowledge

`_personal-automation/travel/knowledge/packing-tips.md`:
```markdown
# Packing Tips

## General Rules
- Roll clothes to save space
- Pack one extra day of clothes
- Keep essentials in carry-on

## By Trip Length
### Weekend (2-3 days)
- 2-3 outfits
- Minimal toiletries
- One pair of shoes

### Week-long
- 4-5 mix-and-match outfits
- Full toiletry kit
- Two pairs of shoes
```

## Tips

1. **Start with one workflow**: Don't create every possible workflow upfront
2. **Use existing patterns**: Reference finance or email domain for structure
3. **Keep agents focused**: One agent per domain is usually enough
4. **Organize data thoughtfully**: Your workflows will read from these files
5. **Document as you go**: Add to knowledge/ as you learn best practices

## Common Mistakes to Avoid

❌ Creating too many workflows at once
✅ Start with 1-2 core workflows, add more as needed

❌ Forgetting to update manifests
✅ Always register new agents and workflows in CSV files

❌ Hardcoding paths in workflows
✅ Use {domain_root}, {project-root} variables

❌ Mixing concerns between domains
✅ Keep each domain focused on its life area

## Next Steps

After creating your domain:
1. Test the agent loads correctly
2. Run your first workflow
3. Iterate based on real usage
4. Add more workflows as needed
5. Share patterns with other domains
