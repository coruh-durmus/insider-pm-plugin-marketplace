# Phase 7 — Publishing Flow

Present two options:
> "Where should I put the plugin?
> - A) Add to the marketplace repo — I'll create the files and register it
> - B) Output to a separate folder — I'll generate it at a location you choose"

## Option A: Add to Marketplace

1. Create the plugin directory and all files under the `Plugins/` directory (sibling to other plugins)
2. Add the plugin entry to `Plugins/.claude-plugin/marketplace.json`
3. Add a row to the Available Plugins table in `Plugins/README.md`
4. Add the plugin and its owner to the **Review & Approval** table in `Plugins/README.md` (ask the PM who should be the owner for PR reviews)

### Optional PR Creation

Ask:
> "Would you like to open a PR to submit this plugin to the marketplace?
> The marketplace repo is at: https://github.com/Corcit/insider-pm-plugin-marketplace"

**If yes:**
1. Create a new branch: `feat/add-<plugin-name>`
2. Stage all new/modified files
3. Commit with message: `feat: add <plugin-name> plugin`
4. Push the branch: `git push origin feat/add-<plugin-name>`
5. Open a PR with:
   - Title: `feat: add <plugin-name>`
   - Body: Summary (name, description, dependencies, commands), test plan (install, setup, test each command, verify marketplace.json)
6. Show the PR URL to the PM

**If no:** Inform the PM the files are ready and can be submitted later.

## Option B: Separate Folder

1. Ask the PM for the output path
2. Create the plugin directory and all files there
3. Inform the PM:
   > "Plugin generated at `<path>`. To add it to the marketplace later:
   > 1. Copy the folder to the marketplace repo
   > 2. Register it in `.claude-plugin/marketplace.json`
   > 3. Open a PR to https://github.com/Corcit/insider-pm-plugin-marketplace"
