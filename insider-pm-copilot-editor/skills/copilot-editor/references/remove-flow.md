# Remove Flow — Detailed Workflow

1. Ask the component type to remove:
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
   - If it is a skill with a `references/` directory, delete the entire skill directory
   - Remove references from all dependent files
   - If it had a config section: remove from `team-config.json.example` and remove the setup wizard step from `commands/setup.md`

8. Proceed to Step 3 (Update Related Files)

## Cleanup Checklist

After deletion, verify these are cleaned up:

- [ ] Component file deleted
- [ ] Skill directory deleted (if skill with subdirectories)
- [ ] References removed from README tables
- [ ] References removed from other skills/commands/agents
- [ ] Config section removed from `team-config.json.example` (if applicable)
- [ ] Setup wizard step removed from `commands/setup.md` (if applicable)
- [ ] Team presets updated or warned (if applicable)
