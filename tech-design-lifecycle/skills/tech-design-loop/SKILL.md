---
name: "tech-design-lifecycle:tech-design-loop"
description: "Iterative tech design with automated review. Spawns a Tech Designer agent and a Reviewer agent that iterate until convergence. Use when the user wants to create a tech design and have it reviewed automatically, wants to run 'design loop', 'iterate on tech design', 'tech design with review', or mentions running tech-design and preflight together. Also triggers on 'design review loop', 'auto-review tech design', or 'iterate until convergence'."
---

# Tech Design Loop

Orchestrate two agents — Tech Designer and Reviewer — in an iterative loop that produces a progressively refined tech design. Each iteration creates new files (append-only, never modify previous iterations). The loop runs on autopilot until convergence.

## Input

The user provides one of:
- A Jira ticket ID (e.g., `SD-133740`)
- A task file path (e.g., `workspace/tasks/SD-133740-AoO/task.md`)
- A freeform description of what needs designing

## Setup

1. Determine the output directory. Default: `workspace/tasks/<ticket-or-name>/design-iterations/`
2. Create the output directory if it doesn't exist.

## File Convention

Each iteration produces new files. Previous files are never modified — this preserves the full audit trail.

| File | Created by | Purpose |
|------|-----------|---------|
| `tech-design-iteration-N.md` | Tech Designer | The design document |
| `tech-design-comments-iteration-N.md` | Reviewer | Review findings with severity levels |
| `tech-design-comment-answers-iteration-N.md` | Tech Designer | Point-by-point responses to reviewer |

## The Loop

### Iteration 1 — Initial Design

Spawn a **Tech Designer agent** with this prompt structure:

```
You are a Tech Designer agent. Your task is to create a tech design.

## Task
Read the task file at: <TASK_PATH>
Then use the /tech-design-lifecycle:tech-design skill to create a comprehensive tech design.
The Jira ticket is <TICKET_ID>.

## Instructions
- Invoke the /tech-design-lifecycle:tech-design skill with the Jira ticket
- Save the final tech design to: <OUTPUT_DIR>/tech-design-iteration-1.md
- Do NOT publish to Confluence or link to Jira — just save the file locally
- Research the actual codebase to understand current implementations
- Be thorough — cover all acceptance criteria from the task

The workspace is <REPO_ROOT>
```

Run this agent in the background. When it completes, proceed to the review.

### Iteration 1 — Review

Spawn a **Reviewer agent** with this prompt structure:

```
You are a Tech Design Reviewer agent. Your job is to critically review a tech design.

## Task
1. Read the tech design at: <OUTPUT_DIR>/tech-design-iteration-1.md
2. Invoke the /tech-design-lifecycle:preflight skill to review this tech design
3. Save your review to: <OUTPUT_DIR>/tech-design-comments-iteration-1.md

## Review Guidelines
- Each comment needs a severity: Critical / Major / Minor / Suggestion
- Comments must be actionable — say what's wrong AND what to do about it
- Verify claims against the codebase — don't trust the design's code references blindly
- Consider: error handling, backward compatibility, rollback, monitoring, testing,
  performance, security, deployment safety
- Check: does the design address ALL acceptance criteria from the task?

The workspace/codebase is at <REPO_ROOT>
```

Run this agent in the background. When it completes, check the findings to decide whether to continue.

### Iteration 2+ — Refine

Spawn a **Tech Designer agent** that reads the previous review:

```
You are a Tech Designer agent, iteration N. Improve the tech design based on reviewer feedback.

## Files to read
1. Current tech design: <OUTPUT_DIR>/tech-design-iteration-(N-1).md
2. Reviewer comments: <OUTPUT_DIR>/tech-design-comments-iteration-(N-1).md
3. Original task: <TASK_PATH>

## Your job
1. Read all files above
2. Address EVERY comment, especially Critical and Major ones
3. Research the codebase to verify claims and fix inaccuracies
4. Save improved design to: <OUTPUT_DIR>/tech-design-iteration-N.md
5. Save comment answers to: <OUTPUT_DIR>/tech-design-comment-answers-iteration-(N-1).md

Do NOT modify any previous iteration files.
Be concrete — use actual function signatures, file paths, code patterns from the codebase.
```

Then spawn a **Reviewer agent** that checks resolution:

```
You are a Tech Design Reviewer agent, iteration N.

## Files to read
1. Current tech design: <OUTPUT_DIR>/tech-design-iteration-N.md
2. Previous comments: <OUTPUT_DIR>/tech-design-comments-iteration-(N-1).md
3. Comment answers: <OUTPUT_DIR>/tech-design-comment-answers-iteration-(N-1).md

## Your job
1. Read all files above
2. Invoke the /tech-design-lifecycle:preflight skill to review the iteration-N tech design
3. For each previous comment, note: Resolved / Partially Resolved / Unresolved
4. Identify any NEW issues introduced in this iteration
5. Save review to: <OUTPUT_DIR>/tech-design-comments-iteration-N.md

## Guidelines
- If the design is solid with no blocking issues, say so clearly
- Don't invent issues — if it's good, it's good
- Research the codebase to verify claims
```

### Convergence

**Stop when** the reviewer finds 0 blocking issues (no Critical, High, or Major findings).

**Minimum**: 5 iterations (to ensure sufficient scrutiny even if early iterations look clean).

**Maximum**: 8 iterations. If still not converged after 8, stop and present the remaining issues to the user — the design likely needs human judgment on a tradeoff.

**Between iterations**, report progress to the user:

```
| Iter | Tech Design | Review | Issues |
|------|------------|--------|--------|
| 1    | Done       | Done   | 3C/5M/4m/3S |
| 2    | Done       | Done   | 1M/4m/2S |
| 3    | Running... | —      | — |
```

## Why This Works

The two agents have complementary blind spots. The Tech Designer knows the codebase deeply but can make assumptions. The Reviewer catches those assumptions by independently verifying claims against the code and checking against incident history and review patterns. Each iteration narrows the gap — typically converging in 4-6 iterations based on observed runs:

- Iteration 1-2: Catches factual errors (wrong function signatures, misunderstood APIs)
- Iteration 3-4: Catches design gaps (missing guards, unhandled edge cases)
- Iteration 5+: Polish (documentation clarity, metric naming, operational details)

## After Convergence

When the loop completes, summarize:

1. **Iteration table** showing issue counts per iteration
2. **Files produced** — list all files in the output directory
3. **Final design location** — path to the last `tech-design-iteration-N.md`
4. The final design is implementation-ready

Optionally offer to create a referenced version (`tech-design-comments-iteration-N-with-references.md`) that adds footnotes linking claims to source files and line numbers.
