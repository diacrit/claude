---
description: Resume a cached discussion by ID
argument-hint: <cache-id>
skill: NONE
---

# Recall a discussion

Resume a previous bookmark discussion from cache. Used when a backlink like `run: diacrit:recall <cache-id>` appears in project docs.

## Steps

1. Parse `<cache-id>` from: `$ARGUMENTS`
   - If no cache-id provided, tell the user: "Usage: `/diacrit:recall <cache-id>` — the 8-char hex ID from a backlink." and stop
   - Config directory: `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows

2. Read the cache:
   - Look for `fetch-*.md` files in `<config-dir>/cache/<cache-id>/`
   - If no cache directory or no fetch files: "No cached content for `<cache-id>`. It may have been deleted." and stop
   - Read the most recent `fetch-*.md` (sort by filename, last wins)
   - Read `discussions.md` if it exists

3. Present:
   - If `discussions.md` exists: show previous discussion notes first, then a summary of the fetched page
   - If no discussions: just summarise the fetched page
   - Ask: "What would you like to explore?"

4. Continue the conversation naturally. Same note-saving rules as discuss:
   - Wait for natural moments to offer saving notes
   - **Before wrapping up**: if there was meaningful discussion, offer to save
   - **NB: The whole point is to capture insights. Don't let discussion slip away unsaved — when in doubt, offer.**
   - Append to `<config-dir>/cache/<cache-id>/discussions.md` (timestamped, don't replace)
   - After saving notes, update the bookmark's entry in `index.md` (if it exists in the config directory): change `fetched` to `discussed` and update the date to today. Add a one-sentence summary if missing. If no entry exists for this cache-id, add one — read the topic from the most recent `fetch-*.md` H1, write a one-sentence summary, and find the URL from `shares.json` by matching the hash.

5. Project context — if the user mentions a project name you don't recognize, **don't ask what it is** — look it up:
   a. First search `<config-dir>/projects.json` (curated list with name, path, desc)
   b. Fall back to `~/.claude/projects/` (directory names are paths with slashes replaced by dashes, use grep/fuzzy match)
   c. Fall back to `~/projects/` directory names
   d. If a match is found, read its `MEMORY.md` or `CLAUDE.md` to understand the project and connect the discussion to it
   e. When saving information to another project, include a backlink:
      ```
      run: `diacrit:recall <cache-id>`
      ```
