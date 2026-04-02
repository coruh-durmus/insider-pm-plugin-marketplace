# Modify Flow — Detailed Workflow

1. Ask the component type to modify:
   > Skills | Commands | Agents | Competitor references | Config

2. List all components of that type (from the state scan)

3. PM picks one

4. Read the full current file and show it to the PM

5. Ask: "What would you like to change? Describe the modifications."

6. Apply the changes to the file

7. **Check for dependent files**: Search all files in `$COPILOT_ROOT` for references to this component's name or path. Show the PM which files reference it:
   > "This component is referenced in: [list of files]. I'll update them if needed."

8. Update dependent files where the change affects the reference (e.g., if a skill name changed, update commands that invoke it)

9. Proceed to Step 3 (Update Related Files)

## Dependency Types to Check

When modifying a component, search for these dependency patterns:

- **Skill renamed/changed**: Commands that invoke it, agents that reference it, other skills that delegate to it, setup wizard steps, README sections
- **Command renamed**: Skills that reference the command name, README command tables
- **Agent renamed**: Skills or commands that dispatch it, other orchestrator agents
- **Competitor reference changed**: Competitor-intelligence SKILL.md roster table, README competitor list
- **Config section changed**: Setup wizard steps in `commands/setup.md`, team presets in `config/presets/`
