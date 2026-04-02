# Phase 5 — Generation Templates

Generate the full plugin structure following established patterns from existing marketplace plugins.

## Always Generate

### `.claude-plugin/plugin.json`

Follow the exact format of existing plugins:
```json
{
  "name": "<plugin-name>",
  "version": "0.1.0",
  "description": "<description from interview>",
  "author": { "name": "Insider" },
  "dependencies": ["<detected-dependencies>"],
  "keywords": ["<relevant-keywords>"]
}
```

### `commands/<command-name>.md`

One file per command, with YAML frontmatter:
```markdown
---
name: command-name
description: <command description>
allowed-tools: <relevant tools>
argument-hint: "<hint>"
---

<Command instructions that invoke the main skill>
```

### `README.md`

Follow the established pattern:
- Plugin description
- Requirements (dependencies, MCPs)
- Getting Started (install deps, install plugin, setup)
- Commands table
- How It Works
- Configuration (if applicable)
- Permissions table

## Generate If Setup Wizard Needed

### `.gitignore`

Ignore `config/team-config.json` (contains team-specific values).

### `config/team-config.json.example`

Show the expected config format with placeholder values.

### `commands/setup-<name>.md`

Setup wizard command with:
- Dependency check
- Team name configuration
- Relevant config steps from interview answers

## Generate Skills Using skill-creator

For each skill the plugin needs, **invoke the `skill-creator` skill** to:

1. Capture intent — what the skill should do, when it should trigger, expected output
2. Generate an initial skill draft with proper frontmatter, trigger phrases, and step-by-step instructions
3. Create test prompts/evals for the skill
4. Run the skill against test cases
5. Iterate based on results until the skill is solid
6. Produce the final refined SKILL.md

Provide the plugin-creator context (plugin purpose, dependencies, commands, permissions) as input to skill-creator so it can generate skills that fit the plugin's architecture.
