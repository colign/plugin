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
| `/colign:propose` | Write a structured proposal (Problem, Scope, Out of Scope) |
| `/colign:plan` | Break proposal into architecture and tasks |
| `/colign:implement` | Code against the spec, update task progress |
| `/colign:complete` | Verify all tasks done, advance workflow |

Skills auto-trigger by context or can be invoked explicitly.

## MCP Tools (33)

All tools are auto-permitted via `settings.json`.

| Category | Tools |
|----------|-------|
| **Projects** | `list_projects`, `create_project`, `update_project` |
| **Changes** | `create_change`, `list_changes`, `get_change`, `update_change`, `archive_change` |
| **Workflow** | `advance_stage`, `approve_change`, `reject_change` |
| **Specs** | `read_spec`, `write_spec`, `suggest_spec` |
| **Tasks** | `create_task`, `list_tasks`, `update_task`, `delete_task` |
| **Acceptance Criteria** | `list_acceptance_criteria`, `create_acceptance_criteria`, `toggle_acceptance_criteria`, `link_ac_to_test` |
| **Comments** | `list_comments`, `create_comment` |
| **Memory** | `get_memory`, `save_memory` |
| **Analytics** | `get_change_summary`, `get_change_history`, `get_project_dashboard`, `get_gate_status`, `get_work_context` |
| **User** | `get_me`, `list_members` |

## License

AGPL-3.0 — See [Colign](https://github.com/colign/colign) for details.
