---
title: "MCP Server"
tags: [mcp, server, tools, reference, api]
content_type: entity
domain: same-getting-started
---

# MCP Server

SAME's MCP server exposes 12 tools that any MCP-compatible AI client can use. The server runs as a subprocess managed by your AI tool.

## Starting the Server

Automatically (via `same init`):
```bash
same init    # configures MCP for Claude Code automatically
```

Manually (add to `.mcp.json`):
```json
{
  "mcpServers": {
    "same": {
      "command": "npx",
      "args": ["-y", "@sgx-labs/same", "mcp", "--vault", "/path/to/vault"]
    }
  }
}
```

## Tool Reference

### Search Tools
| Tool | Parameters | Returns |
|------|-----------|---------|
| `search_notes` | `query` (string) | Ranked list of matching notes with excerpts |
| `search_notes_filtered` | `query`, `domain?`, `tags?`, `agent?` | Filtered search results |
| `search_across_vaults` | `query` | Results from all configured vaults |
| `find_similar_notes` | `path` (note path) | Notes similar to the given note |

### Read Tools
| Tool | Parameters | Returns |
|------|-----------|---------|
| `get_note` | `path` (note path) | Full note content |
| `get_session_context` | none | Pinned notes + latest handoff + git state |
| `recent_activity` | `limit?` | Recently modified notes |
| `index_stats` | none | Index health and statistics |

### Write Tools
| Tool | Parameters | Returns |
|------|-----------|---------|
| `save_note` | `path`, `content`, `frontmatter?` | Confirmation |
| `save_decision` | `title`, `rationale`, `alternatives?` | Confirmation |
| `create_handoff` | `summary`, `completed`, `next_steps` | Confirmation |
| `reindex` | `force?` | Index rebuild status |

## Key Behaviors

- **Search first** -- The AI should always search before generating from scratch
- **Automatic context** -- `get_session_context` is called at session start
- **Citation** -- Search results include note paths for attribution
- **Fallback** -- If embeddings aren't available, falls back to keyword search

## Related Notes

- How MCP works: `research/how-mcp-works.md`
- Connect to Claude Code: `guides/connect-to-claude-code.md`
- Configuration: `entities/config-toml.md`
