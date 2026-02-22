# Waiting — Menu Bar Companion

A native macOS menu bar app that shows unread bookmark counts across all your paired devices.

## Why

Your mom shares a link from her phone. A friend sends you an article from their Samsung. You save something from your own iPhone. Instead of checking each account manually or getting spammed on WhatsApp — Waiting sits quietly in your menu bar and shows you at a glance.

## How It Works

1. **Reads your paired accounts** from `~/.config/diacrit/accounts.json` (same file the Claude Code plugin uses)
2. **Polls the Diacrit API** (`GET /dia/shares`) for each account on every menu open
3. **Shows a count** next to each device name — how many bookmarks are queued
4. **Click an account** → opens a new Terminal window into Claude Code. Claude will open in the directory you designate on connect. See [Project Directories](#project-directories)
5. **Add account** → opens Terminal running `claude "/diacrit:connect"` for a new pairing

## What You See

The diacrit logo sits in your macOS menu bar. Click it to see:

```
👤 Accounts
 0  My iPhone
 2  Work Android
 3  Mom
 +  Add account
─────────────────
⏻  Quit Diacrit
```

- `*` shows briefly while counts are loading
- Counts refresh every time you open the popover
- Zero-count accounts still appear (so you know they're connected)

## Project Directories

Each account has a `project` field in `accounts.json` — the directory Claude Code opens in when you click that account.

```json
[
  { "name": "My iPhone", "project": "~/projects/my-app", ... },
  { "name": "Mom", "project": "~/projects/family", ... }
]
```

Clicking "My iPhone" runs: `cd ~/projects/my-app && claude "/diacrit:ls My iPhone"`
Clicking "Mom" runs: `cd ~/projects/family && claude "/diacrit:ls Mom"`

The project path is set during `/diacrit:connect`. This means bookmarks land in the right project context — different accounts can open in different projects.

## Install

Download the latest release from [GitHub Releases](https://github.com/diacrit/diacrit/releases). Unzip and move `Waiting.app` to your Applications folder. The app lives in your menu bar (no dock icon, no window).

## Requirements

- macOS (tested on Tahoe)
- Paired devices via `/diacrit:connect` (accounts in `~/.config/diacrit/accounts.json`)
- Claude Code with the diacrit plugin installed

