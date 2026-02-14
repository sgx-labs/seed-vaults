---
title: "Search and Web MCP Servers"
tags: [mcp, search, web, brave, exa, puppeteer, fetch, browser]
content_type: research
domain: engineering
---

# Search and Web MCP Servers

## The Question

How do you give Claude Code access to the web for research, documentation lookups, and fact-checking? Search MCP servers let Claude query the web, fetch pages, and automate browsers — all within your coding session.

## Available Search and Web Servers

| Server | Package | What It Does | Requires |
|--------|---------|-------------|----------|
| Brave Search | `@modelcontextprotocol/server-brave-search` | Web + local search | API key (free tier available) |
| Fetch | `@modelcontextprotocol/server-fetch` | Fetch and parse web pages | Nothing |
| Puppeteer | `@modelcontextprotocol/server-puppeteer` | Full browser automation | Chrome/Chromium |
| Exa | `@exa/mcp-server` | Semantic web search | API key |

## Brave Search Setup

Free tier: 2,000 queries/month. Sign up at [brave.com/search/api](https://brave.com/search/api/).

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "type": "stdio",
      "env": { "BRAVE_API_KEY": "your-api-key-here" }
    }
  }
}
```

Tools: **brave_web_search** (web results with titles, URLs, snippets) and **brave_local_search** (local businesses and places).

## Fetch Server Setup

Retrieves and converts web pages to markdown. No API key needed.

```json
{
  "mcpServers": {
    "fetch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-fetch"],
      "type": "stdio"
    }
  }
}
```

Tool: **fetch** — retrieve any public URL and convert to markdown. Useful for reading docs, blog posts, and reference material.

## Puppeteer Server Setup

Full browser automation — screenshots, clicking, form filling, JavaScript execution.

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"],
      "type": "stdio"
    }
  }
}
```

Tools: `puppeteer_navigate`, `puppeteer_screenshot`, `puppeteer_click`, `puppeteer_fill`, `puppeteer_evaluate`. Use cases: screenshotting your app to verify UI, scraping dynamic content, testing multi-step flows.

## Exa Semantic Search

Exa provides semantic search — you describe what you want in natural language, and it finds relevant pages based on meaning rather than keywords.

```json
{
  "mcpServers": {
    "exa": {
      "command": "npx",
      "args": ["-y", "@exa/mcp-server"],
      "type": "stdio",
      "env": {
        "EXA_API_KEY": "your-api-key-here"
      }
    }
  }
}
```

### When Exa Is Better Than Brave

| Scenario | Better Tool |
|----------|------------|
| Known keyword search ("React useEffect cleanup") | Brave Search |
| Conceptual search ("libraries for building type-safe REST APIs in TypeScript") | Exa |
| Finding similar pages to a known URL | Exa |
| Local business search | Brave Search |

## Built-in WebSearch vs. MCP Search Servers

Claude Code already has a built-in `WebSearch` tool. When should you use MCP search servers instead?

### Use the Built-in WebSearch When

- You need a quick factual lookup
- You do not want to manage another MCP server
- The built-in tool covers your needs (general web search)

### Use an MCP Search Server When

- You need **semantic search** (Exa) — find pages by meaning, not keywords
- You need **page fetching** (Fetch) — read full page content, not just snippets
- You need **browser automation** (Puppeteer) — interact with dynamic pages
- You want **consistent API access** — same API key, same results, no per-session variation
- You need **local search** (Brave) — find businesses and places

### Comparison

| Feature | Built-in WebSearch | Brave MCP | Exa MCP | Fetch MCP |
|---------|-------------------|-----------|---------|-----------|
| Web search | Yes | Yes | Yes (semantic) | No |
| Full page content | No (snippets) | No (snippets) | Optional | Yes |
| Semantic search | No | No | Yes | No |
| API key required | No | Yes | Yes | No |
| Rate limits | Platform-managed | Your API quota | Your API quota | None |

## Security Notes

- **API keys**: Store in `~/.claude/settings.json` (user-level, never committed) or shell env vars
- **Puppeteer**: Gives Claude a real browser. Avoid using alongside authenticated sessions. Browser profile is ephemeral by default (no saved cookies).
- **Data exposure**: Search queries go to third-party APIs. Do not search for proprietary code or internal URLs. Fetch requests come from your IP.

## Practical Setup Recommendation

For most developers, start with these two:

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "type": "stdio",
      "env": {
        "BRAVE_API_KEY": "your-key"
      }
    },
    "fetch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-fetch"],
      "type": "stdio"
    }
  }
}
```

Brave Search for finding things, Fetch for reading them. Add Puppeteer or Exa later if you need browser automation or semantic search.
