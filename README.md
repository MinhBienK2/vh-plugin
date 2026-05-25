# MarkFlow Codex Plugin

This repository is a local Codex plugin marketplace for MarkFlow Hub.

It exposes one plugin, `mark-flow`, that bundles:

- a Codex plugin manifest at `plugins/mark-flow/.codex-plugin/plugin.json`
- a bundled MCP stdio server config at `plugins/mark-flow/.mcp.json`
- a MarkFlow usage skill at `plugins/mark-flow/skills/mark-flow/SKILL.md`
- marketplace metadata at `.agents/plugins/marketplace.json`

## Requirements

- Codex CLI with plugin support.
- `uv` on `PATH`.
- MarkFlow backend source at `/home/pc599/Documents/mark-flow/backend`.
- MarkFlow FastAPI backend running at `MARKFLOW_API_BASE_URL` or `http://localhost:8000`.
- `MARKFLOW_ACCESS_TOKEN` in the environment that starts Codex.

## Local Setup

Start the MarkFlow backend:

```bash
cd /home/pc599/Documents/mark-flow/backend
uv sync
uv run uvicorn app.main:app --reload
```

Get a local MCP access token from MarkFlow:

```bash
curl -X POST http://localhost:8000/auth/token \
  -H 'Content-Type: application/json' \
  -d '{"bu":"vuihoc","username":"<username>","password":"<password>"}'
```

Export the returned access token before launching Codex:

```bash
export MARKFLOW_API_BASE_URL=http://localhost:8000
export MARKFLOW_ACCESS_TOKEN=<access-token>
```

Add this local marketplace to Codex:

```bash
codex plugin marketplace add /home/pc599/Documents/mark-flow-plugin
```

Restart Codex, open `/plugins`, choose `MarkFlow Local`, install `MarkFlow`, and enable it.

## MCP Server

The plugin launches the existing MarkFlow backend script:

```bash
cd /home/pc599/Documents/mark-flow/backend
uv run markflow-mcp
```

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
CODEX_HOME="$(mktemp -d)" codex plugin marketplace add /home/pc599/Documents/mark-flow-plugin
```
