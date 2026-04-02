# Add Flow — Detailed Workflow

Present the component type menu:
- A) Skill (with commands)
- B) Command (for an existing skill)
- C) Agent
- D) Competitor
- E) Config section

---

## Add Skill

1. **Interview** (one question at a time):
   - "What should this skill do? Describe it in a few sentences."
   - "When should it trigger? Describe the kinds of questions or requests that should activate it."
   - "What tools does it need?" (suggest based on description: ekb tools, Slack MCP, WebFetch, etc.)
   - "Does it need its own config section in team-config.json?" (if yes, ask what fields)
   - "What commands should it have?" (suggest based on description)

2. **Generate the SKILL.md** using the `skill-creator` skill:
   - Provide context: copilot purpose, existing skills, dependencies, permissions
   - Follow the same conventions as existing copilot skills (YAML frontmatter with name, description, version; body with Permissions, Prerequisites, Phases, Important Rules)
   - Iterate with skill-creator until quality is solid

3. **Generate associated commands**: One `.md` per command, following the established frontmatter pattern:
   ```yaml
   ---
   name: command-name
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

---

## Add Command

1. Ask which existing skill the command belongs to (list available skills)
2. Ask what the command does (brief description)
3. Ask what tools it needs (suggest based on the skill's existing tools)
4. Ask what arguments it accepts (optional)
5. Generate the command `.md` file
6. Write to `$COPILOT_ROOT/commands/<name>.md`
7. Proceed to Step 3

---

## Add Agent

1. Ask what the agent should do (describe its purpose)
2. Ask when it should be triggered (dispatched by a command, another agent, or a skill)
3. Ask if it is a worker (processes one item) or an orchestrator (dispatches multiple workers)
4. If orchestrator: ask which worker agents it dispatches
5. Ask what tools it needs
6. Ask what color it should be (suggest based on category: blue for research, green for orchestration, yellow for workers, cyan for intel)
7. Generate the agent `.md` file following the established pattern:
   - YAML frontmatter with name, description (including `<example>` blocks), model, color, tools
   - Body with role description, process steps, quality standards
8. Write to `$COPILOT_ROOT/agents/<name>.md`
9. Proceed to Step 3

---

## Add Competitor

1. Ask which competitor to add
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

---

## Add Config Section

1. Ask what configuration is needed and which capability it serves
2. Ask what fields the config section should have (name, type, example value for each)
3. Update `$COPILOT_ROOT/config/team-config.json.example` with the new section
4. Add a new Step to `$COPILOT_ROOT/commands/setup.md` for configuring this section
5. Proceed to Step 3
