---
description: Open your bookmarks for discussion
argument-hint: [--account <name>]
skill: NONE
---

# Discuss your bookmarks

The user wants to talk about their saved URLs. This is the main Diacrit experience.

## Steps

1. Parse arguments from: `$ARGUMENTS`
   - API base is always `https://diacrit.com/dia`
   - If `--account <name>` is present, use that account from `accounts.json`

2. Load account:
   - Config directory: `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows
   - Read `accounts.json` from the config directory
   - If file doesn't exist or is empty: tell user to run `/diacrit:connect` first and stop
   - If `--account` specified, find matching entry by `name`; error if not found
   - If only one account, use it
   - If multiple accounts and no `--account`, prompt: "Which device?" and list them by name
   - Extract `user_key`

3. Fetch new shares from server using Bash:
   ```
   curl -s -X GET https://diacrit.com/dia/shares -H 'Dia-User-Key: <USER_KEY>'
   ```

4. If the server returned shares (non-empty array):
   a. Collect the `id` from each share in the response
   b. Read `shares.json` from the config directory if it exists (JSON array), otherwise start with `[]`
   c. For each share from API, add `user_key` field and append — skip if a share with same URL already exists (no `hash` yet — hash is added later when content is fetched)
   d. Write back to the config directory's `shares.json`
   e. Immediately delete from server using the ids:
      ```
      curl -s -X DELETE https://diacrit.com/dia/shares -H 'Dia-User-Key: <USER_KEY>' -H 'Content-Type: application/json' -d '{"ids":[1,2,3]}'
      ```

5. Now read `shares.json` from the config directory to get the full local collection.
   - Split into "new" (just fetched in step 4) and "existing" (already in file before step 4)

6. Present to the user:
   - If there are new shares: "You have **N** new bookmarks:" then list each with label and URL
   - If there are also existing shares: "You also have **M** saved from before. Want me to list them? I can search by topic too."
   - If no new but there are existing: "No new bookmarks, but you have **M** saved. Want to discuss any? I can search by topic."
   - If nothing at all: "No bookmarks yet. Save a URL from your phone and come back!"
   - Ask: "Would you like to discuss any of these?"

7. When the user picks one, get the page content:

   a. **Compute hash** — `<hash>` is the first 8 hex characters of the SHA-256 of the URL

   b. **Check cache** — look for `fetch-*.md` files in the config directory's `cache/<hash>/`
      - If fetches exist, find the most recent one (sort by filename, last wins)
      - Prompt: "I downloaded this page on **DD-MMM HH:MM**. (1) Download latest (2) That version is fine"
      - If user picks (2), use the cached content
      - If user picks (1), or no fetches exist, proceed to fetch

   c. **Fetch** — try in order:
      1. **WebFetch** — try this first
      2. **Playwright** — if WebFetch fails, check: `npx playwright --version 2>/dev/null`
         If installed: `node -e "const {chromium}=require('playwright');(async()=>{const b=await chromium.launch();const p=await b.newPage();await p.goto('URL');console.log(await p.content());await b.close()})()"`
      3. **Manual fallback** — if both fail, offer:
         - "I can open this in your browser — paste the content here and we'll discuss it"
         - "Want to install Playwright? Run: `npx playwright install chromium`"

   d. **Convert to markdown** — if content is HTML (from Playwright or paste), convert to markdown before saving.
      WebFetch already returns markdown. For HTML, use Claude's own ability to convert — just extract the main content and reformat as clean markdown.

   e. **Save to cache** — save as `fetch-YYYY-MM-DD-HH-MM.md` in the config directory's `cache/<hash>/`

   f. **Mark as discussed** — add the `hash` to this share's entry in `shares.json` and write back. Presence of `hash` means content has been fetched and cached.

8. Present a summary and start the conversation.

9. After the conversation winds down, ask: "Want me to save notes from this discussion?"
   - If yes, write a timestamped summary to `cache/<hash>/discussions.md` in the config directory (append, don't replace)
   - Format:
     ```
     ## 2026-02-17

     - Key takeaway 1
     - Key takeaway 2
     ```
   - If `discussions.md` already exists, mention: "There are notes from a previous discussion. Want to see them first?"

## Cache Structure

```
<config-dir>/cache/<hash>/
├── fetch-2026-02-19-17-28.md   # first fetch
├── fetch-2026-02-19-22-24.md   # re-fetch (user chose "download latest")
└── discussions.md               # conversation notes (appended, timestamped)
```

`<config-dir>` = `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows
`<hash>` = first 8 hex characters of SHA-256 of the URL
