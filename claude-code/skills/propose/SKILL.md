---
name: propose
description: Create or update a structured proposal for a Colign change. Use when the user wants to define a new feature, write a problem statement, scope work, says "let's write a proposal", "create a new change", "I want to build X", or any request that involves capturing requirements and saving them to Colign.
---

# Propose a Change

Create a structured proposal and save it to the Colign platform.

## Workflow

1. If no change exists yet, ask the user for the project and change name
2. Gather context from the user about what they want to build
3. Call `mcp__plugin_colign_colign__read_spec` with `doc_type: proposal` to check for existing content
4. Draft a structured proposal with these sections:
   - **problem** (required): Why is this change needed? What user pain does it solve?
   - **scope** (required): What specifically will change? Be concrete about deliverables.
   - **outOfScope** (optional): What is explicitly NOT included in this change?
   - **approach** (optional): Technical direction and key design decisions.
5. Present the draft to the user for review
6. After approval, call `mcp__plugin_colign_colign__write_spec` with `doc_type: proposal` and content as a **JSON string**
7. Generate acceptance criteria and save them using `mcp__plugin_colign_colign__create_acceptance_criteria`

## Content Format

The content MUST be a JSON string with these keys:

```json
{
  "problem": "Why is this change needed?",
  "scope": "What will change? Use \\n for line breaks.",
  "outOfScope": "What is NOT included?",
  "approach": "Technical direction."
}
```

Do NOT use markdown format. The web UI parses this JSON to display each section separately.

## Acceptance Criteria

After saving the proposal, generate BDD-style acceptance criteria for each key scenario. Use `mcp__plugin_colign_colign__create_acceptance_criteria` for each scenario.

Each AC has:
- **scenario**: A descriptive name for the test case
- **steps**: A JSON array of steps with keywords and text

Available keywords: `Given`, `When`, `Then`, `And`, `But`

Example:
```json
{
  "scenario": "User can create a new project",
  "steps": [
    {"keyword": "Given", "text": "a user is logged in"},
    {"keyword": "When", "text": "they click 'New Project'"},
    {"keyword": "And", "text": "they enter a project name"},
    {"keyword": "Then", "text": "the project is created"},
    {"keyword": "But", "text": "the project name must be unique"}
  ]
}
```

Generate 2-4 acceptance criteria that cover the main success scenarios and edge cases from the proposal scope.

## Guidelines

- Keep proposals concise — focus on the "why" and "what", not implementation details
- Always check existing content before overwriting
- Ask clarifying questions if the scope is unclear
- Use `\n` for line breaks within sections, not actual newlines

## Next Step

After the proposal and acceptance criteria are saved, suggest running `/colign:plan` to generate the implementation plan and tasks.
