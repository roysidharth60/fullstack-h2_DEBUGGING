# NOTES

## Summary of changes
Seven fixes, one commit each. Details are in `handwritten/`.

| # | Where | Found by | Root cause | Fix |
|---|---|---|---|---|
| 1 | TaskRepository query | UI: "api" + In Progress showed OPEN rows | SQL AND/OR precedence | Parenthesized the OR |
| 2 | TaskController | Slow first load, then read code | `Thread.sleep` by query length | Commented out the sleep |
| 3 | TaskController | curl with bad input | Unchecked `valueOf`/`subList` | Return 400 |
| 4 | App.jsx | UI: last page, then Done, empty table | Page not reset on filter change | `setPage(1)` |
| 5 | useTasks.js | UI: stopped backend, error stuck | Error never cleared | Reset error; loading in `finally` |
| 6 | Controller + repository | Searching `%` matched everything | Unescaped LIKE wildcards | Escape + `ESCAPE` |
| 7 | db/ SQL files | Same pattern as #1 | Same precedence bug | Same parentheses |

## Not changed
- Huge `page` overflows `int` and returns 500 (UI cannot send it).
- Possible stale-response race; not reproduced.
- Oracle package reviewed, not run.

## Suggestions
- **Performance:** paginate in the database, not in memory; debounce search and cancel old requests; cap `pageSize`; add a sort tie-breaker.
- **UX:** empty-state message, retry button on errors, keep old rows while loading, "Showing 11-20 of 47", keep filters in the URL.

## Biggest remaining risk
In-memory pagination: every matching row is loaded, then sliced.

## Tools / AI
I used Claude as a debugging mentor. It pointed me to suspicious code, gave reproduction tests, and suggested fixes. I ran every test, applied the edits, and checked each result.
