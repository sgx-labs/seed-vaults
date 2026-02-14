---
title: "SAME as an MCP Server — Persistent Memory for Claude Code"
tags: [mcp, same, memory, knowledge-base, search, session-handoff, sqlite-vec, embeddings]
content_type: research
domain: engineering
---

# SAME as an MCP Server — Persistent Memory for Claude Code

## The Question

How do you give Claude Code persistent memory across sessions? SAME runs as an MCP server that provides 12 tools for searching notes, logging decisions, and handing off between sessions — all stored locally in SQLite with vector embeddings.

## Setup

### Quick Setup

```bash
same setup mcp
```

This adds SAME to your Claude Code MCP configuration automatically.

### Manual Setup

Add to `.claude/settings.json` (project-level) or `~/.claude/settings.json` (global):

```json
{
  "mcpServers": {
    "same": {
      "command": "same",
      "args": ["mcp"],
      "type": "stdio"
    }
  }
}
```

### Configuration

SAME finds your vault (notes directory) through this priority order:

1. `VAULT_PATH` environment variable
2. `vault_path` in `~/.config/same/config.toml`
3. Current working directory fallback

To override per-project:

```json
{
  "mcpServers": {
    "same": {
      "command": "same",
      "args": ["mcp"],
      "type": "stdio",
      "env": {
        "VAULT_PATH": "/path/to/your/notes"
      }
    }
  }
}
```

## How It Works

1. Claude Code starts SAME as a stdio subprocess at session start
2. SAME connects to a SQLite database with vector embeddings (sqlite-vec)
3. Claude sees 12 tools and calls them as needed during conversation
4. Results flow back into the conversation context as structured data

Embeddings are generated locally via Ollama (default: nomic-embed-text). No data leaves your machine.

## The 12 Tools

### Search Tools

| Tool | What It Does | When to Use |
|------|-------------|-------------|
| `search_notes` | Semantic + keyword search across your vault | Finding relevant context on any topic |
| `search_notes_filtered` | Search with domain/workstream/tag filters | Narrowing results in large vaults |
| `find_similar_notes` | Find notes related to a given note | Discovering connections, finding duplicates |
| `search_across_vaults` | Federated search across multiple vaults | Cross-project context |

### Read Tools

| Tool | What It Does | When to Use |
|------|-------------|-------------|
| `get_note` | Read full content of a specific note | After search returns a relevant result |
| `recent_activity` | List recently modified notes | Session start orientation |
| `get_session_context` | Pinned notes + latest handoff + recent decisions | Starting a new session |
| `index_stats` | Check index health and size | Debugging, verifying indexing |

### Write Tools

| Tool | What It Does | When to Use |
|------|-------------|-------------|
| `save_note` | Create or update a markdown note | Capturing research, documenting patterns |
| `save_decision` | Log a project decision with rationale | Recording architectural choices |
| `create_handoff` | Write a session handoff note | End of session wrap-up |
| `reindex` | Re-scan and re-embed vault notes | After adding files outside of SAME |

## Common Use Cases

### Session Continuity

At the start of every session, Claude calls `get_session_context` to retrieve:
- Pinned notes (always-relevant context)
- The latest handoff note (what happened last session)
- Recent decisions (recent architectural choices)

At the end, `create_handoff` captures what was accomplished and what is pending.

### Knowledge Base Search

During coding, Claude searches your vault for relevant context:

```
Claude calls: search_notes("authentication approach")
Returns: Ranked list of notes about auth decisions, patterns, prior research
Claude calls: get_note("decisions/use-jwt-for-auth.md")
Returns: Full decision document with rationale
```

### Decision Logging

When you make an architectural choice:

```
Claude calls: save_decision(
  title: "Use PostgreSQL over SQLite for production",
  body: "SQLite works for dev but concurrent writes under load...",
  status: "accepted"
)
```

These decisions are searchable in future sessions.

### Multi-Vault Search

If you manage multiple projects, each with its own vault:

```bash
same vault add work /path/to/work/notes
same vault add personal /path/to/personal/notes
```

Then `search_across_vaults` searches all registered vaults and merges results by relevance score.

## Security Notes

- All data stays local. Embeddings are computed on your machine via Ollama.
- Write operations are rate-limited (30/minute) to prevent prompt injection abuse.
- Note content is capped at 100KB per save. Read content capped at 1MB.
- Private directories (`_PRIVATE/`) are excluded from search results.
- The `vault feed` command (CLI only) includes a PII guard for cross-vault propagation.

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Tools not appearing | Run `same setup mcp` to re-register |
| Search returns nothing | Run `same reindex` to rebuild embeddings |
| Ollama not connected | Start Ollama: `ollama serve`, then restart Claude Code |
| Wrong vault | Check `same status` to see which vault path is active |
