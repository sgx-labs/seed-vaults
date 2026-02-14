---
title: "Troubleshooting — Common Errors and Fixes"
tags: [troubleshooting, errors, debugging, fixes, common-issues, context-window, mcp-errors]
content_type: hub
domain: engineering
---

# Troubleshooting — Common Errors and Fixes

## Overview

Troubleshooting Claude Code covers common errors (permission denied, rate limit exceeded, context window full, MCP connection failed, authentication errors), debugging hooks that don't fire, MCP server diagnosis, performance slowdowns, context management strategies, and effective bug reporting. This hub provides quick fixes for the most frequent issues and links to detailed debugging guides for hooks, MCP servers, performance optimization, and context window management.

## Quick Fixes

| Problem | Likely cause | Fix |
|---------|-------------|-----|
| "Permission denied" on tool use | Not pre-approved | Add to `.claude/settings.json` `permissions.allow` |
| Claude doesn't know about recent changes | Context compacted | Reference specific file paths to reload |
| Slow responses | Large context window | Start fresh session, use subagents for research |
| MCP server not connecting | Wrong path or not installed | Check `claude mcp list`, verify server binary exists |
| Hook not firing | Not configured or wrong event | Check `.claude/settings.json` hooks section |
| Claude ignores CLAUDE.md rules | File not in project root | Move to repo root, verify filename is exactly `CLAUDE.md` |
| "Rate limit exceeded" | Too many API calls | Wait and retry, or batch operations |
| Git operations failing | Auth not configured | Set up `gh auth login` or SSH keys |

## Common Scenarios

### Claude Made Unwanted Changes
Don't panic. Git is your safety net:
```bash
git diff              # See what changed
git checkout -- file  # Revert specific file
git stash             # Stash all changes
```

### Context Window Full
Signs: responses get slower, Claude forgets earlier context, compaction messages appear.

Solutions:
1. Start a new session (cheapest fix)
2. Use subagents for research tasks (protects main context)
3. Reference files by path instead of pasting content
4. Use plan mode to think before writing (avoids throwaway code)

See: `entities/context-window.md`

### MCP Server Issues
```bash
# List configured servers
claude mcp list

# Test a server manually
echo '{"jsonrpc":"2.0","method":"initialize","params":{},"id":1}' | same mcp

# Check server logs
# Servers write to stderr, check your terminal output
```

See: `research/debugging/mcp-debugging.md`

### Hook Debugging
```bash
# Test a hook manually
same hook session-start

# Check hook output
# Hook stdout becomes context, stderr is logged
```

See: `research/hooks/hook-debugging.md`

## Research Notes

- `research/debugging/common-errors.md` — Error messages and what they mean
- `research/debugging/mcp-debugging.md` — MCP server troubleshooting
- `research/debugging/effective-bug-reports.md` — Getting Claude to fix bugs faster
- `research/debugging/performance-issues.md` — Dealing with slow responses
- `research/debugging/context-management.md` — Managing the context window

## See Also

- `hub/project-setup.md` — Configuration that prevents common issues
- `hub/hooks.md` — Hook debugging specifics
- `hub/mcp-servers.md` — MCP server troubleshooting
- `hub/prompt-patterns.md` — Better prompts produce fewer errors
