---
description: List your bookmarks
argument-hint: [account name]
skill: NONE
---

# List bookmarks

Show the user's bookmark list at a glance.

## Steps

1. Parse arguments from: `$ARGUMENTS`
   - Config directory: `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows
   - If an account name is provided, read `accounts.json` and find the matching entry by `name`; error if not found. Extract `user_key` for filtering.

2. Read `shares.json` from the config directory. If it doesn't exist or is empty: "No bookmarks yet. Save a URL from your phone and come back!" and stop.
   - If an account name was provided, filter shares to only those whose `user_key` matches the account's `user_key`
   - If filtered list is empty: "No bookmarks for **<name>**." and continue to step 7 (still check server for that account)

3. Read `index.md` from the config directory if it exists. Extract the list of cache hashes that have `discussed` status (lines containing `-- discussed`).

4. Split shares into three groups:
   - **Downloaded from Server** — no `hash` field (content not yet fetched)
   - **Page Cached Locally** — has `hash` but not marked as `discussed` in `index.md` (or `index.md` doesn't exist)
   - **Discussed** — has `hash` and that hash appears as `discussed` in `index.md`

5. Present grouped, most recent first within each group. Use a **single continuous numbering** across all sections so the user can reference any bookmark by one number:

   ```
   **Downloaded from Server** (3)
   1. How to build a REST API — dev.to
   2. PostgreSQL indexing tips — pganalyze.com
   3. Dark mode design patterns — smashingmagazine.com

   **Page Cached Locally** (2)
   4. WebSocket vs SSE — `cache/i9j0k1l2`
   5. CSS container queries — `cache/m3n4o5p6`

   **Discussed** (2)
   6. HTMX: modern web apps — `cache/a1b2c3d4`
   7. SQLite as a production DB — `cache/e5f6g7h8`
   ```

   - For **Downloaded from Server**: show `meta` if available, otherwise extract domain from URL
   - For **Page Cached Locally/Discussed**: use the topic from `index.md` if available, otherwise extract domain from URL. Show cache path.
   - Skip empty groups silently

6. Show total: "N bookmarks (X downloaded, Y cached, Z discussed)"

7. **In parallel with steps 2-6**, check the server for pending shares:
   - Read `accounts.json` from the config directory. If no accounts, skip.
   - If an account name was provided, only check that account. Otherwise check all accounts.
   - For each account, call `GET https://diacrit.com/dia/shares` with header `Dia-User-Key: <user_key>`
   - Count total pending shares across checked accounts
   - After the local list is displayed, append at the bottom:
     - If pending > 0: "N new share(s) waiting on server. Download now? (y/n)"
     - If pending == 0: "Server: no new shares"
     - If request failed: "Server: could not check (offline?)"
   - If user says yes, follow the download flow from `/diacrit:discuss` (save to `shares.json`, dedup, delete from server)
   - To delete shares from server: `DELETE https://diacrit.com/dia/shares` with header `Dia-User-Key: <user_key>` and JSON body `{"ids":["<share-id>", ...]}`
