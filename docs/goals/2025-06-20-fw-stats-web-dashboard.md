# Goal Brief: fw-stats Web Dashboard

## Shared Design Concept
Add a `fw-stats serve` command that starts a local HTTP server serving a browser dashboard showing FastWork marketplace stats with a table and charts.

## Resolved Decisions
| Decision | Choice |
|----------|--------|
| What "website" means | Browser-based web UI/dashboard |
| Architecture | Built-in HTTP server within the `fw-stats` CLI |
| Dashboard scope | Standard — table + charts |
| Chart library | Chart.js loaded from CDN (no npm dep) |
| Charts | Top 15 subcategories by reviews (bar) + Category overview (bar) |
| Server port | 3456, auto-open browser |
| Data freshness | Fetch once at startup + Refresh button |
| Project structure | Add `package.json` with `bin` entry |
| Server module | Node.js built-in `http` module |

## Non-Goals
- No separate web app project or directory
- No database, auth, or user accounts
- No build step, bundler, or compile
- No scraping the FastWork website
- No changes to existing CLI flags/behavior
- No periodic auto-refresh (manual refresh only)

## Constraints
- Server code must work alongside existing CLI code
- Use only Node.js built-ins for the server (`http`, `url`)
- Chart.js loaded from CDN via `<script>` tag
- Data fetched from the same GraphQL API
- Must work with `node fw-stats serve`
- Existing commands (`--json`, `--csv`, `--tsv`, `--sort`, `--top`, `--all`) must continue working

## Acceptance Criteria
1. `fw-stats serve` starts HTTP server on port 3456
2. `fw-stats serve` auto-opens browser to `http://localhost:3456`
3. Dashboard loads with an HTML table of subcategory data (same columns as CLI)
4. Dashboard includes a bar chart: Top 15 subcategories by review count
5. Dashboard includes a bar chart: Category overview (subcategory count per category)
6. "Refresh" button re-fetches data from the FastWork GraphQL API and updates the page
7. Existing CLI commands function unchanged
8. `package.json` exists with `"bin"` entry

## Verification Plan
1. `node fw-stats serve` → port 3456 opens, browser launches
2. `curl http://localhost:3456` → returns HTML dashboard page
3. Table renders with Category, Subcategory, Min Price, Avg Rating, Reviews, Ratio columns
4. Both Chart.js charts visible and rendered
5. Click Refresh button → data re-fetches, page updates
6. `node fw-stats --json` → CLI output unchanged
7. `node fw-stats --help` → shows new `serve` subcommand in help
8. `node -e "require('./package.json').bin"` → confirms bin entry

## Goal-Ready Prompt
```
Add a `fw-stats serve` command to the existing `fw-stats` Node.js CLI tool that starts
a local web server with a dashboard. The tool currently fetches FastWork marketplace
data from https://gateway.fastwork.co/graphql and displays it as CLI table/JSON/CSV/TSV.

Requirements:
1. Add `package.json` with `"bin": { "fw-stats": "./fw-stats" }` at minimum
2. Add a `serve` subcommand (parse args looking for "serve" before existing flags)
3. Server starts on port 3456, auto-opens browser using `open` or `xdg-open`
4. Serve a single HTML page dashboard that:
   - Fetches the data from a `/api/data` endpoint on the same server
   - Renders an HTML table: Category, Subcategory, Min Price, Avg Rating, Reviews, Ratio
   - Renders Chart.js bar chart: Top 15 subcategories by reviews_count
   - Renders Chart.js bar chart: Category overview (count of subcategories per category)
   - Has a "Refresh" button that calls /api/data again and updates the table + charts
5. The `/api/data` endpoint calls the same GraphQL API, caches result in-memory
6. Use CDN Chart.js (`https://cdn.jsdelivr.net/npm/chart.js`) — no npm install needed
7. Keep existing CLI behavior (`--json`, `--csv`, `--tsv`, `--sort`, `--top`, `--all`, `--help`) unchanged
8. Use only Node.js built-in `http` module — no Express or other server deps
9. When no subcommand is given, fall through to existing `main()` (CLI mode)

The HTML/CSS/JS should be inline in the response of the HTTP server (no separate files).
Style it minimally but cleanly — a plain dark-on-light dashboard.
```
