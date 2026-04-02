---
name: copilot-editor
description: >
  This skill should be used when the user asks to "edit the copilot", "add a skill to the copilot",
  "add a competitor", "modify a command", "remove an agent", "update the copilot",
  "view copilot structure", "manage copilot components", or any request to add, modify,
  remove, or view components in the insider-pm-copilot plugin.
version: 1.0.0
---

# Copilot Lifecycle Editor

Manage the lifecycle of the `insider-pm-copilot` plugin — add, modify, and remove skills, commands, agents, competitor references, and config sections. Always scan the current copilot state before making changes, update all related files after changes, and flag any impact on team presets.

## Permissions

- **insider-pm-copilot directory:** Read + Write on all files within the copilot plugin
- **`skill-creator` skill:** Invoke for generating and testing new skills
- **Local filesystem:** Write to create/modify copilot files
- **GitHub:** Write only if the PM chooses to commit/PR (via `gh`)
- **WebSearch / WebFetch:** For researching new competitors

The editor itself does not access Confluence, Jira, Slack, or other external data sources directly. The copilot components it generates may use those sources.

## Copilot Path Resolution

Before any operation, locate the copilot plugin:

1. Try `${CLAUDE_PLUGIN_ROOT}/../insider-pm-copilot/` (sibling directory)
2. If not found, search for a directory named `insider-pm-copilot` in the parent of `${CLAUDE_PLUGIN_ROOT}`
3. If still not found, ask the PM for the path
4. Validate by checking that `$COPILOT_ROOT/.claude-plugin/plugin.json` exists and contains `"name": "insider-pm-copilot"`

If validation fails, stop and inform the PM:
> "Could not find the insider-pm-copilot plugin. Make sure it's installed before using `/edit-copilot`."

Resolve `$COPILOT_ROOT` fresh at the start of every invocation — do not cache between sessions.

## Step 1 — Read Copilot State

Scan `$COPILOT_ROOT` to discover all components:

1. **Skills**: List directories under `$COPILOT_ROOT/skills/`. Read each SKILL.md frontmatter for `name` and first line of `description`.
2. **Commands**: List `.md` files under `$COPILOT_ROOT/commands/`. Read each frontmatter `description` field.
3. **Agents**: List `.md` files under `$COPILOT_ROOT/agents/`. Read each frontmatter `name` and `description`.
4. **Competitor references**: List `.md` files under `$COPILOT_ROOT/skills/competitor-intelligence/references/`. Extract competitor name from filename.
5. **Config sections**: Read `$COPILOT_ROOT/config/team-config.json.example` and extract top-level keys.
6. **README**: Read `$COPILOT_ROOT/README.md` — note table structures for later updates.

Present a summary to the PM showing counts and names for each component type.

## Step 2 — Choose Action

If arguments were provided (e.g., `/edit-copilot add competitor`), parse and route directly to the appropriate flow. Otherwise, present:

- **A) Add** — new skill, command, agent, competitor, or config section
- **B) Modify** — change an existing component
- **C) Remove** — delete a component and clean up references
- **D) View** — the summary from Step 1

## Action Flows

Each action follows a guided workflow. Consult the detailed reference for each flow:

### Add Flow

Guided creation of new components through interview + generation. Supports five component types: skills (with commands), standalone commands, agents, competitors, and config sections.

For the full add workflow including interview questions and generation steps, consult **`references/add-flow.md`**.

### Modify Flow

Select a component, review its current content, apply changes, and update all dependents.

For the full modify workflow, consult **`references/modify-flow.md`**.

### Remove Flow

Select a component, scan dependencies, show the PM the blast radius, double-confirm, then delete and clean up all references.

For the full remove workflow, consult **`references/remove-flow.md`**.

## Step 3 — Update Related Files

After every add, modify, or remove operation:

### README.md
Read `$COPILOT_ROOT/README.md` and update the relevant tables (Commands, Skills, Agents, Covered Competitors). Match the exact table format used in the existing README.

### Config files (if config changed)
- `$COPILOT_ROOT/config/team-config.json.example`: Add/remove/modify sections
- `$COPILOT_ROOT/commands/setup.md`: Add/remove/modify setup steps

### Team installer impact
Check if any team presets in `$COPILOT_ROOT/config/presets/` reference changed components. If so, display a warning and offer to update the affected presets automatically.

## Step 4 — Review and Publish

1. Show all files created, modified, or deleted
2. For each modified file, offer to show the changes
3. PM approves the changes
4. Present save options:
   - **A) Commit** — create a git commit with a descriptive message
   - **B) Commit + push** — commit and push to the marketplace
   - **C) Leave uncommitted** — PM reviews and commits later

## Important Rules

- **PM-friendly language.** No technical jargon without explanation.
- **One question at a time.** Never overwhelm the PM with multiple questions.
- **Always scan first.** Read the copilot state (Step 1) before any modification.
- **Always show dependencies before removal.** The PM must see the blast radius.
- **Double confirm removals.** Removals are destructive — require explicit "yes."
- **Always update README.** Every add/modify/remove must update the copilot's README.
- **Always flag team preset impact.** Never silently break team presets.
- **Use skill-creator for new skills.** Every new skill goes through the generate, eval, iterate loop.
- **Never modify files outside $COPILOT_ROOT** except the marketplace README if the copilot's description changes significantly.
- **Follow existing patterns.** New components must match the structure and conventions of existing copilot components.

## Additional Resources

### Reference Files

For detailed workflows, consult:
- **`references/add-flow.md`** — Full add workflow for all five component types
- **`references/modify-flow.md`** — Full modify workflow with dependency checking
- **`references/remove-flow.md`** — Full remove workflow with dependency scan and cleanup
