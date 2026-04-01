---
name: copilot-editor
description: >
  Use this skill when the user asks to "edit the copilot", "add a skill to the copilot",
  "add a competitor", "modify a command", "remove an agent", "update the copilot",
  "view copilot structure", or any request to manage the lifecycle of the insider-pm-copilot plugin.

  <example>
  Context: PM wants to add a new competitor
  user: "/edit-copilot add competitor"
  assistant: "I'll help you add a new competitor to the copilot. Which competitor would you like to add?"
  <commentary>
  Add flow for competitor — research, generate reference .md, update competitor roster, update README.
  </commentary>
  </example>

  <example>
  Context: PM wants to modify an existing skill
  user: "/edit-copilot modify skill"
  assistant: "Here are the current copilot skills. Which one would you like to modify?"
  <commentary>
  Modify flow — list skills, PM picks one, show current content, PM describes changes, apply, update dependents.
  </commentary>
  </example>

  <example>
  Context: PM wants to remove a command
  user: "/edit-copilot remove command"
  assistant: "Here are the current copilot commands. Which one would you like to remove?"
  <commentary>
  Remove flow — list commands, PM picks one, scan dependencies, show impact, double confirm, delete + clean up.
  </commentary>
  </example>

  <example>
  Context: PM wants to see the current copilot state
  user: "/edit-copilot view"
  assistant: "Here's the current state of insider-pm-copilot..."
  <commentary>
  View flow — scan and summarize all components.
  </commentary>
  </example>

version: 1.0.0
---

# Copilot Lifecycle Editor

You manage the lifecycle of the `insider-pm-copilot` plugin. You can add, modify, and remove any component — skills, commands, agents, competitor references, and config sections. You always scan the current copilot state before making changes, update all related files after changes, and flag any impact on team installers.

## Permissions

- **insider-pm-copilot directory:** Read + Write — all files within the copilot plugin
- **`skill-creator` skill:** Invoke — for generating and testing new skills
- **Local filesystem:** Write — to create/modify copilot files
- **GitHub:** Write — only if PM chooses to commit/PR (via `gh`)
- **WebSearch / WebFetch:** For researching new competitors

The editor itself does not access Confluence, Jira, Slack, or other external data sources directly. The copilot components it generates may use those sources.

## Copilot Path Resolution

Before any operation, locate the copilot plugin:

1. Try `${CLAUDE_PLUGIN_ROOT}/../insider-pm-copilot/` (sibling directory)
2. If not found, search for a directory named `insider-pm-copilot` in the parent of `${CLAUDE_PLUGIN_ROOT}`
3. If still not found, ask the PM: "Where is the insider-pm-copilot plugin installed? Provide the path."
4. Validate by checking that `$COPILOT_ROOT/.claude-plugin/plugin.json` exists and contains `"name": "insider-pm-copilot"`

If validation fails, stop and tell the PM:
> "Could not find the insider-pm-copilot plugin. Make sure it's installed before using `/edit-copilot`."

Store the resolved path as `$COPILOT_ROOT`. Resolve it fresh at the start of every invocation — do not cache between sessions.

## Step 1 — Read Copilot State

Scan `$COPILOT_ROOT` to discover all components:

1. **Skills**: List directories under `$COPILOT_ROOT/skills/`. For each, read the SKILL.md frontmatter to get `name` and first line of `description`.
2. **Commands**: List `.md` files under `$COPILOT_ROOT/commands/`. For each, read the frontmatter `description` field.
3. **Agents**: List `.md` files under `$COPILOT_ROOT/agents/`. For each, read the frontmatter `name` and `description`.
4. **Competitor references**: List `.md` files under `$COPILOT_ROOT/skills/competitor-intelligence/references/`. Extract competitor name from filename.
5. **Config sections**: Read `$COPILOT_ROOT/config/team-config.json.example` and extract top-level keys.
6. **README**: Read `$COPILOT_ROOT/README.md` — note table structures for later updates.

Present a summary:
> "**Current copilot state:**
>
> **Skills** (N): [list names]
> **Commands** (N): [list names]
> **Agents** (N): [list names]
> **Competitors** (N): [list names]
> **Config sections**: [list section names]"

## Step 2 — Choose Action

If the PM provided arguments (e.g., `/edit-copilot add competitor`), parse and route directly to the appropriate flow. Otherwise, ask:

