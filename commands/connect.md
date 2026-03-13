---
description: Connect Claude Code to a mobile device
argument-hint: <CODE>
skill: NONE
---

# Connect to Diacrit mobile app

The user is connecting their Claude Code with a Diacrit mobile app. They have a 6-character code displayed on their phone.

## Steps

1. Parse arguments from: `$ARGUMENTS`
   - If a 6-character alphanumeric CODE is present, use it
   - If no code is provided, ask the user for it. Mention: "If you don't have Diacrit on your phone yet, visit https://diacrit.com"
   - API base is always `https://diacrit.com/dia`

2. Determine the computer name:
   - Config directory: `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows
   - Read `config.json` from the config directory if it exists — it has `{"computer_name": "..."}`
   - If `computer_name` exists, use it silently (do NOT ask the user)
   - If no `config.json` or no `computer_name`, ask the user: "What would you like to name this computer?" (e.g. "Work MacBook", "Home Desktop")
   - Save the name to `config.json` in the config directory (create dir if needed)

3. Call the pairing API using Bash:
   ```
   curl -s -X DELETE https://diacrit.com/dia/pairings -H 'Content-Type: application/json' -d '{"code":"<CODE>","computer_name":"<NAME>"}'
   ```

4. Check the response:
   - Success: `{"user_key":"...", "device_name":"iPhone"}` — continue to step 5
   - Error `{"error":"Invalid or expired code"}` — tell user the code is invalid or expired, suggest tapping Get Code again on their phone
   - Network error — tell user to check their connection

5. Store the account:
   - Config directory: `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows. Create if it doesn't exist.
   - Read `accounts.json` from the config directory if it exists (it's a JSON array), otherwise start with `[]`
   - If an entry with the same `name` (device_name) already exists, replace it (update `user_key`, `paired_at`)
   - Otherwise append: `{"user_key": "<KEY>", "paired_at": "<ISO8601>", "name": "<DEVICE_NAME>"}`
   - Write back the updated array to the config directory's `accounts.json`

6. Set the default project directory:
   - If CWD is NOT `$HOME` (`~`): save `"project": "<CWD>"` on the account entry and tell the user: "Saved `<CWD>` as the default project for <DEVICE_NAME>."
   - If CWD IS `$HOME`:
     - Check other accounts in `accounts.json` for existing `project` values
     - If any exist, show them as a numbered list, plus "or type a path"
     - If none exist, ask: "What directory should be the default for this account?"
     - If the user presses Enter without typing a path, default to `$HOME`
   - Always save `"project": "<path>"` on the account entry — every account must have a project path

7. Confirm to the user:
   - "Connected to <DEVICE_NAME>! Your phone will see this computer as '<COMPUTER_NAME>'."
   - "Note: When you discuss a URL, Diacrit saves a cached copy of the page content locally. Please respect the rights of copyright holders and protected work."

8. Bookmark index setup:
   - Check if `~/.claude/CLAUDE.md` exists and already contains the text `~/.config/diacrit/index.md`
   - If the reference already exists, skip silently — nothing to do
   - If NOT present, ask the user: "Would you like your bookmarks to be discoverable from any Claude session? I'll add a one-line reference to your global CLAUDE.md."
   - If the user declines, skip — respect the choice
   - If yes:
     a. Create `~/.config/diacrit/index.md` if it doesn't exist, with this content:
        ```
        # Diacrit Bookmarks

        Your saved bookmarks. Each cache dir has fetched pages and discussion notes.
        Run `diacrit:recall <id>` to resume any discussion.
        ```
     b. If `shares.json` exists and contains entries with a `hash` (previously discussed bookmarks), backfill the index — for each hashed share, read the first H1 from the most recent `fetch-*.md` in `cache/<hash>/` as the topic, check if `discussions.md` exists to determine status (`discussed` vs `fetched`), then add a line to `index.md`:
        ```
        - **<Topic>** -- <one-sentence summary> -- <URL> -- `cache/<hash>/` -- <status> <YYYY-MM-DD>
        ```
     c. Append this line to the `## Reference Trees` section of `~/.claude/CLAUDE.md` (after the last `- ` bullet in that section):
        ```
        - [`~/.config/diacrit/index.md`](~/.config/diacrit/index.md) — Your saved bookmarks. Consult when the user mentions `diacrit`, bookmarks, a saved link, or a topic that could be a cached page. To link back to a bookmark from project docs, use `run: diacrit:recall <cache-id>` — don't duplicate the cached content, just reference it.
        ```
     d. Confirm: "Done — your bookmarks are now visible from any Claude session."
