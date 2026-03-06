---
title: "Connect to Claude Code"
tags: [claude-code, mcp, setup, integration, hooks, configuration]
content_type: guide
domain: same-getting-started
---

# Connect SAME to Claude Code

## The Quick Way

```bash
cd ~/my-project
same init
```

When `same init` detects Claude Code, it automatically:
1. Installs 6 hooks that inject context at session start
2. Adds SAME as an MCP server so Claude Code can use all 12 tools
3. Creates a `.same/config.toml` with sensible defaults

That's it. Start Claude Code and it will have persistent memory.

## What Gets Installed

### Hooks (6 total)

Hooks run automatically at specific points in a Claude Code session. SAME installs hooks that:

- Load pinned notes at session start
- Surface the latest handoff
- Include recent decisions
- Show git state for orientation

You can see installed hooks with:

```bash
same status
```

### MCP Server

SAME registers itself as an MCP server. This gives Claude Code access to 12 tools:

- `search_notes` -- Search your vault
- `save_decision` -- Log decisions
- `create_handoff` -- Create session handoffs
- And 9 more (see `entities/mcp-server.md`)

## Manual Setup

If `same init` didn't configure MCP automatically, add this to your `.mcp.json` (in your project root or home directory):

```json
{
  "mcpServers": {
    "same": {
      "command": "npx",
      "args": ["-y", "@sgx-labs/same", "mcp", "--vault", "/path/to/your/vault"]
    }
  }
}
```

Replace `/path/to/your/vault` with the absolute path to your notes directory.

## Verifying the Connection

### Check SAME Status

```bash
same status
```

Look for:
- Hooks: installed
- MCP: configured
- Vault: indexed

### Test from Claude Code

Start a Claude Code session and ask:

> "Search my SAME vault for getting started"

If SAME is connected, Claude Code will use the `search_notes` tool and return results from your vault. If it doesn't, check the MCP configuration.

### Check Hook Output

```bash
same doctor
```

This runs 19 diagnostic checks including hook verification.

## How It Works in Practice

Once connected, Claude Code automatically:

1. **Session start** -- Loads your pinned notes, latest handoff, and git state
2. **During work** -- Searches your vault when you ask questions, logs decisions when you make them
3. **Session end** -- Creates a handoff capturing what was done and what's next

You don't need to tell Claude Code to do any of this. The CLAUDE.md and hooks handle it.

## Multiple Vaults

If you have multiple vaults (e.g., project notes + a SeedVault), SAME can search across all of them:

```bash
same vault add ~/my-seedvault
```

Then use `search_across_vaults` to find notes in any vault.

## Troubleshooting

**"MCP server not found"** -- Check that SAME is installed and in your PATH. Try `which same` or `npx @sgx-labs/same --version`.

**"Hooks not firing"** -- Run `same setup hooks` to reinstall. Verify with `same status`.

**"No results from search"** -- Run `same reindex` to rebuild the index. Then test with `same search "test query"`.

## Next Steps

- Set up your daily workflow: `guides/daily-workflow.md`
- Understand MCP in depth: `research/how-mcp-works.md`
- Learn about session memory: `hub/session-memory.md`
