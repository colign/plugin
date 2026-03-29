---
name: propose
description: Create or update a structured proposal for a Colign change. Use when the user wants to define a new feature, write a problem statement, scope work, says "let's write a proposal", "create a new change", "I want to build X", or any request that involves capturing requirements and saving them to Colign.
---

# Propose a Change

Create a structured proposal and save it to the Colign platform. This captures the "why" and "what" — not the "how".

**Input**: The user describes what they want to build, or names an existing change to update.

---

## Workflow

1. Call `mcp__colign__list_projects` to find the target project
2. If no change exists yet, call `mcp__colign__create_change` with the project ID and a name
3. If a change already exists, call `mcp__colign__read_spec` with `doc_type: proposal` to check for existing content
4. Optionally call `mcp__colign__get_memory` for project conventions and context
5. Gather context from the user about what they want to build
6. Draft a structured proposal with these sections:
   - **Problem**: Why is this change needed? What user pain does it solve?
   - **Scope**: What specifically will change? Be concrete about deliverables.
   - **Out of Scope**: What is explicitly NOT included in this change?

7. Present the draft to the user for review
8. After approval, call `mcp__colign__write_spec` with `doc_type: proposal`. **Important**: The content must be a JSON string with keys `problem`, `scope`, and `outOfScope` (optional). Example:

```json
{"problem": "Users can't reset passwords...", "scope": "Add password reset flow...", "outOfScope": "SSO integration..."}
```

9. **Automatic Review** — after saving, launch a subagent review (see Review Phase below)
10. Present review results to the user
11. If revisions are needed, update the draft and call `write_spec` again
12. Repeat steps 9–11 until the user is satisfied

---

## Review Phase

After the proposal is saved, immediately spawn a subagent to perform an independent review. The reviewer has no knowledge of the drafting process — it evaluates the saved proposal with fresh eyes.

### How to run the review

Use the Agent tool with `subagent_type: "general-purpose"` and the following prompt structure:

```
You are a product proposal reviewer. You have no context about how this proposal was written — evaluate it purely on its merits.

## Proposal to Review
- Project: [project name]
- Change: [change name]
- Problem: [problem text]
- Scope: [scope text]
- Out of Scope: [outOfScope text]

## Project Context (if available)
[Insert any project memory from get_memory]

## Evaluate against these criteria

1. **Problem Clarity** — Is the problem specific and grounded in real user pain? Would a new team member understand *why* this matters?
2. **Scope Precision** — Are deliverables concrete and verifiable? Could someone tell when this is "done"?
3. **Completeness** — Are there obvious gaps? Dependencies or edge cases the scope should address but doesn't?
4. **Feasibility** — Does the scope feel realistic for a single change? Is it too broad or too narrow?
5. **Out of Scope** — Are the boundaries clear? Is anything important being excluded that should be included (or vice versa)?

## Output format

For each criterion, give:
- **Rating**: Good / Needs Work / Weak
- **Comment**: 1-2 sentences explaining why

Then provide:
- **Overall verdict**: Ready / Revise
- **Suggested improvements**: Concrete, actionable bullet points (only if verdict is Revise)
```

### After the review

Present the review results to the user in a clear summary:

```
📋 Proposal Review Results

Problem Clarity:  [rating] — [comment]
Scope Precision:  [rating] — [comment]
Completeness:     [rating] — [comment]
Feasibility:      [rating] — [comment]
Out of Scope:     [rating] — [comment]

Verdict: [Ready / Revise]
```

- If **Ready** — proceed to Next Steps
- If **Revise** — show suggested improvements, ask the user which ones to adopt, update the draft accordingly, call `write_spec` again, and re-run the review

The user can also skip the revision cycle at any point by saying they're satisfied.

---

## Guidelines

- **Keep proposals concise** — focus on "why" and "what", not implementation details
- **Always check existing content** before overwriting — call `read_spec` first
- **Ask when unclear** — don't guess requirements, ask the user
- **Use project memory** — call `get_memory` for project conventions if available
- **Don't design yet** — architecture, data models, and task breakdowns belong in the plan phase
- **Always run the review** — the review phase is not optional; it catches blind spots before the team sees the proposal

---

## Next Steps

After the proposal passes review, guide the user based on what they need:

```
Proposal saved and reviewed!

→ To continue with spec and tasks: /colign:plan
→ Or stop here — another team member can pick it up from the platform.
```
