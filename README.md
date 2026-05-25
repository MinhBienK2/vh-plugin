# MarkFlow Codex Plugin

This repository is a local Codex plugin marketplace for MarkFlow Hub.

It exposes one plugin, `mark-flow`, that bundles:

- a Codex plugin manifest at `plugins/mark-flow/.codex-plugin/plugin.json`
- a bundled MCP HTTP server config at `plugins/mark-flow/.mcp.json`
- a MarkFlow usage skill at `plugins/mark-flow/skills/mark-flow/SKILL.md`
- marketplace metadata at `.agents/plugins/marketplace.json`

## Requirements

- Codex CLI with plugin support.
- A deployed or local MarkFlow MCP Streamable HTTP endpoint.
- Default local MCP URL: `http://localhost:8001/mcp`.
- OAuth login through the MCP client for the configured MarkFlow MCP URL.

## Local Setup

Build/run MarkFlow as an HTTP MCP service. For local development from the source repo, run MarkFlow's backend and MCP HTTP service from `/home/pc599/Documents/mark-flow`; that source path is only for building/reading the service, not something the Codex plugin spawns at runtime.

```bash
cd /home/pc599/Documents/mark-flow/backend
uv sync
uv run uvicorn app.main:app --reload
```

```bash
cd /home/pc599/Documents/mark-flow/backend
MARKFLOW_MCP_TRANSPORT=streamable-http \
MARKFLOW_MCP_HOST=127.0.0.1 \
MARKFLOW_MCP_PORT=8001 \
MARKFLOW_MCP_PATH=/mcp \
MARKFLOW_API_BASE_URL=http://localhost:8000 \
MARKFLOW_OAUTH_ISSUER=http://localhost:8000 \
MARKFLOW_MCP_RESOURCE_URL=http://localhost:8001/mcp \
uv run markflow-mcp
```

If your MCP service is deployed somewhere else, change `plugins/mark-flow/.mcp.json` from `http://localhost:8001/mcp` to that deployed `/mcp` URL.

Add this local marketplace to Codex:

```bash
codex plugin marketplace add /home/pc599/Documents/vh-plugin
```

Restart Codex, open `/plugins`, choose `MarkFlow Local`, install `MarkFlow`, enable it, then complete the MCP OAuth login when Codex prompts for access.

## MCP Server

The plugin connects to the configured MarkFlow MCP HTTP URL. It does not run `uv`, does not depend on `/home/pc599/Documents/mark-flow/backend` at runtime, and does not read local backend files as data.

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
