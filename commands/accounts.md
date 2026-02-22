---
description: List paired devices
argument-hint:
skill: NONE
---

# List paired devices

Show the user's paired devices. Local only — no server calls.

## Steps

1. Config directory: `~/.config/diacrit/` on Mac/Linux, `%APPDATA%/diacrit/` on Windows

2. Read `accounts.json` from the config directory. If it doesn't exist or is empty: "No paired devices yet. Run `/diacrit:connect` to pair your phone." and stop.

3. Present as a numbered table, most recently paired first:

   ```
   **Paired Devices** (3)

   | # | Name | Paired |
   |---|------|--------|
   | 1 | iPhone | 2026-03-07 |
   | 2 | Samsung S23 | 2026-02-28 |
   | 3 | sdk_gphone64_arm64 | 2026-02-24 |
   ```

   - Format `paired_at` as date only (YYYY-MM-DD)
   - Sort by `paired_at` descending (newest first)

4. Show total: "N device(s) paired"
