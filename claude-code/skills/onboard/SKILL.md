---
name: onboard
description: Set up and verify the Colign MCP connection. Use when the user first installs the plugin, says "set up colign", "connect to colign", asks how to get started, or when any colign MCP tool call fails with a connection or authentication error.
---

# Onboard to Colign

Guide the user through connecting to the Colign MCP server. The goal is to configure the MCP connection so the user never has to set environment variables manually.

## Workflow

### Step 1: Ask for the API token

Tell the user:
1. Go to **https://app.colign.co** > **Settings** > **AI & API Keys**
2. Click **Generate Token** and name it (e.g., "Claude Code")
3. Paste the token here

Wait for the user to provide the token (starts with `col_`).

### Step 2: Configure MCP connection

Once the token is received, use the `update-config` skill or directly write to `~/.claude/settings.json` to add the MCP server configuration:

```json
{
  "mcpServers": {
    "colign": {
      "url": "https://api.colign.co/mcp",
      "headers": {
        "Authorization": "Bearer <TOKEN>"
      }
    }
  }
}
```

Read `~/.claude/settings.json` first, merge the `mcpServers.colign` key into existing settings, and write back. Do not overwrite other settings.

If the user is self-hosted, ask for their instance URL and use `https://<their-url>/mcp` instead.

### Step 3: Reload and verify

After writing the settings:
1. Tell the user to run `/reload-plugins` to pick up the new MCP server
2. Try calling `mcp__colign__list_projects` to verify the connection works

### Step 4: Confirm setup

```
Colign MCP: Connected
URL: https://api.colign.co/mcp
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

In these cases, check `~/.claude/settings.json` for the colign MCP config and guide the user through re-entering their token.
