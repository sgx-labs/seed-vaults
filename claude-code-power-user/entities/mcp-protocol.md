---
title: "MCP Protocol — Current State"
tags: [mcp, protocol, entity, tools, servers, stdio, sse, json-schema]
content_type: entity
domain: engineering
---

# MCP Protocol — Current State

## What It Is
Model Context Protocol — an open standard for connecting AI agents to external tools and data sources. Created by Anthropic, adopted across the ecosystem.

## How It Works
1. Server exposes tools with JSON schema descriptions
2. Client (Claude Code) discovers tools at session start
3. Agent decides when to call tools during conversation
4. Results flow back into conversation context

## Transport Types

| Transport | How it works | Use when |
|-----------|-------------|----------|
| stdio | Server runs as subprocess, talks via stdin/stdout | Local tools, CLI tools |
| SSE | Server runs as HTTP service, Server-Sent Events | Remote services, shared servers |

## Configuration in Claude Code

```json
{
  "mcpServers": {
    "server-name": {
      "command": "path/to/server",
      "args": ["arg1"],
      "type": "stdio"
    }
  }
}
```

Place in `.claude/settings.json` (project) or `~/.claude/settings.json` (global).

## Key Directories
- **Glama** — glama.ai/mcp/servers
- **Smithery** — smithery.ai
- **mcp.so** — mcp.so
- **PulseMCP** — pulsemcp.com
- **awesome-mcp-servers** — GitHub list

## Server SDK
Official SDKs: TypeScript, Python, Kotlin, C#. Build custom servers with `@modelcontextprotocol/sdk`.

## Last Updated
February 2026
