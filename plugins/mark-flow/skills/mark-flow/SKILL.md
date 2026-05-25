---
name: mark-flow
description: Uses the bundled `markflow` MCP server to search, inspect, summarize, compare, and review MarkFlow Hub scripts, daily scripts, catalog metadata, comments, and review timelines. Use when a user asks about MarkFlow script content, daily script content, approval/review context, comments, classes, subjects, teams, target audiences, or MCP-backed MarkFlow data access.
---

# MarkFlow

Use the configured `markflow` MCP server first for MarkFlow Hub business questions. Work from the user's business intent to the smallest useful tool sequence, then answer only from returned records.

## Business Objects

- Normal scripts are section-based lesson/production scripts. Key fields: `title`, `status`, `class_snapshots`, `subject_snapshots`, `team`, `target_audience`, `sections[].section_title`, `sections[].dialogue`, `sections[].performance_style`, duet teacher/actor names.
- Daily scripts are item-based daily content. Key fields: `title`, `status`, `team_name`, `type_key`, `metadata_values`, `note`, `caption`, `items[].item_title`, `items[].voice`, `items[].source`.
- Comments are unit-level feedback: normal scripts group by `section_order`; daily scripts group by `item_order`. Use them for unresolved feedback, reviewer notes, or content blockers.
- Timelines are workflow events: `CREATED`, `EDITED`, `SUBMITTED`, `WITHDRAWN`, `REJECTED`, `RESUBMITTED`, `APPROVED`, `DELETED`. Use them for audit history, approval flow, rejection reasons, and actor/date context.

## Tool Playbooks

- Catalog name resolution: use `list_classes`, `list_subjects`, `list_teams`, or `list_target_audiences` before numeric filters. Match names carefully, preserve Vietnamese text, and mention ambiguity when multiple catalog items look plausible.
- Normal script search: use `search_scripts` for title or section text discovery. Filters: `keyword`, `status_filter`, `team_id`, `class_id`, `target_audience_id`, `author_person_type`, `updated_from`, `updated_to`, `page`, `page_size`.
- Normal script detail: use `get_script` after a likely ID. Set `include_sections=false` for metadata/status/catalog checks; set `include_sections=true` for summaries, section review, dialogue, or performance style.
- Normal script review context: use `get_script_comments` for section feedback and `get_script_timeline` for workflow events. Use both when the user asks why a script is blocked, rejected, pending, or approved.
- Daily script search: use `search_daily_scripts` for daily content, voice/source, note, caption, type, metadata, status, team, or updated-date questions. Filters: `keyword`, `status_filter`, `team_id`, `updated_from`, `updated_to`, `page`, `page_size`.
- Daily script detail: use `get_daily_script` after a likely ID. Set `include_items=false` for metadata/status checks; set `include_items=true` for voice/source summaries, item review, or content comparison.
- Daily script review context: use `get_daily_script_comments` for item feedback and `get_daily_script_timeline` for workflow events.

## Common Business Workflows

1. "Find scripts for class/team/audience/status": resolve named catalogs, then call `search_scripts` with IDs and `status_filter`. Return title, ID, status, class, subject, team, audience, author, updated time.
2. "Find daily scripts for team/type/topic": resolve team if named, then call `search_daily_scripts`. Put type and metadata in the answer because daily script type often lives in `type_key` or `metadata_values`, not only title.
3. "Summarize this script": call `get_script` with sections. Summarize by section order, dialogue intent, performance style, and any obvious missing/weak content. Do not infer unseen sections.
4. "Summarize this daily script": call `get_daily_script` with items. Summarize by item order, voice/source, note, caption, type, and metadata values.
5. "What feedback is unresolved?": call the relevant comments tool. Group by section/item order; include author, created/updated time, `is_resolved`, and content.
6. "Why was this rejected or approved?": call the relevant timeline tool; add comments only if the user asks for feedback details or blockers. Timeline notes are workflow reasons; comments are discussion feedback.
7. "Compare two records": fetch both details with content arrays only when comparison needs content. Compare status/catalog metadata first, then section/item content.

## Query Rules

- Preserve user-provided Vietnamese keywords exactly. Keyword search is case-insensitive in MarkFlow, so do not translate or normalize user terms unless the user asks.
- Status filters must be uppercase: `DRAFT`, `SUBMITTED`, `REJECTED`, `RESUBMITTED`, `APPROVED`.
- `author_person_type` accepts only `teacher` or `actor`, and only for `search_scripts`.
- Dates must be ISO 8601 strings. Use date-only values for whole days when the user gives a day.
- `page_size` must be 1-100. Start with 10-20 unless the user asks for a broad scan; if `total` exceeds returned `items`, state that results are paged.
- `search_scripts` has no `subject_id` filter. If the user names a subject, use `list_subjects` for context, then search by keyword and inspect candidate details for `subject_snapshots`.

## Answer Rules

- Lead with the direct business answer, then list evidence records. Include IDs when the user may need follow-up detail.
- Separate record facts from your interpretation. Use phrases like "Based on returned comments..." for conclusions.
- Do not invent catalog names, statuses, reasons, comments, or missing records. If no data returns, say so and suggest the next narrower or broader query.
- The bundled MCP tools are read-only. For create/update/approve/reject/delete/schedule requests, say the MCP cannot perform that action and offer to help with API/code work if relevant.
