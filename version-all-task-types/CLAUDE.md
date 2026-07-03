# ClickUp Time Tracking App

A plain HTML/JS timesheet app that connects to the ClickUp API, similar to ClickUp's built-in timesheet view.

## Goal

Weekly grid view — tasks as rows, days as columns. Click a cell to add/edit hours for that task on that day. Supports multiple users via their own ClickUp API tokens (stored in localStorage).

## Features Built

- [x] API token input — user pastes their token once, stored in localStorage
- [x] Week navigation (prev/next week arrows + Today button)
- [x] Fetch all tasks assigned to the user (including completed/closed)
- [x] Fetch existing time entries for the current week
- [x] Grid display: tasks × days with tracked time per cell
- [x] Click a cell → input hours → POST to ClickUp API
- [x] Tab / Shift+Tab keyboard navigation between cells
- [x] Show weekly total per task + daily totals + grand total
- [x] User can clear/reset their token
- [x] Task hierarchy — subtasks indented under parent tasks with L-connector
- [x] Tasks grouped by project (list) with collapsible group headers
- [x] Group header shows weekly total and ↗ link to open list in ClickUp
- [x] Click task name → opens task in ClickUp (new tab)
- [x] Status badges on tasks (in progress / complete / to do)
- [x] Filter to hide/show completed tasks (hidden by default)
- [x] Toolbar dropdown filters for live ClickUp task types
- [x] Saturday/Sunday columns visually dimmed
- [x] Today column highlighted
- [x] ClickUp MCP server connected (`claude mcp add --transport http clickup https://mcp.clickup.com/mcp`)
- [x] Deployed to GitHub Pages via HelixAnimation org
- [x] Holiday highlighting (MK public holidays via date.nager.at API)
- [x] All-time total column per task
- [x] Future dates blocked (dimmed, non-clickable) — ClickUp API doesn't return time entries for dates after today
- [x] Bulk fill skips future dates

## Live App

- **URL:** https://helixanimation.github.io/clickup-timesheet/
- **Repo:** https://github.com/HelixAnimation/clickup-timesheet

## ClickUp API Reference

Base URL: `https://api.clickup.com/api/v2`

| Action | Endpoint |
|---|---|
| Get authorized user | `GET /user` |
| Get user's teams | `GET /team` |
| Get tasks | `GET /team/{team_id}/task?assignees[]={user_id}&subtasks=true&include_closed=true` |
| Get workspace custom fields | `GET /team/{team_id}/field` |
| Get custom task types | `GET /team/{team_id}/custom_item` |
| Get time entries | `GET /team/{team_id}/time_entries?start={ms}&end={ms}&assignee={user_id}` |
| Add time entry | `POST /team/{team_id}/time_entries` |
| Delete time entry | `DELETE /team/{team_id}/time_entries/{time_entry_id}` |
| Get list views | `GET /list/{list_id}/view` |

### Time entry POST body
```json
{
  "tid": "task_id",
  "start": 1670000000000,
  "duration": 3600000
}
```
- `start` and `duration` are in **milliseconds**
- Hours input → multiply by `3600000` to get ms

### List URL construction
- Fetch list views via `GET /list/{list_id}/view`
- Find view with `type === "list"`, use its `id`
- URL: `https://app.clickup.com/{team_id}/v/l/{view_id}`
- Fallback: `https://app.clickup.com/{team_id}/v/li/{list_id}`

## File Structure

```
TimeTracking/
├── CLAUDE.md
├── index.html       # main app (single file, all HTML/CSS/JS)
```

## Notes

- Single file app (`index.html`) — no build tools, no framework
- Token stored in `localStorage` under key `clickup_token`
- Team ID and User ID fetched automatically after token entry
- All API calls include `Authorization: {token}` header (no "Bearer" prefix)
- API errors are thrown so they surface in the UI via alert()
- Collapsed list groups persist in `collapsedLists` Set (in-memory, resets on reload)
- Task fetch is filtered by live ClickUp task types. The toolbar dropdown is populated from `GET /team/{team_id}/custom_item` plus the regular `Task` type (`custom_item_id=0`), and selected IDs are sent as `custom_items[]`. The default is all task types; clicking one task type isolates that type, and clicking it again returns to all. If the custom task type API fails, the app can still try a ClickUp custom field named `Task Type` via `custom_fields` and then client-side filtering.
- Completed tasks filtered via `showCompleted` flag (default: false)
- **ClickUp API quirk:** The `GET /team/{id}/time_entries` endpoint does NOT return entries for future dates (after today), even if they exist. Creating future entries works via POST, but they won't appear in GET responses until that date arrives. The app blocks editing on future cells to avoid this mismatch.
- **ClickUp API quirk (date filtering is broken):** The `start`/`end` range filter on `GET /team/{id}/time_entries` is reliable only for WIDE ranges. Single-week/month queries come back truncated or date-shifted (e.g. a query for week A returns entries from ~4 weeks earlier; an unfiltered query drops entries). A 6-month query returned all entries correctly. The app therefore fetches the full history (since 2020) once via `fetchAllUserEntries()` (paginated + deduped by entry `id`) and slices to the displayed week client-side in `buildWeekEntryMap()`; `buildTotals()` sums the same dataset for the Total column. Do NOT revert to a single narrow-range call — old weeks will look empty again.
- Holidays fetched from `https://date.nager.at/api/v3/PublicHolidays/{year}/MK`
- To deploy updates: `git add index.html && git commit -m "..." && git push`
