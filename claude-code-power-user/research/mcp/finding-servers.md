---
title: "Where to Find MCP Servers"
tags: [mcp, servers, directories, discovery, ecosystem]
content_type: research
domain: engineering
---

# Where to Find MCP Servers

## The Question

You know MCP can extend Claude Code with external tools. But where do you actually find servers worth installing? There are five main directories, plus Anthropic's official servers.

## MCP Directories

### Glama (glama.ai/mcp/servers)

The largest curated directory. Categorized, searchable, with quality indicators.

- **Strengths**: Curated listings, health checks, popularity metrics
- **Best for**: Browsing by category, finding well-maintained servers
- **Tip**: Sort by "most used" to find battle-tested options

### Smithery (smithery.ai)

npm-installable servers with a focus on easy setup.

- **Strengths**: One-command install, standardized packaging
- **Best for**: Quick setup without reading docs
- **Tip**: `npx @smithery/cli install <server-name>` handles config automatically

### mcp.so

Community directory with broad coverage.

- **Strengths**: Wide listing, community ratings
- **Best for**: Finding niche or specialized servers

### PulseMCP (pulsemcp.com)

Focused on real-time monitoring and discovery.

- **Strengths**: Server health monitoring, uptime tracking
- **Best for**: Evaluating server reliability before committing

### awesome-mcp-servers (GitHub)

Classic awesome-list format. Community-maintained on GitHub.

- **Strengths**: Open source, easy to contribute, PR-based vetting
- **Best for**: Comprehensive list scanning, finding new entries

## Anthropic's Official Servers

The `@modelcontextprotocol` org on npm publishes reference implementations:

| Server | Package | What It Does |
|--------|---------|-------------|
| Filesystem | `@modelcontextprotocol/server-filesystem` | Read/write files |
| GitHub | `@modelcontextprotocol/server-github` | Issues, PRs, repos |
| PostgreSQL | `@modelcontextprotocol/server-postgres` | Database queries |
| SQLite | `@modelcontextprotocol/server-sqlite` | SQLite queries |
| Brave Search | `@modelcontextprotocol/server-brave-search` | Web search |
| Fetch | `@modelcontextprotocol/server-fetch` | HTTP requests |
| Puppeteer | `@modelcontextprotocol/server-puppeteer` | Browser automation |
| Memory | `@modelcontextprotocol/server-memory` | Key-value memory |
| Google Maps | `@modelcontextprotocol/server-google-maps` | Location APIs |
| Slack | `@modelcontextprotocol/server-slack` | Slack messages |

Install any of these with:

```bash
npx -y @modelcontextprotocol/server-<name>
```

## How to Evaluate a Server

Before adding an MCP server to your setup, check these:

### 1. Maintenance Activity

- **Last commit**: More than 6 months ago is a warning sign
- **Open issues**: Many unanswered issues = likely abandoned
- **Release cadence**: Regular releases indicate active maintenance

### 2. Security

| Transport | Risk Level | Notes |
|-----------|-----------|-------|
| stdio | Lower | Server runs locally as subprocess |
| SSE | Higher | Server runs as HTTP service, may be remote |

**Prefer stdio servers.** They run as local subprocesses with no network exposure. SSE servers open HTTP endpoints and introduce network attack surface.

### 3. Stars and Usage

- GitHub stars show interest, not quality — but 0 stars on a 1-year-old repo is a signal
- Check if the server appears in multiple directories (cross-listed = more visibility)
- Look for mentions in discussions or blog posts

### 4. Permissions Model

Read what the server accesses:

- Does a "database server" need filesystem access? Suspicious.
- Does it require broad env vars or API keys? Understand why.
- Does it phone home or have telemetry? Check the code.

### 5. Dependencies

- Fewer dependencies = smaller attack surface
- Check if deps are well-known packages or obscure
- `npm audit` / `pip audit` the package before running

## Server Categories

| Category | Examples | Use Cases |
|----------|---------|-----------|
| **Filesystem** | filesystem, SAME | Read/write files, manage notes |
| **Database** | postgres, sqlite, supabase | Query data, explore schemas |
| **Search** | brave-search, exa, tavily | Web research, documentation lookup |
| **Browser** | puppeteer, playwright | Scraping, testing, screenshots |
| **Git/GitHub** | github, gitlab | PR management, issue tracking |
| **Cloud** | aws, gcp, cloudflare | Infrastructure management |
| **Monitoring** | sentry, datadog | Error tracking, metrics |
| **Communication** | slack, linear, notion | Team tools integration |

## Practical Workflow

1. **Start with Anthropic's official servers** — they are well-tested
2. **Search Glama for your specific need** — filter by category
3. **Check GitHub for stars and recent activity** — skip unmaintained repos
4. **Read the README** — understand what permissions it needs
5. **Test in a throwaway project first** — never install untested servers in production work
6. **Pin versions** — use specific versions, not `latest`, for reproducibility

## Configuration Pattern

Once you find a server, add it to settings:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@org/server-name@1.2.3"],
      "type": "stdio",
      "env": {
        "API_KEY": "your-key-here"
      }
    }
  }
}
```

Keep API keys out of version control. Use environment variables or a secrets manager.
