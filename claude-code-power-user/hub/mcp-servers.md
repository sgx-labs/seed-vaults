---
title: "MCP Servers — Extending Claude Code"
tags: [mcp, servers, tools, integration, extensions]
content_type: hub
domain: engineering
---

# MCP Servers — Extending Claude Code

## What Is MCP?

Model Context Protocol (MCP) is a standard for giving AI agents access to external tools. MCP servers provide tools (functions) that Claude Code can call — like searching databases, accessing APIs, or managing infrastructure.

## How It Works

1. You register an MCP server in `.claude/settings.json`
2. Claude Code connects to the server at session start
3. The server exposes tools (with JSON schema descriptions)
4. Claude sees the tools and can call them during conversation
5. Results flow back into the conversation context

## Configuration

```json
{
  "mcpServers": {
    "same": {
      "command": "same",
      "args": ["mcp"],
      "type": "stdio"
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"],
      "type": "stdio"
    }
  }
}
```

## Popular MCP Servers

| Server | What it does | See |
|--------|-------------|-----|
| SAME | Persistent memory, knowledge base search | `research/mcp/same-mcp.md` |
| Filesystem | Read/write files outside project | `research/mcp/finding-servers.md` |
| GitHub | Issues, PRs, repo management | `research/mcp/finding-servers.md` |
| PostgreSQL | Database queries | `research/mcp/database-servers.md` |
| Brave Search | Web search | `research/mcp/search-servers.md` |
| Puppeteer | Browser automation | `research/mcp/search-servers.md` |

## MCP vs Hooks

| Feature | MCP | Hooks |
|---------|-----|-------|
| **Trigger** | Claude decides to call a tool | Lifecycle events (automatic) |
| **Direction** | Claude → external tool | External tool → Claude context |
| **Use case** | Search, APIs, databases | Automation, guard rails, context loading |
| **Transport** | stdio or SSE | Shell command |

Use hooks for automation that should happen every time. Use MCP for tools Claude calls when it needs them.

## Key Concepts

- **stdio transport** — Server runs as a subprocess, communicates via stdin/stdout
- **SSE transport** — Server runs as HTTP service, communicates via Server-Sent Events
- **Tool schemas** — Each tool has a JSON schema describing its parameters
- **Server scope** — Project-level (`.claude/settings.json`) or global (`~/.claude/settings.json`)

## Research Notes

- `research/mcp/same-mcp.md` — SAME as an MCP server
- `research/mcp/finding-servers.md` — Where to find MCP servers
- `research/mcp/building-servers.md` — Building your own MCP server
- `research/mcp/database-servers.md` — Database MCP servers
- `research/mcp/search-servers.md` — Search and web MCP servers
