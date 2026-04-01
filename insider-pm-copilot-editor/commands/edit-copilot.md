---
description: Add, modify, remove, or view components in the PM copilot plugin
allowed-tools: Read, Write, Bash, WebFetch, WebSearch, Agent
argument-hint: "[add|modify|remove|view] [skill|command|agent|competitor|config]"
---

# Edit Copilot

Invoke the copilot-editor skill to manage the lifecycle of `insider-pm-copilot`.

Use the `copilot-editor` skill at `${CLAUDE_PLUGIN_ROOT}/skills/copilot-editor/SKILL.md`.

If `$ARGUMENTS` are provided, parse them to shortcut directly to the appropriate flow:
- `add skill` → Add Skill flow
- `add command` → Add Command flow
- `add agent` → Add Agent flow
- `add competitor` → Add Competitor flow
- `add config` → Add Config Section flow
- `modify <type>` → Modify flow for that component type
- `remove <type>` → Remove flow for that component type
- `view` → View current copilot state

If no arguments, the skill will present the action menu.
