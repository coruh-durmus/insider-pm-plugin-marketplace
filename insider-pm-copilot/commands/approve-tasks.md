---
description: Review and approve improved tasks one by one, or resume a previous review session
allowed-tools: Read, Write
argument-hint: "[--status | --reset ticket-key | --reset-all]"
---

Review and approve improved task descriptions: $ARGUMENTS

**Entry point:** `/approve-tasks`

## Behavior

Read `task-improvements/registry.json`. If the file does not exist, report: "No improvement registry found. Run `/improve-backlog` or `/improve-task` first."

### No arguments — Resume one-by-one review

1. Find all entries with `status: "pending_review"` or `status: "revision_requested"`, ordered by `review_index`.

2. Also check for `status: "stale"` entries. If any exist, inform the PM before starting the review:
   > "Note: [N] stale task(s) detected — their Jira descriptions changed since improvement. Run `/improve-backlog` to re-improve them before reviewing."
   List the stale ticket keys. Stale entries are excluded from the review queue — they need re-improvement first.

3. If no `pending_review` or `revision_requested` entries found: "All tasks have been reviewed. Use `/approve-tasks --status` to see the full registry."

4. If `pending_review` or `revision_requested` entries found, enter the one-by-one review loop:

   For each task (starting from the lowest unreviewed `review_index`):

   a. Read the improvement file at the entry's `output_file` path.

   b. **For `pending_review` tasks:** Present the **Improved Description** section.

   c. **For `revision_requested` tasks:** Present the **Improved Description** section AND the PM's stored `pm_feedback`. Then offer to regenerate the improvement incorporating the feedback — use the `pm-task-writer` skill at `${CLAUDE_PLUGIN_ROOT}/skills/pm-task-writer/SKILL.md` to re-improve the task. After regeneration, update the improvement file and present the new version.

   d. Ask the PM:
      ```
      [TICKET-KEY] — [Title] ([original_score]/10 → [improved_score]/10)
      [N of M remaining]

      [Display the improved description]

      Approve this improvement?
      A) Approve — I'll copy this to Jira
      B) Request changes — tell me what to adjust
      C) Skip — review later
      D) Stop — continue reviewing in another session
      ```

   e. Based on PM response:
      - **Approve**: Set `status: "approved"`, `approved_at` to current ISO timestamp. Proceed to next task.
      - **Request changes**: Ask the PM what to change. Set `status: "revision_requested"`, store `pm_feedback` with the PM's response. Proceed to next task.
      - **Skip**: Leave as `pending_review`. Proceed to next task.
      - **Stop**: Save registry immediately. Report: "Progress saved. N of M tasks reviewed. Run `/approve-tasks` to continue."

   f. **Write registry after EACH task decision** — ensures progress survives if the session drops.

5. After all tasks reviewed, present summary:
   ```
   Review complete: N approved, M revision requested, K skipped.
   ```

### `--status` — Show registry status

Read registry and display a status table:

```
Improvement Registry Status

| Status | Count | Tickets |
|--------|-------|---------|
| Approved | 12 | SD-1234, SD-1235, ... |
| Pending Review | 5 | SD-1240, SD-1241, ... |
| Revision Requested | 2 | SD-1238, SD-1239 |
| Stale | 1 | SD-1236 |

Total: 20 tasks tracked
Progress: 12/20 approved (60%)
```

### `--reset [ticket-key]` — Remove a single entry

Remove the specified entry from the registry. This allows the task to be re-improved in the next `/improve-backlog` run.

Confirm before removing: "Remove SD-1234 from the registry? This means it will be re-scored and re-improved in the next run."

### `--reset-all` — Clear entire registry

Confirm before clearing: "This will remove ALL entries from the registry. All tasks will be treated as new in the next run. Are you sure?"

If confirmed, write `{"version": 1, "entries": {}}` to `task-improvements/registry.json`.

## Rules

- **Never write to Jira.** This command only manages the local registry file.
- **Save registry after every individual decision.** Never batch writes.
- **Present one task at a time.** Do not show multiple tasks simultaneously.
- **Respect the review_index order.** Always present tasks in sequence.
- **For revision_requested tasks, always show the PM's previous feedback** before asking for a new decision.
