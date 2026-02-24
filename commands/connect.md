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

6. Confirm to the user:
   - "Connected to <DEVICE_NAME>! Your phone will see this computer as '<COMPUTER_NAME>'."
   - "Note: When you discuss a URL, Diacrit saves a cached copy of the page content locally. Please respect the rights of copyright holders and protected work."
