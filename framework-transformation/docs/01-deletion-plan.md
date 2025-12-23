# BMAD to Personal Automation Framework: Deletion Plan

**Purpose:** This document provides a step-by-step review of what can be safely deleted vs. what must remain from the BMAD framework.

**Review Status:** 🟡 PENDING REVIEW
**Last Updated:** 2025-12-22

---

## Review Instructions

For each section below:
- ✅ **KEEP** - Essential for framework operation
- ⚠️ **REVIEW** - Evaluate before deciding
- ❌ **DELETE** - Safe to remove (software development specific)

---

## Section 1: Core Engine Components

### ✅ KEEP - Essential Core Files

**Location:** `_bmad/core/`

| File/Folder | Purpose | Action | Notes |
|-------------|---------|--------|-------|
| `tasks/workflow.xml` | Universal workflow execution engine | **KEEP** | Domain-agnostic, powers all workflows |
| `agents/bmad-master.md` | Master orchestrator agent | **ADAPT** | Rename to `master.md`, update references |
| `config.yaml` | Global configuration | **KEEP** | Update values for personal use |
| `workflows/party-mode/` | Multi-agent collaboration | **KEEP** | Domain-agnostic, enables agent discussions |

**Migration Actions:**
1. Copy `core/tasks/workflow.xml` → New framework (no changes)
2. Copy `core/workflows/party-mode/` → New framework (no changes)
3. Adapt `core/agents/bmad-master.md` → Update paths and menu items
4. Recreate `core/config.yaml` with personal settings

---

### ⚠️ REVIEW - Core Supplementary

| File/Folder | Purpose | Decision |
|-------------|---------|----------|
| `workflows/brainstorming/` | Creative ideation workflow | **OPTIONAL** - Keep if you want brainstorming sessions |
| `tasks/advanced-elicitation.xml` | Advanced prompting techniques | **OPTIONAL** - Keep for better AI responses |
| `tasks/index-docs.md` | Document indexing | **OPTIONAL** - Useful for organizing outputs |
| `tools/shard-doc.md` | Split large documents | **OPTIONAL** - Useful for large personal docs |
| `resources/excalidraw/` | Diagram generation | **OPTIONAL** - Keep only if creating flowcharts |

**Review Questions:**
- Do you want creative brainstorming sessions? → Keep brainstorming workflow
- Do you plan to create diagrams? → Keep excalidraw resources
- Will you have large documents (>500 lines)? → Keep shard-doc tool

---

## Section 2: BMM Module - Software Development Specific

### ❌ DELETE - Entire Module Structure

**Location:** `_bmad/bmm/`

The entire BMM (BMAD Method Module) is software development specific and should be deleted. However, we'll extract patterns first.

---

### ❌ DELETE - Software Development Agents

**Location:** `_bmad/bmm/agents/`

| Agent File | Role | Delete? | Reason |
|------------|------|---------|--------|
| `pm.md` | Product Manager | ❌ YES | Software-specific (PRDs, epics) |
| `analyst.md` | Business Analyst | ❌ YES | Software requirements focus |
| `architect.md` | System Architect | ❌ YES | Software architecture decisions |
| `sm.md` | Scrum Master | ❌ YES | Agile/Sprint ceremonies |
| `dev.md` | Developer | ❌ YES | Code implementation |
| `tea.md` | Test Engineer/Architect | ❌ YES | Software testing |
| `ux-designer.md` | UX Designer | ❌ YES | UI/UX for software |
| `tech-writer.md` | Technical Writer | ❌ YES | Software documentation |
| `quick-flow-solo-dev.md` | Full-stack Developer | ❌ YES | Software development |

**Before Deletion:**
1. ✅ Extract agent structure pattern (XML template)
2. ✅ Extract persona structure (role, identity, communication_style, principles)
3. ✅ Extract menu-handler patterns
4. ✅ Note activation protocol structure

**What to Preserve:**
- Agent XML structure → Use as template for personal agents
- Persona definition pattern → Apply to Finance, Email, Health agents
- Menu system → Reuse for all new agents
- Activation steps → Adapt for new agents

---

### ❌ DELETE - Software Development Workflows

**Location:** `_bmad/bmm/workflows/`

#### Phase 1: Analysis Workflows

| Workflow | Purpose | Delete? | Notes |
|----------|---------|---------|-------|
| `1-analysis/create-product-brief/` | Business product brief | ❌ YES | Software product focused |
| `1-analysis/research/` | Market/technical research | ⚠️ **ADAPT** | Research pattern is universal - extract pattern |

**Action:** Extract research workflow pattern before deleting (web search, analysis, reporting pattern is reusable)

#### Phase 2: Planning Workflows