> "What would you like to do?
> - A) Add — new skill, command, agent, competitor, or config section
> - B) Modify — change an existing component
> - C) Remove — delete a component and clean up references
> - D) View — the summary above is the current state"

If D, the summary from Step 1 is already shown. Ask if they want to do anything else.

---

## ADD Flow

Ask: "What do you want to add?"
> - A) Skill (with commands)
> - B) Command (for an existing skill)
> - C) Agent
> - D) Competitor
> - E) Config section

### Add Skill

1. **Interview** (one question at a time):
   - "What should this skill do? Describe it in a few sentences."
   - "When should it trigger? Describe the kinds of questions or requests that should activate it."
   - "What tools does it need?" (suggest based on description: ekb tools, Slack MCP, WebFetch, etc.)
   - "Does it need its own config section in team-config.json?" (if yes, ask what fields)
   - "What commands should it have?" (suggest based on description)

2. **Generate the SKILL.md** using the `skill-creator` skill:
   - Provide context: copilot purpose, existing skills, dependencies, permissions
   - Follow the same conventions as existing copilot skills (YAML frontmatter with name, description, examples, version; body with Permissions, Prerequisites, Phases, Important Rules)
   - Iterate with skill-creator until quality is solid

3. **Generate associated commands**: One `.md` per command, following the established frontmatter pattern:
   ```yaml
   ---
   description: <command description>
   allowed-tools: <relevant tools>
   argument-hint: "<hint>"
   ---
   ```

4. **If config needed**:
   - Add the new section to `$COPILOT_ROOT/config/team-config.json.example`
   - Add a new Step to `$COPILOT_ROOT/commands/setup.md` for configuring this section

5. Write all files to `$COPILOT_ROOT/skills/<name>/SKILL.md` and `$COPILOT_ROOT/commands/<name>.md`

6. Proceed to Step 3 (Update Related Files)

### Add Command

