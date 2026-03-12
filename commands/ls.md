---
description: List your bookmarks
argument-hint:
skill: NONE
---

# List bookmarks

Show the user's bookmark queue at a glance. Local only — no server calls, no account selection.

## Steps

1. Config directory: `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows

2. Read `shares.json` from the config directory. If it doesn't exist or is empty: "No bookmarks yet. Save a URL from your phone and come back!" and stop.

3. Read `index.md` from the config directory if it exists. Extract the list of cache hashes that have `discussed` status (lines containing `-- discussed`).

4. Split shares into three groups:
   - **Queued** — no `hash` field (content not yet fetched)
   - **Discussed** — has `hash` and that hash appears as `discussed` in `index.md`
   - **Fetched** — has `hash` but not marked as `discussed` in `index.md` (or `index.md` doesn't exist)

5. Present grouped, most recent first within each group. Use a **single continuous numbering** across all sections so the user can reference any bookmark by one number:

   ```
   **Queued** (3)
   1. How to build a REST API — dev.to
   2. PostgreSQL indexing tips — pganalyze.com
   3. Dark mode design patterns — smashingmagazine.com

   **Discussed** (2)
   4. HTMX: modern web apps — `cache/a1b2c3d4`
   5. SQLite as a production DB — `cache/e5f6g7h8`

   **Fetched** (2)
   6. WebSocket vs SSE — `cache/i9j0k1l2`
   7. CSS container queries — `cache/m3n4o5p6`
   ```

   - For **Queued**: show `meta` if available, otherwise extract domain from URL
   - For **Discussed/Fetched**: use the topic from `index.md` if available, otherwise extract domain from URL. Show cache path.
   - Skip empty groups silently

6. Show total: "N bookmarks (X queued, Y fetched, Z discussed)"
