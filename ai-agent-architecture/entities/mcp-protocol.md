---
title: "MCP — Model Context Protocol Current State"
tags: [MCP, protocol, tools, standard, entity, interoperability, JSON-RPC, tool-schema]
content_type: entity
domain: engineering
pinned: true
---

# MCP — Model Context Protocol Current State

## What It Is

Model Context Protocol (MCP) is the open standard for connecting AI agents to tools and data sources. Originally created by Anthropic, now adopted by OpenAI, Google, Microsoft, and most major AI providers. It is becoming the USB-C of agent tool interfaces.

## Protocol Architecture

```
AI Agent (MCP Client)
  ↕ JSON-RPC over stdio/SSE/HTTP
MCP Server (your tool)
  ↕
External Service (API, DB, filesystem)
```

**Transport options:**
- **stdio** — Local process communication (most common for CLI tools)
- **SSE** — Server-Sent Events over HTTP (web-based servers)
- **Streamable HTTP** — Newer transport for flexible client-server communication

## Three Primitives

| Primitive | Who controls | Purpose |
|-----------|-------------|---------|
| **Tools** | Model-controlled | Functions the agent can call |
| **Resources** | Application-controlled | Data the agent can read |
| **Prompts** | User-controlled | Pre-built prompt templates |

## Tool Schema Example

```json
{
  "name": "search_notes",
  "description": "Search the knowledge base for relevant notes",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": {
        "type": "string",
        "description": "Natural language search query"
      },
      "top_k": {
        "type": "integer",
        "description": "Number of results to return",
        "default": 5
      }
    },
    "required": ["query"]
  }
}
```

## Specification Timeline

| Version | Date | Key changes |
|---------|------|-------------|
| Initial | Nov 2024 | Core protocol, tools/resources/prompts |
| June 2025 | Jun 2025 | Structured tool output, elicitation, auth updates |
| Nov 2025 | Nov 2025 | Async execution, enterprise auth, long-running tasks |

## Key Design Decisions

- **JSON-RPC 2.0** — Standard RPC protocol, well-tooled
- **Schema-first** — Tools declare their interface; clients validate
- **Stateless by default** — Each tool call is independent
- **Provider-agnostic** — Same server works with Claude, GPT, Gemini

## Adoption Status (February 2026)

MCP has crossed into mainstream adoption. All major AI providers support it natively. Enterprise adoption is accelerating with the November 2025 spec adding authorization and governance features. MCP directories (Glama, Smithery, mcp.run) list thousands of community servers.

## Last Updated

February 2026
