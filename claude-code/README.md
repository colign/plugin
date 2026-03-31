# Colign Plugin for Claude Code

Connect Claude Code to the [Colign](https://github.com/colign/colign) spec management platform. Read, write, and manage specs directly from your terminal.

No binary, no API tokens, no environment variables — just install and connect.

## Install

```bash
# 1. Register marketplace
/plugin marketplace add https://github.com/colign/plugin

# 2. Install plugin
/plugin install colign@colign

# 3. Reload and connect
/reload-plugins
```

On first connection, your browser will open for Colign login. After signing in, the MCP connection is established automatically via OAuth.

For self-hosted instances, set `COLIGN_MCP_URL` before installing:
```bash
export COLIGN_MCP_URL=https://your-instance.com/mcp
```

## Workflow

The plugin follows Colign's change lifecycle:

```
onboard → explore → propose → plan → implement → complete
```

| Skill | Stage | Description |
|-------|-------|-------------|
| `/colign:onboard` | Setup | Verify MCP connection |
| `/colign:explore` | Any | Browse projects, read specs, check change status |
| `/colign:propose` | Draft | Define the problem, scope, and write a structured proposal |
| `/colign:plan` | Draft → Spec | Break proposal into architecture, implementation steps, and tasks |
| `/colign:implement` | Spec | Code against the spec, update task progress |
| `/colign:complete` | Spec → Approved | Verify all tasks are done, advance the workflow |

### Stages & Sub-Status

Each change has a **stage** (`draft` → `spec` → `approved`) and a **sub-status** (`in_progress` | `ready`):

- `in_progress` — Work is ongoing, not ready for review
- `ready` — Work is complete, ready for the next stage or review

Use `mcp__colign__update_change` to toggle sub-status. The UI shows "Mark as Ready" / "Mark as In Progress" buttons.

Skills auto-trigger based on context (e.g., "implement the next task") or can be invoked explicitly.

## MCP Tools

| Tool | Description |
|------|-------------|
| `list_projects` | List all accessible projects |
| `get_change` | Get change details including stage and artifacts |
| `read_spec` | Read a spec document (proposal, design, spec, tasks) |
| `write_spec` | Write or update a spec document |
| `list_tasks` | List implementation tasks for a change |
| `update_task` | Update a task's status (todo, in_progress, done) |
| `suggest_spec` | Get AI suggestions for improving a spec |
| `list_acceptance_criteria` | List acceptance criteria (Given/When/Then) |
| `create_acceptance_criteria` | Create BDD-style acceptance criteria |

## Example Usage

```
# Explicit skill invocation
> /colign:explore
> Show me the current spec for change 42

# Auto-triggered by context
> I want to build a new notification system
  (triggers: propose)

> Break this down into implementation tasks
  (triggers: plan)

> Start coding the first task
  (triggers: implement)

> All tasks are done, let's wrap up
  (triggers: complete)
```
