---
name: mark-flow
description: MarkFlow Hub workflow skill for using the bundled `markflow` MCP server to search, inspect, summarize, compare, and review scripts, daily scripts, comments, catalog metadata, and review timelines. Use when a user asks about MarkFlow script content, daily script content, approval/review context, comments, classes, subjects, teams, target audiences, or MCP-backed MarkFlow data access.
---

# MarkFlow

Use the bundled `markflow` MCP server as the first choice for MarkFlow Hub data. Treat the MCP tools as read-only business-data accessors, not as mutation tools.

## Preconditions

- Expect the MarkFlow FastAPI backend at `MARKFLOW_API_BASE_URL` or `http://localhost:8000`.
- Expect `MARKFLOW_ACCESS_TOKEN` in the environment that launches Codex for stdio MCP.
- Never ask the user to paste passwords or long-lived secrets into chat. If auth fails, ask them to refresh `MARKFLOW_ACCESS_TOKEN` in their shell and restart Codex.

## Route The Request

1. Classify the user's intent before calling tools:
   - Discovery: find scripts or daily scripts by keyword, status, team, class, audience, author type, or updated date.
   - Detail: inspect one known script or daily script ID.
   - Review context: inspect comments or timeline for a known script or daily script.
   - Catalog lookup: translate names like class, subject, team, or target audience into available catalog entries.
   - Summary/comparison: gather only the records needed, then synthesize from tool output.
2. Use catalog tools first when the user gives names but a search filter needs IDs.
3. Use search tools for broad discovery and detail tools only after there is a likely ID.
4. Fetch comments or timelines only when the user asks for review context, feedback, approval history, blockers, or audit trail.
5. Keep the answer scoped to the records returned. State when results are partial because filters, permissions, or page size limit the data.

## Tool Selection

- `search_scripts`: search normal scripts. Use uppercase status enums such as `DRAFT`, `SUBMITTED`, `REJECTED`, `RESUBMITTED`, or `APPROVED`.
- `get_script`: open one normal script. Use `include_sections=false` when only metadata is needed; include sections for summaries or content review.
- `get_script_comments`: read section comments for one normal script.
- `search_daily_scripts`: search daily scripts. Use this for item voice/source/note/caption/type and daily metadata.
- `get_daily_script`: open one daily script. Use `include_items=false` when only metadata is needed; include items for summaries or content review.
- `get_daily_script_comments`: read item comments for one daily script.
- `list_classes`, `list_subjects`, `list_teams`, `list_target_audiences`: resolve catalog values before filtering.
- `get_script_timeline`, `get_daily_script_timeline`: read approval/review event history.

## Query Discipline

- Preserve user-provided Vietnamese names, titles, and keywords exactly when searching.
- Use ISO 8601 date strings for updated-date filters.
- Keep page sizes modest unless the user asks for a larger scan.
- Prefer narrower filters over broad searches when the user gives enough context.
- Do not fabricate IDs, statuses, comments, or review events. If no matching record is returned, say that directly and suggest a narrower or broader query.

## Response Rules

- Separate facts from interpretation. Mention which records or tools the answer is based on when it matters.
- For summaries, include title/status/team/class when available before content findings.
- For review answers, distinguish comments from timeline events.
- If the user asks to create, update, approve, reject, delete, or schedule MarkFlow records, explain that the bundled MCP tools are read-only and ask whether they want code/API work instead.
