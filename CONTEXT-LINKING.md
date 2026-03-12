# Context Linking — Hyperlinks for AI Memory

## What This Enables

Two complementary systems that together create persistent, navigable context across Claude sessions:

1. **Diacrit bookmarks** — share URLs from phone, discuss with Claude, cache insights locally
2. **Index + recall backlinks** — a hyperlink system within Claude's own markdown files that connects project docs back to cached bookmark discussions

## The Architecture

```
Phone → Cache → Index → Project docs → recall → Cache
```

- **Phone** captures the intent (bookmark a URL)
- **Cache** stores the knowledge (fetched pages + discussion notes)
- **Index** makes it discoverable from any Claude session
- **Recall** prevents duplication — backlinks instead of copies
- **CLAUDE.md** is the entry point — one line, every session sees it

## The Problem It Solves

Claude has no memory between sessions. You accumulate knowledge through discussions, but it's trapped in individual conversations. Diacrit gives you a structured way to build persistent, navigable context without polluting the context window.

## How It Works

1. Share a URL from your phone
2. `/diacrit:discuss` — Claude fetches, discusses, caches insights
3. Save notes to a project doc with a `diacrit:recall <cache-id>` backlink
4. Open a new Claude session, mention the topic
5. Claude finds it via the index, reads the cache — full context restored

No duplication. Project docs reference cached content through backlinks, never copy it. The tree pattern keeps the context window clean — Claude only reads the index when relevant.
