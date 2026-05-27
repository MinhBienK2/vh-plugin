# MarkFlow Codex Plugin

This repository is a Codex plugin marketplace for MarkFlow Hub.

It exposes one plugin, `mark-flow`, that bundles:

- a Codex plugin manifest at `plugins/mark-flow/.codex-plugin/plugin.json`
- a bundled MCP HTTP server config at `plugins/mark-flow/.mcp.json`
- a MarkFlow usage skill at `plugins/mark-flow/skills/mark-flow/SKILL.md`
- marketplace metadata at `.agents/plugins/marketplace.json`

## Requirements

- Codex CLI with plugin support.
- A MarkFlow MCP HTTP endpoint, defaulting to `https://markflow-api.vuihoc.vn/mcp`.

## Setup

Set `plugins/mark-flow/.mcp.json` to the MarkFlow MCP URL you want Codex to use. The default is:
`https://markflow-api.vuihoc.vn/mcp`.

Add this marketplace to Codex from GitHub:

```bash
codex plugin marketplace add https://github.com/MinhBienK2/vh-plugin.git
```

Restart Codex, open `/plugins`, choose `MarkFlow`, install it, and enable it.

## MCP Server

The plugin connects to the configured MarkFlow MCP HTTP URL. The plugin bundle does not run a backend service; it only configures Codex to call that MCP endpoint.

The bundled MCP tools are read-only:

- `search_scripts`
- `get_script`
- `get_script_comments`
- `search_daily_scripts`
- `get_daily_script`
- `get_daily_script_comments`
- `list_classes`
- `list_subjects`
- `list_teams`
- `list_target_audiences`
- `get_script_timeline`
- `get_daily_script_timeline`