| Workflow | Purpose | Delete? | Notes |
|----------|---------|---------|-------|
| `2-plan-workflows/prd/` | Product Requirements Doc | ❌ YES | Software-specific |
| `2-plan-workflows/ux-design/` | UX design system | ❌ YES | Software UI/UX |

#### Phase 3: Solutioning Workflows

| Workflow | Purpose | Delete? | Notes |
|----------|---------|---------|-------|
| `3-solutioning/create-architecture/` | System architecture | ❌ YES | Software architecture |
| `3-solutioning/create-epics-and-stories/` | Agile stories | ❌ YES | Software development |
| `3-solutioning/check-implementation-readiness/` | Pre-dev validation | ❌ YES | Software-specific |

#### Phase 4: Implementation Workflows

| Workflow | Purpose | Delete? | Notes |
|----------|---------|---------|-------|
| `4-implementation/sprint-planning/` | Sprint setup | ❌ YES | Agile/software |
| `4-implementation/sprint-status/` | Sprint tracking | ❌ YES | Agile/software |
| `4-implementation/create-story/` | Generate user story | ❌ YES | Software development |
| `4-implementation/dev-story/` | Implement story | ❌ YES | Code implementation |
| `4-implementation/code-review/` | Code review | ❌ YES | Software development |
| `4-implementation/correct-course/` | Mid-sprint corrections | ⚠️ **PATTERN** | "Course correction" pattern is universal |
| `4-implementation/retrospective/` | Post-epic review | ⚠️ **PATTERN** | "Retrospective" pattern is universal |

**Before Deletion - Extract Patterns:**
1. ✅ Sprint-status tracking pattern → Adapt to personal goal tracking
2. ✅ Retrospective structure → Adapt to weekly/monthly reviews
3. ✅ Course correction pattern → Adapt to habit/goal adjustments

#### Testing Workflows

| Workflow | Purpose | Delete? | Notes |
|----------|---------|---------|-------|
| `testarch/framework/` | Test framework setup | ❌ YES | Software testing |
| `testarch/test-design/` | Test planning | ❌ YES | Software testing |
| `testarch/atdd/` | TDD workflow | ❌ YES | Software testing |
| `testarch/automate/` | Test automation | ❌ YES | Software testing |
| `testarch/test-review/` | Test quality review | ❌ YES | Software testing |
| `testarch/trace/` | Traceability matrix | ❌ YES | Software testing |
| `testarch/ci/` | CI/CD pipeline | ❌ YES | Software testing |
| `testarch/nfr-assess/` | Non-functional requirements | ❌ YES | Software testing |

**ALL TestArch workflows:** ❌ DELETE (entire directory)

#### Utility Workflows

| Workflow | Purpose | Delete? | Notes |
|----------|---------|---------|-------|
| `workflow-status/` | Status checker/router | ⚠️ **ADAPT** | Pattern is useful - "what should I do now?" |
| `workflow-status/init/` | Workflow initialization | ⚠️ **ADAPT** | Initialization pattern is reusable |
| `document-project/` | Brownfield codebase scan | ❌ YES | Software-specific |
| `generate-project-context/` | AI coding rules | ❌ YES | Software development |
| `bmad-quick-flow/create-tech-spec/` | Technical spec | ❌ YES | Software development |
| `bmad-quick-flow/quick-dev/` | Fast development | ❌ YES | Software development |

**Before Deletion - Extract Patterns:**
1. ✅ workflow-status routing logic → Adapt to personal automation routing
2. ✅ workflow-init pattern → Adapt to personal framework initialization

#### Excalidraw Workflows

| Workflow | Purpose | Delete? | Notes |
|----------|---------|---------|-------|
| `excalidraw-diagrams/create-diagram/` | Architecture diagrams | ⚠️ **OPTIONAL** | Keep if you want flowcharts |
| `excalidraw-diagrams/create-wireframe/` | UI wireframes | ❌ YES | Software UI design |
| `excalidraw-diagrams/create-flowchart/` | Process flowcharts | ⚠️ **OPTIONAL** | Useful for personal processes |
| `excalidraw-diagrams/create-dataflow/` | Data flow diagrams | ❌ YES | Software data modeling |

**Decision:** Keep only if you want to create visual diagrams for personal workflows

---

### ❌ DELETE - TestArch Knowledge Base

**Location:** `_bmad/bmm/testarch/knowledge/`

**All 32 testing pattern documents:** ❌ DELETE

These are comprehensive software testing best practices (Playwright, Cypress, API testing, etc.) - entirely software development focused.

| Category | Files | Action |
|----------|-------|--------|
| Testing frameworks | overview.md, playwright-config.md, fixture-architecture.md | ❌ DELETE |
| API testing | api-request.md, contract-testing.md, auth-session.md | ❌ DELETE |
| Network testing | intercept-network-call.md, network-recorder.md | ❌ DELETE |
| CI/CD | ci-burn-in.md, burn-in.md, selective-testing.md | ❌ DELETE |
| Test quality | test-quality.md, selector-resilience.md | ❌ DELETE |
| All others | (29 more files) | ❌ DELETE |