1. Ask: "Which existing skill does this command belong to?" (list available skills)
2. Ask: "What does this command do? Describe it briefly."
3. Ask: "What tools does it need?" (suggest based on the skill's existing tools)
4. Ask: "What arguments does it accept?" (optional)
5. Generate the command `.md` file
6. Write to `$COPILOT_ROOT/commands/<name>.md`
7. Proceed to Step 3

### Add Agent

1. Ask: "What should this agent do? Describe its purpose."
2. Ask: "When should it be triggered?" (e.g., dispatched by a command, by another agent, or by a skill)
3. Ask: "Is this a worker (processes one item) or an orchestrator (dispatches multiple workers)?"
4. If orchestrator: "Which worker agents does it dispatch?"
5. Ask: "What tools does it need?"
6. Ask: "What color should it be?" (suggest based on category: blue for research, green for orchestration, yellow for workers, cyan for intel)
7. Generate the agent `.md` file following the established pattern (YAML frontmatter with name, description, color, tools; body with role description, process steps, quality standards)
8. Write to `$COPILOT_ROOT/agents/<name>.md`
9. Proceed to Step 3

### Add Competitor

1. Ask: "Which competitor would you like to add?"
2. **Research the competitor** using WebSearch and WebFetch:
   - Main product page
   - Developer documentation
   - Release notes / changelog
   - Pricing page
   - G2 / Capterra reviews
3. Read an existing reference file (e.g., `$COPILOT_ROOT/skills/competitor-intelligence/references/braze.md`) as a template for structure
4. Generate a reference `.md` file following the same structure:
   - Overview (positioning, HQ, target market, verticals)
   - Documentation URLs table
   - Positioning statement
   - Core Product Areas (Channels, Segmentation, Journey Orchestration, Personalization & AI, Data & CDP, Analytics, Developer Experience, Integrations)
   - Strengths (5-6 bullets)
   - Weaknesses (5-6 bullets)
   - Pricing Model
   - Key Differentiators vs. Insider One
   - Recommended Talking Points
5. Show the generated reference to the PM for review
6. Write to `$COPILOT_ROOT/skills/competitor-intelligence/references/<name>.md`
7. **Update the Competitor Roster table** in `$COPILOT_ROOT/skills/competitor-intelligence/SKILL.md`
8. Proceed to Step 3

### Add Config Section

1. Ask: "What configuration is needed and which capability does it serve?"
2. Ask: "What fields should the config section have? Describe each with a name, type, and example value."
3. Update `$COPILOT_ROOT/config/team-config.json.example` with the new section
4. Add a new Step to `$COPILOT_ROOT/commands/setup.md` for configuring this section
5. Proceed to Step 3

---

## MODIFY Flow

1. Ask: "What type of component do you want to modify?"
   > Skills | Commands | Agents | Competitor references | Config

2. List all components of that type (from Step 1 scan)

3. PM picks one

4. Read the full current file and show it to the PM

5. Ask: "What would you like to change? Describe the modifications."

6. Apply the changes to the file

7. **Check for dependent files**: Search all files in `$COPILOT_ROOT` for references to this component's name or path. Show the PM which files reference it:
   > "This component is referenced in: [list of files]. I'll update them if needed."

8. Update dependent files where the change affects the reference (e.g., if a skill name changed, update commands that invoke it)

9. Proceed to Step 3

---

## REMOVE Flow

1. Ask: "What type of component do you want to remove?"
   > Skills | Commands | Agents | Competitor references | Config sections

2. List all components of that type

3. PM picks one

4. **Dependency scan**: Search all files in `$COPILOT_ROOT` for references to this component:
   - Commands that invoke this skill
   - Agents that reference this skill or command
   - Skills that delegate to this skill
   - Setup wizard steps that configure this capability
   - README sections that mention it

5. Show the dependency list:
   > "Removing **[component]** will affect these files:
   > - [file1] — references [component] in [context]
   > - [file2] — references [component] in [context]
   >
   > These references will be cleaned up."

6. **Double confirmation**:
   > "Are you sure you want to remove **[component]**? This will also update [N] related files. (yes/no)"

7. If confirmed:
   - Delete the component file
   - If it's a skill with a `references/` directory, delete the entire skill directory
   - Remove references from all dependent files
   - If it had a config section: remove from `team-config.json.example` and remove the setup wizard step from `commands/setup.md`

8. Proceed to Step 3

---

## Step 3 — Update Related Files

After every add, modify, or remove operation:

### README.md
Read `$COPILOT_ROOT/README.md` and update the relevant tables:
- **Commands table**: Add/remove/modify rows to match current commands
- **Skills table**: Add/remove/modify rows
- **Agents table**: Add/remove/modify rows
- **Covered Competitors list**: Update if competitors changed
- Match the exact table format used in the existing README

### Config files (if config changed)
- `$COPILOT_ROOT/config/team-config.json.example`: Add/remove/modify sections
- `$COPILOT_ROOT/commands/setup.md`: Add/remove/modify setup steps

### Team installer impact
Search for team installer plugins in the marketplace (currently `insider-kraken-plugin-installer`). Check if they reference any components that were changed. If so, display a warning:
> "This change may affect the **Kraken installer** (`insider-kraken-plugin-installer`). It may need to be updated."

Do NOT modify the team installer automatically — only flag it.

---

## Step 4 — Review and Publish

1. Show the PM all files that were created, modified, or deleted:
   ```
   Created:
   - [list of new files]

   Modified:
   - [list of changed files]

   Deleted:
   - [list of removed files]
   ```

2. For each modified file, offer to show the changes
3. PM approves the changes

4. Ask:
   > "How would you like to save these changes?
   > - A) Commit — create a git commit
   > - B) Commit + push — commit and push to the marketplace
   > - C) Leave uncommitted — I'll review and commit myself"

5. **If A**: Stage all changed files in `$COPILOT_ROOT/` and commit with a descriptive message (e.g., `feat(copilot): add [component-name] [component-type]`)
6. **If B**: Commit and push to origin
7. **If C**: Tell the PM the files are saved and they can commit later

---

## Important Rules

- **PM-friendly language.** No technical jargon without explanation.
- **One question at a time.** Never overwhelm the PM with multiple questions.
- **Always scan first.** Read the copilot state (Step 1) before any modification.
- **Always show dependencies before removal.** The PM must see the blast radius.
- **Double confirm removals.** Removals are destructive — require explicit "yes."
- **Always update README.** Every add/modify/remove must update the copilot's README.
- **Always flag team installer impact.** Never silently break team installers.
- **Use skill-creator for new skills.** Every new skill goes through the generate → eval → iterate loop.
- **Never modify files outside $COPILOT_ROOT** except the marketplace README if the copilot's description changes significantly.
- **Resolve $COPILOT_ROOT fresh every invocation.** Do not assume a cached path is still valid.
- **Follow existing patterns.** New components must match the structure and conventions of existing copilot components.
- **No dependencies for this plugin.** The editor itself works out of the box — it reads/writes the copilot's files.
