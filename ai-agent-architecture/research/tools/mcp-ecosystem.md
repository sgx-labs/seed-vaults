---
title: "What MCP Servers Are Available and How Do I Find Them?"
tags: [MCP, ecosystem, servers, directories, community]
content_type: research
domain: engineering
---

# What MCP Servers Are Available and How Do I Find Them?

## The Question

Where do I find MCP servers for common tasks, and what is the current ecosystem like?

## MCP Server Directories

| Directory | URL | Focus |
|-----------|-----|-------|
| Glama | glama.ai/mcp/servers | Curated, quality-focused |
| Smithery | smithery.ai | Largest collection |
| mcp.run | mcp.run | Hosted MCP servers |
| MCP Hub | mcphub.io | Community-maintained |
| Awesome MCP | github.com/punkpeye/awesome-mcp-servers | GitHub awesome list |

## Popular MCP Server Categories

### Development Tools

- **Filesystem** — Read, write, search files in a project
- **Git** — Git operations (status, diff, commit, branch)
- **GitHub** — Issues, PRs, reviews, actions
- **Database** — Query PostgreSQL, SQLite, MongoDB
- **Docker** — Container management

### Knowledge and Search

- **Web search** — Brave Search, Google, Tavily
- **Documentation** — Read and search docs sites
- **Memory** — Persistent memory (SAME, Mem0)
- **RAG** — Vector search over custom knowledge bases

### Communication

- **Slack** — Read/send messages, search channels
- **Email** — Draft and send emails (with approval)
- **Notion** — Read and write Notion pages

### Monitoring

- **Sentry** — Error tracking and analysis
- **Datadog** — Metrics and dashboards
- **CloudWatch** — AWS monitoring

## Building vs Installing

| Approach | When to use |
|----------|------------|
| Install community server | Standard integration (GitHub, Slack, databases) |
| Build custom server | Internal tools, proprietary APIs, custom logic |
| Wrap existing API | Quick integration with any REST API |

## Quality Considerations

Not all community MCP servers are production-ready. Check for:

- **Maintenance** — When was the last commit? Are issues being responded to?
- **Security** — Does it handle credentials safely? Does it validate inputs?
- **Error handling** — Does it fail gracefully or crash?
- **Documentation** — Are tool descriptions clear? Are parameters documented?
- **Testing** — Does it have tests?

## Connecting MCP Servers

### Claude Code

```json
// .claude/settings.json
{
  "mcpServers": {
    "memory": {
      "command": "same",
      "args": ["mcp"],
      "env": {}
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_..."
      }
    }
  }
}
```

### Custom Agent

```python
from anthropic.tools.mcp import MCPServerStdio

async with MCPServerStdio(command="same", args=["mcp"]) as server:
    tools = await server.list_tools()
    # Tools are now available for the agent to use
```

## See Also

- `entities/mcp-protocol.md` — MCP protocol current state
- `research/tools/mcp-server-design.md` — Building MCP servers
- `hub/tools.md` — Tools overview
