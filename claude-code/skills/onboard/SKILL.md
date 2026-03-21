---
name: onboard
description: Set up and verify the Colign MCP connection. Use when the user first installs the plugin, says "set up colign", "connect to colign", asks how to get started, or when any colign MCP tool call fails with a connection or authentication error.
---

# Onboard to Colign

Guide the user through connecting to the Colign MCP server using interactive prompts. Use the `AskUserQuestion` tool for each input step so the user can respond inline.

## Workflow

### Step 1: Ask for the Colign URL

Use `AskUserQuestion` to ask:

> Colign 인스턴스 URL을 입력하세요.
> - SaaS 사용자: 그냥 Enter (기본값: https://api.colign.co)
> - Self-hosted: https://your-instance.com

If the user provides a URL, use it. If empty or skipped, default to `https://api.colign.co`.
Append `/mcp` to the URL if not already present.

### Step 2: Ask for the API token

Use `AskUserQuestion` to ask:

> API 토큰을 입력하세요.
> (https://app.colign.co > Settings > AI & API Keys에서 생성할 수 있습니다)

Wait for the user to paste their token (starts with `col_`).
If the token doesn't start with `col_`, warn and ask again.

### Step 3: Configure MCP connection

Read `~/.claude/settings.json`, merge the colign MCP server config, and write back:

```json
{
  "mcpServers": {
    "colign": {
      "url": "<URL>/mcp",
      "headers": {
        "Authorization": "Bearer <TOKEN>"
      }
    }
  }
}
```

Do not overwrite other existing settings — only merge the `mcpServers.colign` key.

### Step 4: Reload and verify

1. Tell the user to run `/reload-plugins`
2. After reload, call `mcp__colign__list_projects` to verify

### Step 5: Confirm setup

```
Colign MCP: Connected
URL: [url]
Projects: [count] accessible

You're all set! Here's how to get started:
- /colign:explore — Browse your projects and specs
- /colign:propose — Start a new change with a proposal
```

## Error Recovery

This skill should also trigger when other colign skills fail due to:
- Authentication failures (401)
- Connection refused errors
- MCP server not found

In these cases, check `~/.claude/settings.json` for the colign MCP config and re-run onboard.
