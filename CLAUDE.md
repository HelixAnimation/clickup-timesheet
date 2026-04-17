# ClickUp Time Tracking App

A plain HTML/JS timesheet app that connects to the ClickUp API, similar to ClickUp's built-in timesheet view.

## Goal

Weekly grid view — tasks as rows, days as columns. Click a cell to add/edit hours for that task on that day. Supports multiple users via their own ClickUp API tokens (stored in localStorage).

## Features to Build

- [x] API token input — user pastes their token once, stored in localStorage
- [x] Week navigation (prev/next week arrows)
- [x] Fetch all tasks assigned to the user
- [x] Fetch existing time entries for the current week
- [x] Grid display: tasks × days with tracked time per cell
- [x] Click a cell → input hours → POST to ClickUp API
- [x] Show weekly total per task
- [x] User can clear/reset their token

## ClickUp API Reference

Base URL: `https://api.clickup.com/api/v2`

| Action | Endpoint |
|---|---|
| Get authorized user | `GET /user` |
| Get user's teams | `GET /team` |
| Get tasks | `GET /team/{team_id}/task?assignees[]={user_id}` |
| Get time entries | `GET /team/{team_id}/time_entries?start={ms}&end={ms}&assignee={user_id}` |
| Add time entry | `POST /team/{team_id}/time_entries` |
| Delete time entry | `DELETE /team/{team_id}/time_entries/{time_entry_id}` |

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
- All API calls include `Authorization: {token}` header
