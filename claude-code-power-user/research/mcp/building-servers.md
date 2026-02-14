---
title: "Building Your Own MCP Server"
tags: [mcp, server, sdk, typescript, python, development, custom-tools, json-rpc]
content_type: research
domain: engineering
---

# Building Your Own MCP Server

## The Question

When should you build your own MCP server, and how do you do it? If no existing server covers your use case — an internal API, a proprietary database, a custom workflow — building one is straightforward with the official SDKs.

## When to Build vs. Use Existing

**Build your own when:**
- You need to access an internal/proprietary system
- Existing servers are abandoned or insecure
- You want tight control over what data is exposed
- Your use case is organization-specific (deploy scripts, internal APIs, custom tooling)

**Use existing when:**
- An official or well-maintained server already covers it
- The server has proper security review and active maintenance
- Your use case is standard (databases, git, web search)

## SDK Options

| Language | Package | Best For |
|----------|---------|----------|
| TypeScript | `@modelcontextprotocol/sdk` | npm distribution, web ecosystem |
| Python | `mcp` | Scripting, rapid prototyping |
| Go | `github.com/modelcontextprotocol/go-sdk/mcp` | Single binary distribution |

## Minimal TypeScript Server

This example creates a server with one tool that looks up user info from an internal API.

```bash
mkdir my-mcp-server && cd my-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node
npx tsc --init
```

```typescript
// src/index.ts
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-internal-api",
  version: "1.0.0",
});

// Define a tool
server.tool(
  "lookup_user",
  "Look up a user by email address",
  {
    email: z.string().email().describe("User email address"),
  },
  async ({ email }) => {
    // Replace with your actual API call
    const response = await fetch(
      `https://internal-api.example.com/users?email=${encodeURIComponent(email)}`,
      {
        headers: {
          Authorization: `Bearer ${process.env.API_TOKEN}`,
        },
      }
    );

    if (!response.ok) {
      return {
        content: [{ type: "text", text: `Error: ${response.statusText}` }],
        isError: true,
      };
    }

    const user = await response.json();
    return {
      content: [{ type: "text", text: JSON.stringify(user, null, 2) }],
    };
  }
);

// Start the server
const transport = new StdioServerTransport();
await server.connect(transport);
```

Build with `npx tsc`, run with `node dist/index.js`, then register:

```json
{
  "mcpServers": {
    "my-internal-api": {
      "command": "node",
      "args": ["/path/to/my-mcp-server/dist/index.js"],
      "type": "stdio",
      "env": {
        "API_TOKEN": "your-token-here"
      }
    }
  }
}
```

## Minimal Python Server

```python
# server.py
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-tool-server")

@mcp.tool()
def check_service_health(service_name: str) -> str:
    """Check if an internal service is healthy."""
    # Replace with your actual health check
    import urllib.request
    try:
        url = f"https://internal.example.com/{service_name}/health"
        req = urllib.request.Request(url)
        with urllib.request.urlopen(req, timeout=5) as resp:
            return f"{service_name}: {resp.status} OK"
    except Exception as e:
        return f"{service_name}: DOWN — {e}"

if __name__ == "__main__":
    mcp.run(transport="stdio")
```

Register it:

```json
{
  "mcpServers": {
    "service-health": {
      "command": "python",
      "args": ["/path/to/server.py"],
      "type": "stdio"
    }
  }
}
```

## Key Concepts

**Transport**: Default to stdio (subprocess, stdin/stdout). It is simpler, more secure, and what Claude Code expects. Use SSE only for shared/remote servers.

**Tool definitions** need three things: a name (snake_case), a description (Claude reads this to decide when to call it — be specific), and an input schema (JSON Schema or Zod).

**Return format**: Tools return `{ content: [{ type: "text", text: "..." }], isError: false }`. Set `isError: true` for error responses.

## Testing Your Server

### Manual: Pipe JSON-RPC to stdin

```bash
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-03-26","capabilities":{},"clientInfo":{"name":"test","version":"0.1"}}}' | node dist/index.js
```

### With MCP Inspector

```bash
npx @modelcontextprotocol/inspector node dist/index.js
```

This opens a web UI where you can browse tools, call them with test inputs, and see responses.

### In Claude Code

The fastest real-world test: add the server to your config, start a new Claude Code session, and ask Claude to use the tool.

## Publishing

- **npm**: `npm publish` — users install with `npx -y your-package-name`
- **pip**: `python -m build && twine upload dist/*`
- **Go**: Build a binary — no runtime dependency required

## Common Mistakes

- **Too many tools**: Claude gets confused with 20+ tools. Keep it focused.
- **Vague descriptions**: Claude uses the description to decide when to call a tool. Be specific.
- **Missing error handling**: Always return `isError: true` with a useful message on failure.
- **Exposing secrets in responses**: Never return API keys, tokens, or credentials in tool output.
- **No rate limiting**: If your tool calls an external API, add rate limiting to prevent runaway usage.