**Entire directory:** `_bmad/bmm/testarch/` → ❌ DELETE

---

### ❌ DELETE - BMM Documentation

**Location:** `_bmad/bmm/docs/`

| File/Folder | Purpose | Delete? | Notes |
|-------------|---------|---------|-------|
| `docs/*.md` | BMM user documentation | ❌ YES | Software development focused |
| `docs/images/` | Screenshot assets | ❌ YES | Software development screenshots |

---

### ❌ DELETE - BMM Teams & Data

**Location:** `_bmad/bmm/teams/` and `_bmad/bmm/data/`

| Folder | Purpose | Delete? | Notes |
|--------|---------|---------|-------|
| `teams/*.yaml` | Pre-configured agent teams | ⚠️ **PATTERN** | Extract team pattern, then delete |
| `data/` | Module-specific data files | ❌ YES | Software development data |

**Before Deletion:**
- ✅ Extract team.yaml structure for personal agent teams

---

### ⚠️ REVIEW - BMM Config File

**Location:** `_bmad/bmm/config.yaml`

**Action:** ⚠️ DO NOT DELETE - Extract structure first

This file shows how module configs inherit from core:
```yaml
# Will inherit from core/config.yaml
project_name: "My Project"
user_skill_level: 2
sprint_artifacts: "{output_folder}/sprints"
```

**Pattern to preserve:**
- Config inheritance (module inherits from core)
- Variable structure
- Path resolution

**Then:** ❌ DELETE this specific file (create new for life-management module)

---

## Section 3: Configuration & Manifests

### ⚠️ REVIEW - Configuration Directory

**Location:** `_bmad/_config/`

| File | Purpose | Delete? | Notes |
|------|---------|---------|-------|
| `manifest.yaml` | Installation metadata | ⚠️ **ADAPT** | Update for new framework |
| `agent-manifest.csv` | Agent registry | ⚠️ **ADAPT** | Clear entries, keep structure |
| `workflow-manifest.csv` | Workflow registry | ⚠️ **ADAPT** | Clear entries, keep structure |
| `task-manifest.csv` | Task registry | ⚠️ **OPTIONAL** | Keep if adding custom tasks |
| `files-manifest.csv` | File checksums | ❌ DELETE | Used for installation validation |

**Actions:**
1. Keep `agent-manifest.csv` structure → Clear all BMM agents, add personal agents
2. Keep `workflow-manifest.csv` structure → Clear all BMM workflows, add personal workflows
3. Update `manifest.yaml` → Change framework name and version
4. Delete `files-manifest.csv` → Not needed for custom framework

---

## Section 4: AgentVibes & Claude Integration

### ✅ KEEP - All AgentVibes Integration

**Location:** `.claude/`, `.agentvibes/`, `.mcp.json`

| Component | Action | Reason |
|-----------|--------|--------|
| `.claude/settings.json` | ✅ KEEP | Session hooks configuration |
| `.claude/hooks/` | ✅ KEEP | TTS integration hooks |
| `.claude/personalities/` | ✅ KEEP | Voice personalities |
| `.agentvibes/` | ✅ KEEP | AgentVibes configuration |
| `.mcp.json` | ✅ KEEP | MCP server configuration |

**No changes needed** - these are framework-agnostic

---

## Section 5: Claude Code Proxy

### ✅ KEEP - Entire Proxy System

**Location:** `claude-code-proxy/`

**Action:** ✅ KEEP (entirely separate tool)

This is a standalone monitoring tool, not part of BMAD framework. Keep as-is.

---

## Deletion Execution Plan

### Phase 1: Pattern Extraction (BEFORE ANY DELETION)

**Create backup documents in `framework-transformation/extracted-patterns/`:**

1. ✅ **agent-template.xml** - Extract from any BMM agent
2. ✅ **workflow-yaml-structure.md** - Document workflow.yaml format
3. ✅ **workflow-instructions-patterns.xml** - Common instruction patterns
4. ✅ **team-collaboration-pattern.yaml** - Team configuration structure
5. ✅ **status-tracking-pattern.yaml** - Sprint-status → goal-tracking adaptation
6. ✅ **retrospective-pattern.xml** - Review/reflection structure
7. ✅ **course-correction-pattern.xml** - Adjustment workflow structure

**Completion Criteria:** All patterns documented before proceeding to Phase 2

---

### Phase 2: Safe Deletion (In Order)

