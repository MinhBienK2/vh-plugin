---
name: mark-flow
description: Uses the bundled `markflow` MCP server to search, inspect, summarize, compare, and review MarkFlow Hub scripts, daily scripts, catalog metadata, comments, and review timelines. Use when a user asks about MarkFlow script content, daily script content, approval/review context, comments, classes, subjects, teams, target audiences, or MCP-backed MarkFlow data access.
---

# MarkFlow

Use the bundled `markflow` MCP server first for MarkFlow Hub business data. Treat the tools as read-only accessors; RBAC, BU scoping, and record visibility are enforced by the MarkFlow backend behind the authenticated token.

## Preconditions

- Expect the MarkFlow FastAPI backend at `MARKFLOW_API_BASE_URL` or `http://localhost:8000`.
- Expect `MARKFLOW_ACCESS_TOKEN` in the environment that launches Codex for stdio MCP.
- Never ask the user to paste passwords or long-lived secrets into chat. If auth fails, ask them to refresh `MARKFLOW_ACCESS_TOKEN` in their shell and restart Codex.

## Route The Request

1. Classify the user's intent before calling tools:
   - Catalog lookup: translate class, subject, team, or target audience names into available catalog entries.
   - Script discovery: find normal scripts by keyword, status, team, class, target audience, author person type, or updated date.
   - Daily discovery: find daily scripts by keyword, status, team, or updated date.
   - Detail/summary: inspect one known script or daily script ID, including content only when needed.
   - Review context: inspect comments or timeline for one known script or daily script.
2. Use catalog tools first when the user gives names but a search filter needs integer IDs.
3. Search before detail unless the user already supplied a likely ID.
4. Fetch comments or timelines only for review context, feedback, approval history, blockers, or audit trail.
5. Keep answers scoped to returned records. State when filters, permissions, or pagination make results partial.

## Tool Selection

- `search_scripts`: search normal scripts. Filters: `keyword`, `status_filter`, `team_id`, `class_id`, `target_audience_id`, `author_person_type`, `updated_from`, `updated_to`, `page`, `page_size`.
- `get_script`: open one normal script by ID. Use `include_sections=false` when only metadata is needed; include sections for summaries or content review.
- `get_script_comments`: read section comments for one normal script.
- `search_daily_scripts`: search daily scripts. Filters: `keyword`, `status_filter`, `team_id`, `updated_from`, `updated_to`, `page`, `page_size`.
- `get_daily_script`: open one daily script by ID. Use `include_items=false` when only metadata is needed; include items for summaries or content review.
- `get_daily_script_comments`: read item comments for one daily script.
- `list_classes`, `list_subjects`, `list_teams`, `list_target_audiences`: resolve catalog values before filtering.
- `get_script_timeline`, `get_daily_script_timeline`: read approval/review event history.

## Query Discipline

- Preserve user-provided Vietnamese names, titles, and keywords exactly when searching.
- Use uppercase status values: `DRAFT`, `SUBMITTED`, `REJECTED`, `RESUBMITTED`, or `APPROVED`.
- Use `author_person_type=teacher` or `author_person_type=actor` only for normal scripts.
- Use ISO 8601 date strings for updated-date filters.
- Keep page sizes modest; `page_size` must be between 1 and 100.
- Prefer narrower filters over broad searches when the user gives enough context.
- `search_scripts` has no `subject_id` filter. If the user names a subject, list subjects for context, then use keyword search or inspect candidate details.
- Do not fabricate IDs, statuses, comments, or review events. If no matching record is returned, say that directly and suggest a narrower or broader query.

## Response Rules

- Separate facts from interpretation. Mention which records or tools the answer is based on when it matters.
- For normal script summaries, use title/status/team/class plus section `section_title`, `dialogue`, and `performance_style` when present.
- For daily script summaries, use title/status/team plus item `voice`, `source`, `note`, `caption`, `type`, and metadata values when present.
- For review answers, distinguish comments from timeline events.
- If a tool returns 401, tell the user to refresh `MARKFLOW_ACCESS_TOKEN` for stdio or complete the MCP OAuth flow for HTTP transport. If it returns 403, report that the authenticated user lacks permission for that BU or resource.
- If the user asks to create, update, approve, reject, delete, or schedule MarkFlow records, explain that the bundled MCP tools are read-only and ask whether they want code/API work instead.
