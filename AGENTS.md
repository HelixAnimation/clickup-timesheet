# Repository Guidelines

## Project Structure & Module Organization

This repository is a framework-free ClickUp timesheet. The production application lives entirely in `index.html`: markup, styles, and JavaScript are embedded in one file. `CLAUDE.md` documents behavior, API quirks, endpoints, and deployment notes; update it when changing important assumptions. `version-all-task-types/` is an alternate implementation, while `backup-20260703-164542/` is a historical snapshot. Treat both as reference copies unless a change explicitly targets them.

## Build, Test, and Development Commands

There is no package manager, compilation step, or automated test command. Serve the repository through a local HTTP server instead of opening the file directly:

```powershell
python -m http.server 8000
```

Then open `http://localhost:8000`. Use `git diff --check` to catch whitespace errors and `git diff -- index.html CLAUDE.md AGENTS.md` to review intended changes. Deployment is currently a push to the GitHub Pages branch configured for the repository.

## Coding Style & Naming Conventions

Preserve the existing two-space indentation in HTML, CSS, and JavaScript. Use `camelCase` for variables and functions, `UPPER_SNAKE_CASE` for constants, and kebab-case for HTML IDs and CSS classes (for example, `grid-container`). Prefer small functions, `const` by default, `let` only for reassignment, async/await for API work, and early returns for validation. Keep the app dependency-free unless a new dependency has a clear operational benefit.

## Testing Guidelines

Testing is manual. Before submitting changes, verify token connect/logout, current and historical week navigation, task-type and completed-task filters, search, cell editing/deletion, bulk fill, totals, theme persistence, and disabled future dates. Exercise both light and dark themes and inspect the browser console for API or rendering errors. Historical-week checks are essential: ClickUp's narrow time-entry date filters are unreliable, so do not replace the wide history fetch and client-side slicing without regression evidence.

## Commit & Pull Request Guidelines

Recent commits use short, imperative, sentence-case subjects such as `Filter ClickUp tasks by task type` and `Fix old weeks empty...`. Keep each commit focused and explain non-obvious API workarounds in the body. Pull requests should summarize user-visible behavior, list manual checks performed, link the relevant issue, and include screenshots for layout, theme, or grid changes. Never commit ClickUp API tokens or other credentials; tokens belong only in browser `localStorage`.
