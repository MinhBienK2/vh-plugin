---
name: mark-flow
description: Uses the bundled `markflow` MCP server to search, inspect, summarize, compare, and review MarkFlow Hub scripts, daily scripts, catalog metadata, comments, and review timelines. Use when a user asks about MarkFlow script content, daily script content, approval/review context, comments, classes, subjects, teams, target audiences, or MCP-backed MarkFlow data access.
---

# MarkFlow

Use the configured `markflow` MCP server first for MarkFlow Hub business questions. Choose the smallest tool sequence that can answer the user's intent, then synthesize only from returned records.

## Intent Routing

1. Classify the user's intent before calling tools:
   - Catalog: class, subject, team, or target audience names.
   - Normal script discovery: section-based scripts by keyword, status, team, class, target audience, author person type, or updated date.
   - Daily script discovery: item-based scripts by keyword, status, team, or updated date.
   - Detail/summary: one known script or daily script ID.
   - Review context: comments, approval history, rejection reasons, blockers, or audit trail.
2. Resolve catalog names before using ID filters.
3. Search first, then fetch details for likely IDs. Skip detail calls when list results already answer the question.
4. Fetch content arrays only for summaries, comparisons, or content review.
5. Fetch comments or timelines only for review context.

## Tool Selection

- `search_scripts`: search normal scripts. Filters: `keyword`, `status_filter`, `team_id`, `class_id`, `target_audience_id`, `author_person_type`, `updated_from`, `updated_to`, `page`, `page_size`.
- `get_script`: open one normal script by ID. Use `include_sections=false` when only metadata is needed; include sections for summaries or content review.
- `get_script_comments`: read section comments for one normal script.
- `search_daily_scripts`: search daily scripts. Filters: `keyword`, `status_filter`, `team_id`, `updated_from`, `updated_to`, `page`, `page_size`.
- `get_daily_script`: open one daily script by ID. Use `include_items=false` when only metadata is needed; include items for summaries or content review.
- `get_daily_script_comments`: read item comments for one daily script.
- `list_classes`, `list_subjects`, `list_teams`, `list_target_audiences`: resolve catalog values before filtering.
- `get_script_timeline`, `get_daily_script_timeline`: read approval/review event history.

## Business Rules

- Preserve user-provided Vietnamese names, titles, and keywords exactly when searching.
- Use uppercase status values: `DRAFT`, `SUBMITTED`, `REJECTED`, `RESUBMITTED`, or `APPROVED`.
- Use `author_person_type=teacher` or `author_person_type=actor` only for normal scripts.
- Use ISO 8601 date strings for updated-date filters.
- Keep page sizes modest; `page_size` must be between 1 and 100.
- Prefer narrower filters over broad searches when the user gives enough context.
- `search_scripts` has no `subject_id` filter. If the user names a subject, list subjects for context, then use keyword search or inspect candidate details.
- Do not fabricate IDs, statuses, comments, or review events. If no matching record is returned, say that directly and suggest a narrower or broader query.

## Response Rules

- Separate facts from interpretation. Mention source record IDs/titles when comparing or summarizing multiple records.
- For normal script summaries, use title/status/team/class plus section `section_title`, `dialogue`, and `performance_style` when present.
- For daily script summaries, use title/status/team plus item `voice`, `source`, `note`, `caption`, `type`, and metadata values when present.
- For review answers, distinguish comments from timeline events.
- If the user asks to create, update, approve, reject, delete, or schedule MarkFlow records, explain that the bundled MCP tools are read-only and ask whether they want code/API work instead.
