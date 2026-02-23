# diacrit/claude

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

### /diacrit:discuss

Fetch your saved bookmarks and discuss them with Claude.

```
/diacrit:discuss
```

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
