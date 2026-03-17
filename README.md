# diacrit/claude

[![Download on the App Store](https://diacrit.com/download-on-app-store.svg)](https://apps.apple.com/us/app/diacrit/id6759536023)

Bookmarks you can talk to. Connect Claude Code to the Diacrit mobile app.

## What is Diacrit?

Save URLs from your phone, discuss them with Claude. No email, no password — just a pairing code.

1. Save a link on your phone
2. Run `/diacrit:discuss` in Claude Code
3. Claude fetches the page and starts a conversation

## Install (quit Claude first)

```bash
claude plugin marketplace add diacrit/claude
claude plugin install diacrit@claude
```

## Commands

### /diacrit:connect

Pair Claude Code with your phone. Open Diacrit on your phone, tap Pair, then:

```
/diacrit:connect ABC123
```

### /diacrit:ls

List your bookmarks at a glance. No server calls — just shows what you have locally.

```
/diacrit:ls
```

### /diacrit:discuss

Fetch your saved bookmarks and discuss them with Claude.

```
/diacrit:discuss
```

## Menu bar companion

Don't want to check manually? **Waiting** is a native macOS menu bar app that shows unread bookmark counts across all your paired devices. Click an account to jump straight into Claude Code. See [WAITING.md](./WAITING.md).

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- Diacrit app on your phone — [diacrit.com](https://diacrit.com)

## Update (quit Claude first)

```bash
claude plugin marketplace update claude
claude plugin update diacrit@claude
```

## Uninstall (quit Claude first)

```bash
claude plugin uninstall diacrit@claude
claude plugin marketplace remove claude
```

## License

MIT — see [LICENSE.md](./LICENSE.md)
