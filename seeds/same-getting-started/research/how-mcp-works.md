---
title: "How MCP Works"
tags: [mcp, model-context-protocol, tools, integration, claude-code, cursor]
content_type: research
domain: same-getting-started
---

# How MCP Works

## What is MCP?

MCP (Model Context Protocol) is an open standard that lets AI agents use external tools. Think of it like a USB port for AI -- any tool that speaks MCP can plug into any AI client that supports it.

Before MCP, every AI tool had its own custom integration. Claude Code had its own plugins. Cursor had its own. Nothing was compatible. MCP changed that by creating a shared protocol.

## How SAME Uses MCP

SAME runs an MCP server that exposes 12 tools to your AI agent. When your AI needs to search notes, log a decision, or create a handoff, it calls these tools through MCP.

Here's what happens when your AI searches your vault:

```
You: "What did we decide about caching?"
  |
  v
AI Agent (Claude Code, Cursor, etc.)
  |
  v
MCP Protocol
  |
  v
SAME MCP Server
  |
  v
SQLite (embeddings + full-text search)
  |
  v
Results returned to AI with citations
```

The AI doesn't need to know how SAME works internally. It just calls `search_notes` and gets results.

## The 12 MCP Tools

| Tool | What it does |
|------|-------------|
| `search_notes` | Semantic search across your knowledge base |
| `search_notes_filtered` | Search with domain/tag/agent filters |
| `search_across_vaults` | Search across multiple vaults at once |
| `get_note` | Read the full content of a specific note |
| `find_similar_notes` | Find notes related to a given note |
| `get_session_context` | Load pinned notes, latest handoff, git state |
| `recent_activity` | See recently modified notes |
| `save_note` | Create or update a note |
| `save_decision` | Log a structured project decision |
| `create_handoff` | Write a session handoff |
| `reindex` | Re-scan and re-index the vault |
| `index_stats` | Check index health and statistics |

## Enabling MCP in Claude Code

The easiest way:

```bash
same init
```

This automatically adds SAME to Claude Code's MCP configuration. If you need to do it manually, add to your `.mcp.json`:

```json
{
  "mcpServers": {
    "same": {
      "command": "npx",
      "args": ["-y", "@sgx-labs/same", "mcp", "--vault", "/path/to/your/notes"]
    }
  }
}
```

## Enabling MCP in Cursor / Windsurf

Add the same configuration to your editor's MCP settings. The server command is identical -- only the config file location changes.

For Cursor, add to your Cursor settings under MCP servers.

## How Tools Get Called

Your AI doesn't call MCP tools with special syntax. It just... does it. When Claude Code sees that `search_notes` is available, it will use it automatically when relevant.

For example, if you ask "what's our auth strategy?", Claude Code will:
1. See that `search_notes` is available
2. Call `search_notes` with query "auth strategy"
3. Read the results
4. Give you an answer citing the specific notes

You don't need to tell it to search. SAME's CLAUDE.md instructions (loaded via hooks) tell the AI to always search before generating.

## Without MCP: CLI Mode

If your AI tool doesn't support MCP, SAME still works through its CLI:

```bash
same search "auth strategy"
same ask "what did we decide about caching?"
```

Your AI can call these commands via shell access. Less seamless than MCP, but fully functional.

## Next Steps

- Set up MCP with Claude Code: `guides/connect-to-claude-code.md`
- See the MCP server reference: `entities/mcp-server.md`
- Understand session context loading: `hub/session-memory.md`
