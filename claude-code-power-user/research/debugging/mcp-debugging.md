---
title: "Debugging MCP Server Issues"
tags: [mcp, debugging, troubleshooting, servers, configuration]
content_type: research
domain: engineering
---

# Debugging MCP Server Issues

MCP servers extend Claude Code with custom tools. When they break, tools silently disappear or connection errors appear. This guide covers systematic diagnosis.

---

## Symptom Quick Reference

| Symptom | Likely Cause | Section |
|---|---|---|
| "Connection failed" on startup | Bad path, binary missing, crash on init | 1 |
| Tools do not appear | Config format wrong, server not listed | 2 |
| Tool calls return errors | Server-side bug, bad input schema | 3 |
| Tools work but are slow | Large responses, expensive computation | 4 |
| Works in terminal, fails in Claude Code | PATH/env differences | 5 |

---

## 1. Server Won't Connect

**Verify the binary exists and is executable:**

```bash
which same
ls -la /usr/local/bin/same
/usr/local/bin/same --version
chmod +x /usr/local/bin/same    # if needed
```

**Test the server manually with a raw JSON-RPC initialize request:**

```bash
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}' \
  | /usr/local/bin/same mcp
```

You should get a JSON capabilities response. No output or an error means a startup bug.

**Capture stderr** (where MCP servers log errors):

```bash
echo '...' | /usr/local/bin/same mcp 2>/tmp/mcp-stderr.log
cat /tmp/mcp-stderr.log
```

Common stderr findings: missing database, permission denied on data dir, missing env vars, dependency not found.

**Verify config** in `~/.claude/settings.json` or `.claude/settings.json`:

```json
{
  "mcpServers": {
    "same": {
      "command": "/usr/local/bin/same",
      "args": ["mcp"],
      "env": {}
    }
  }
}
```

Common mistakes: relative path, missing `args`, JSON syntax errors. Validate with `jq`:

```bash
cat ~/.claude/settings.json | jq .
```

---

## 2. Tools Not Showing Up

**Is the server in config?** Check with `jq '.mcpServers'`.

**Right file?** Claude Code reads project-level `.claude/settings.json` first, then user-level `~/.claude/settings.json`. A project config can shadow the user config.

**Did you restart?** MCP servers initialize at session start. Config changes mid-session have no effect. Run `/clear`.

**Does tools/list work?** Test manually:

```bash
(echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}'
echo '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}') \
  | /usr/local/bin/same mcp
```

The second response should list tools. Empty or error means the server is not registering them.

---

## 3. Tool Calls Failing

| Error Pattern | Cause | Fix |
|---|---|---|
| "invalid params" | Wrong argument types or missing fields | Check tool's input schema |
| "not found" | Referenced resource doesn't exist | Verify paths, IDs, names |
| "internal error" | Server-side exception | Check server stderr logs |
| "timeout" | Server took too long | See Section 4 |

**Test a tool call in isolation:**

```bash
(echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}'
echo '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search_notes","arguments":{"query":"test","top_k":5}}}') \
  | /usr/local/bin/same mcp 2>/tmp/mcp-stderr.log
```

This isolates whether the problem is in Claude Code or the server.

---

## 4. Performance Issues

| Problem | Indicator | Fix |
|---|---|---|
| Slow embeddings | 5-30s per call | Use faster model or pre-index |
| Large payloads | Thousands of lines returned | Reduce `top_k`, add pagination |
| Unoptimized queries | Consistent 2-5s per call | Add indexes, check query plans |
| Slow startup | First call slow, rest fast | Keep server process alive |

Every byte returned enters Claude's context window. Design tools to return concise results.

---

## 5. Common Gotchas

**PATH differences:** Claude Code may not inherit your shell PATH. Always use absolute paths in config:

```json
{ "command": "/usr/local/bin/same" }
```

Not: `{ "command": "same" }`

**Missing env vars:** Set them in config, not your shell profile. Claude Code spawns servers in a clean environment:

```json
{
  "env": {
    "DATABASE_PATH": "/home/user/data/notes.db"
  }
}
```

**Wrong transport:** MCP supports stdio (default) and SSE. Stdio needs `command` + `args`. SSE needs a `url` field. Mixing them fails silently.

**JSON syntax errors:** One bad comma breaks the whole file. Always validate: `cat ~/.claude/settings.json | jq . > /dev/null`

---

## Debugging Checklist

1. Binary exists at the configured absolute path
2. Binary is executable
3. Binary runs manually without errors
4. `settings.json` is valid JSON
5. Server entry is in the correct settings file
6. Session was restarted after config changes
7. `initialize` returns capabilities
8. `tools/list` returns expected tools
9. `tools/call` with test arguments returns data
10. stderr shows no errors

If all 10 pass and it still fails, check for Claude Code updates or file a bug.
