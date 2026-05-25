# MarkFlow Codex Plugin

This repository is a local Codex plugin marketplace for MarkFlow Hub.

It exposes one plugin, `mark-flow`, that bundles:

- a Codex plugin manifest at `plugins/mark-flow/.codex-plugin/plugin.json`
- a bundled MCP HTTP server config at `plugins/mark-flow/.mcp.json`
- a MarkFlow usage skill at `plugins/mark-flow/skills/mark-flow/SKILL.md`
- marketplace metadata at `.agents/plugins/marketplace.json`

## Requirements

- Codex CLI with plugin support.
- A MarkFlow MCP HTTP endpoint, defaulting to `http://localhost:8001/mcp`.

## Setup

Set `plugins/mark-flow/.mcp.json` to the MarkFlow MCP URL you want Codex to use. The default is local development:
`http://localhost:8001/mcp`.

Add this local marketplace to Codex:

```bash
codex plugin marketplace add /home/pc599/Documents/vh-plugin
```

Restart Codex, open `/plugins`, choose `MarkFlow Local`, install `MarkFlow`, and enable it.

## MCP Server

The plugin connects to the configured MarkFlow MCP HTTP URL. It does not run local source files as part of plugin execution.

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

## Validate

From this repository:

```bash
python3 -m json.tool .agents/plugins/marketplace.json >/dev/null
python3 -m json.tool plugins/mark-flow/.codex-plugin/plugin.json >/dev/null
python3 -m json.tool plugins/mark-flow/.mcp.json >/dev/null
CODEX_HOME="$(mktemp -d)" codex plugin marketplace add /home/pc599/Documents/vh-plugin
```