**Step 1: Delete Software-Specific Workflows**
```bash
rm -rf _bmad/bmm/workflows/1-analysis/create-product-brief/
rm -rf _bmad/bmm/workflows/2-plan-workflows/
rm -rf _bmad/bmm/workflows/3-solutioning/
rm -rf _bmad/bmm/workflows/4-implementation/
rm -rf _bmad/bmm/workflows/testarch/
rm -rf _bmad/bmm/workflows/bmad-quick-flow/
rm -rf _bmad/bmm/workflows/document-project/
rm -rf _bmad/bmm/workflows/generate-project-context/
```

**Step 2: Delete TestArch Knowledge Base**
```bash
rm -rf _bmad/bmm/testarch/
```

**Step 3: Delete Software Agents**
```bash
rm -rf _bmad/bmm/agents/pm.md
rm -rf _bmad/bmm/agents/analyst.md
rm -rf _bmad/bmm/agents/architect.md
rm -rf _bmad/bmm/agents/sm.md
rm -rf _bmad/bmm/agents/dev.md
rm -rf _bmad/bmm/agents/tea.md
rm -rf _bmad/bmm/agents/ux-designer.md
rm -rf _bmad/bmm/agents/tech-writer.md
rm -rf _bmad/bmm/agents/quick-flow-solo-dev.md
```

**Step 4: Delete BMM Documentation**
```bash
rm -rf _bmad/bmm/docs/
```

**Step 5: Delete BMM Teams & Data**
```bash
rm -rf _bmad/bmm/teams/
rm -rf _bmad/bmm/data/
```

**Step 6: Delete BMM Config**
```bash
rm _bmad/bmm/config.yaml
```

**Step 7: Clean Manifest Files**
- Edit `_bmad/_config/agent-manifest.csv` → Remove all BMM agent entries
- Edit `_bmad/_config/workflow-manifest.csv` → Remove all BMM workflow entries
- Delete `_bmad/_config/files-manifest.csv`

**Step 8: Optional - Remove Excalidraw if Not Needed**
```bash
# Only if you don't want diagram generation
rm -rf _bmad/core/resources/excalidraw/
rm -rf _bmad/bmm/workflows/excalidraw-diagrams/
```

**Step 9: Optional - Remove Core Optional Tools**
```bash
# Only if you don't need these
rm -rf _bmad/core/workflows/brainstorming/
rm -rf _bmad/core/tasks/advanced-elicitation.xml
rm -rf _bmad/core/tools/shard-doc.md
```

---

### Phase 3: Verify Minimal Framework

**After deletion, you should have:**

```
_bmad/
├── core/
│   ├── tasks/
│   │   └── workflow.xml                 ✅ KEPT
│   ├── workflows/
│   │   └── party-mode/                  ✅ KEPT
│   ├── agents/
│   │   └── bmad-master.md              ✅ KEPT (to adapt)
│   ├── resources/                       ⚠️ Optional
│   │   └── excalidraw/                  (if kept)
│   └── config.yaml                      ✅ KEPT
│
├── bmm/                                 ❌ ENTIRE MODULE DELETED
│
└── _config/
    ├── manifest.yaml                    ⚠️ To update
    ├── agent-manifest.csv              ⚠️ Cleared, structure kept
    └── workflow-manifest.csv           ⚠️ Cleared, structure kept
```

**Verification Checklist:**
- [ ] workflow.xml exists and unchanged
- [ ] party-mode workflow exists
- [ ] bmad-master.md exists
- [ ] core/config.yaml exists
- [ ] BMM directory is empty or deleted
- [ ] Patterns extracted to framework-transformation/extracted-patterns/

---

## Review Checkpoints

Before executing deletions, confirm:

- [ ] **Checkpoint 1:** All patterns extracted and documented
- [ ] **Checkpoint 2:** Core engine files identified and will be preserved
- [ ] **Checkpoint 3:** Understand what each deletion removes
- [ ] **Checkpoint 4:** Optional components (excalidraw, brainstorming) decision made
- [ ] **Checkpoint 5:** Backup of entire `_bmad/` directory created (just in case)

---

## Rollback Plan

If issues arise:

1. **Full Backup:** Before any deletion
   ```bash
   cp -r _bmad/ _bmad-backup-$(date +%Y%m%d)/
   ```

2. **Incremental Backups:** After each phase
   ```bash
   cp -r _bmad/ _bmad-phase2-$(date +%Y%m%d)/
   ```

3. **Git Tracking:** Commit after each major change
   ```bash
   git add .
   git commit -m "Phase 2: Deleted software-specific workflows"
   ```

---

## Next Document

After reviewing and approving this deletion plan, proceed to:
→ **02-framework-preservation.md** - What remains and how to use it

---

**Review Status:** 🟡 PENDING YOUR REVIEW
**Approval Required:** YES
**Est. Deletion Time:** 5 minutes (after approval)
**Risk Level:** LOW (with pattern extraction complete)
