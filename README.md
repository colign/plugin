# Colign Plugin for Claude Code

Connect Claude Code to the [Colign](https://github.com/colign/colign) spec management platform. Read, write, and manage specs directly from your terminal.

No binary, no API tokens, no environment variables — just install and connect via OAuth.

## Install

```bash
# 1. Register marketplace
/plugin marketplace add https://github.com/colign/plugin

# 2. Install plugin
/plugin install colign@colign

# 3. Reload and connect (browser opens for OAuth login)
/reload-plugins
```

For self-hosted instances:
```bash
export COLIGN_MCP_URL=https://your-instance.com/mcp
```

## Workflow Skills

```
onboard → explore → propose → plan → implement → complete
```

| Skill | Description |
|-------|-------------|
| `/colign:onboard` | Verify MCP connection |
| `/colign:explore` | Browse projects, read specs, check status |
| `/colign:propose` | Write a structured proposal (Problem, Scope, Approach) |
| `/colign:plan` | Break proposal into architecture and tasks |
| `/colign:implement` | Code against the spec, update task progress |
| `/colign:complete` | Verify all tasks done, advance workflow |

Skills auto-trigger by context or can be invoked explicitly.

## MCP Tools

| Tool | Description |
|------|-------------|
| `list_projects` | List all accessible projects |
| `get_change` | Get change details including stage |
| `read_spec` | Read a spec document |
| `write_spec` | Write or update a spec document |
| `list_tasks` | List implementation tasks |
| `update_task` | Update a task's status |
| `suggest_spec` | Get suggestions for improving a spec |
| `list_acceptance_criteria` | List acceptance criteria (Given/When/Then) |
| `create_acceptance_criteria` | Create BDD-style acceptance criteria |

## License

AGPL-3.0 — See [Colign](https://github.com/colign/colign) for details.
