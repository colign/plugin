---
name: implement
description: Implement code based on a Colign spec. Use when the user says "implement this", "start coding", "work on the next task", "build this feature", or any request to write code that has a corresponding spec or task list on the Colign platform. Reads specs and tasks from Colign, writes code locally, and updates task status as work progresses.
---

# Implement from Spec

Read the spec from Colign, implement the code locally, and update task progress on the platform.

---

## Workflow

### Select Project & Change

1. Call `mcp__colign__list_projects` to get available projects
2. If multiple projects exist, present the list and ask the user which project to work on
3. Call `mcp__colign__list_changes` for the selected project
4. Present changes that have tasks (filter out empty drafts) and ask the user which change to implement
5. If the user already specified a project or change (by name or ID), skip the corresponding selection step

### Load Spec & Tasks

1. Call `mcp__colign__get_change` to see the change's current stage and metadata
2. Call `mcp__colign__read_spec` with `doc_type: spec` to read the spec
3. Call `mcp__colign__read_spec` with `doc_type: proposal` for additional context
4. Call `mcp__colign__list_tasks` to see the task list and their current status
5. Call `mcp__colign__list_acceptance_criteria` to see what needs to be verified

If no spec or tasks exist, suggest running `/colign:plan` first.

### Task Loop

For each task:

1. Pick the next `todo` task (or let the user choose)
2. Call `mcp__colign__update_task` to set it to `in_progress`
3. Implement the code locally following the spec
4. Verify the implementation (build, tests, lint)
5. Call `mcp__colign__update_task` to set the task to `done`
6. Continue to next task or pause for user input

### Mid-Implementation Checks

Between tasks, periodically:
- Call `mcp__colign__get_gate_status` to see progress toward next stage
- Call `mcp__colign__list_acceptance_criteria` to check which AC are met
- Call `mcp__colign__toggle_acceptance_criteria` when an AC is satisfied by the implementation

---

## Guidelines

- **Always read the spec before coding** — don't guess requirements
- **Update task status in real-time** — the platform should reflect actual progress
- **Follow existing code conventions** — check the local codebase for patterns
- **Write tests alongside implementation** — not after
- **If the spec is unclear**, suggest running `/colign:propose` or `/colign:plan` to update it
- **If a task is too large**, suggest breaking it down via `/colign:plan`

## Verification

Before marking a task as `done`:
- Code compiles without errors
- Tests pass
- Implementation matches the spec requirements

## Code Review Phase

After all tasks are marked `done`, run a code review before moving to completion. This catches issues while context is fresh — before the team sees the code.

### Strategy: Skill-first, Subagent fallback

**Option A (preferred)** — If the `/plugin:code-review` skill is available, use it. It runs a structured security + quality review on uncommitted changes via `git diff`. Invoke it with the Skill tool:

```
Skill("plugin:code-review")
```

This covers hardcoded credentials, SQL/XSS injection, input validation, code complexity, missing error handling, and best practices — all with severity ratings and line-level locations.

**Option B (fallback)** — If `/plugin:code-review` is not available (skill not installed), spawn a subagent review instead. Use the Agent tool with `subagent_type: "code-reviewer"`:

```
Review the code changes for this Colign change.

## Spec
[Insert the spec content from read_spec]

## Acceptance Criteria
[Insert AC list from list_acceptance_criteria]

## Files Changed
[List the files that were created or modified during implementation]

## Review Criteria

1. **Spec Compliance** — Does the implementation fulfill the spec requirements? Are any deliverables missing?
2. **Code Quality** — Naming, structure, readability. Any overly complex logic that could be simplified?
3. **Error Handling** — Are failure cases handled? Missing validations at boundaries?
4. **Security** — Input sanitization, auth checks, no hardcoded secrets, OWASP basics
5. **Test Coverage** — Are critical paths tested? Any obvious gaps in test cases?
6. **Performance** — N+1 queries, unnecessary allocations, missing pagination, blocking calls

For each finding, provide:
- **Severity**: Critical / High / Medium / Low
- **Location**: File and line
- **Issue**: What's wrong
- **Fix**: Concrete suggestion

Then provide:
- **Overall verdict**: Approve / Request Changes
- **Summary**: 1-2 sentences on overall code health
```

### After the review

Present the results clearly:

```
Code Review Results

Spec Compliance:  [verdict]
Code Quality:     [X findings]
Error Handling:   [X findings]
Security:         [X findings]
Test Coverage:    [X findings]
Performance:      [X findings]

Verdict: [Approve / Request Changes]
```

- If **Approve** (or no CRITICAL/HIGH issues) — proceed to Next Step
- If **Request Changes** (or CRITICAL/HIGH issues found) — show findings grouped by severity, ask the user which to address. Fix the accepted items, re-run verification (build, tests), and re-run the review
- The user can skip the revision cycle at any point if they're satisfied

### When to run

- **Always** after all tasks are complete, before suggesting `/colign:complete`
- If the user implements tasks across multiple sessions, run the review when the last task is marked done

---

## Next Step

When all tasks are complete and the code review passes:

1. **Mark as Ready** — Call `mcp__colign__update_change` to set sub-status to `ready`, signaling the change is ready for review
2. Suggest running `/colign:complete` to finalize the change

```
All tasks done and code review passed!

→ Change marked as Ready for review
→ To finalize: /colign:complete
```
