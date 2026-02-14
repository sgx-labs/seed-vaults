---
title: "How Do I Build an MCP Server for Agents?"
tags: [MCP, server, tools, protocol, implementation]
content_type: research
domain: engineering
---

# How Do I Build an MCP Server for Agents?

## The Question

How do I build an MCP server that exposes tools, resources, or prompts to any AI agent?

## Minimal MCP Server (TypeScript)

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-tools",
  version: "1.0.0",
});

// Define a tool
server.tool(
  "search_docs",
  "Search documentation for a topic. Returns matching sections with relevance scores.",
  {
    query: z.string().describe("Search query (e.g., 'authentication setup')"),
    limit: z.number().default(5).describe("Max results to return"),
  },
  async ({ query, limit }) => {
    const results = await searchIndex(query, limit);
    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(results, null, 2),
        },
      ],
    };
  }
);

// Start the server
const transport = new StdioServerTransport();
await server.connect(transport);
```

## Minimal MCP Server (Python)

```python
from mcp.server import Server
from mcp.server.stdio import stdio_server
from mcp.types import Tool, TextContent

server = Server("my-tools")

@server.list_tools()
async def list_tools():
    return [
        Tool(
            name="search_docs",
            description="Search documentation for a topic",
            inputSchema={
                "type": "object",
                "properties": {
                    "query": {"type": "string", "description": "Search query"},
                    "limit": {"type": "integer", "default": 5},
                },
                "required": ["query"],
            },
        )
    ]

@server.call_tool()
async def call_tool(name: str, arguments: dict):
    if name == "search_docs":
        results = search_index(arguments["query"], arguments.get("limit", 5))
        return [TextContent(type="text", text=str(results))]

async def main():
    async with stdio_server() as (read, write):
        await server.run(read, write, server.create_initialization_options())
```

## MCP Server Design Checklist

```
[ ] Every tool has a clear, specific description
[ ] Parameter descriptions include examples
[ ] Required vs optional parameters are explicit
[ ] Errors return helpful messages (not stack traces)
[ ] Output is consistent JSON shape
[ ] Server handles graceful shutdown
[ ] Long-running operations have timeouts
[ ] Sensitive operations require confirmation (via elicitation)
```

## Transport Options

| Transport | Use case | Setup |
|-----------|----------|-------|
| stdio | Local CLI tools, same-machine agents | Simplest — pipe stdin/stdout |
| SSE | Web-based servers, remote agents | HTTP server with SSE endpoint |
| Streamable HTTP | Flexible client-server | Newer, supports bidirectional |

For most agent-local tools, stdio is the right choice. It is the simplest, fastest, and requires no network configuration.

## Testing MCP Servers

Use the MCP Inspector for interactive testing:

```bash
npx @modelcontextprotocol/inspector node build/index.js
```

This opens a web UI where you can call tools, inspect schemas, and verify responses without connecting to an actual agent.

## See Also

- `entities/mcp-protocol.md` — MCP protocol current state
- `research/tools/tool-design-patterns.md` — Tool design principles
- `research/tools/tool-security.md` — Security for agent tools
