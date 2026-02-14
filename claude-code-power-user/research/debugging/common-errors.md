---
title: "Common Claude Code Error Messages and What They Mean"
tags: [debugging, errors, troubleshooting, claude-code]
content_type: research
domain: engineering
---

# Common Claude Code Error Messages and What They Mean

Six errors you will see most often, what triggers them, and how to fix them.

---

## Quick Reference

| Error | Root Cause | Immediate Fix |
|---|---|---|
| Permission denied | Tool not approved | Approve or add to allowlist |
| Rate limit exceeded | Too many API calls | Wait 60s or batch requests |
| Context window exceeded | Conversation too large | Start a new session |
| MCP server connection failed | Bad config or missing binary | Verify path and config |
| Tool execution failed | Underlying command errored | Read the tool output |
| Authentication error | Invalid or expired token | Run `/login` |

---

## 1. Permission Denied

**Cause:** Claude Code requires explicit approval for each tool use. Bash commands, file writes, and other actions must be approved unless pre-allowed.

**Fix:** Approve when prompted, or add trusted tools to your allowlist:

```json
{
  "permissions": {
    "allow": [
      "Bash(git *)",
      "Bash(npm test)",
      "Bash(make *)",
      "Read",
      "Glob",
      "Grep"
    ]
  }
}
```

**Prevent:** Pre-allow common patterns (git, test runners, build tools). Use globs like `Bash(npm *)` for families of commands. Keep destructive commands out of the allowlist.

---

## 2. Rate Limit Exceeded

**Cause:** The Anthropic API enforces per-account rate limits. Rapid tool calls, agentic loops, and parallel subagents can trigger 429 errors.

**Fix:** Wait 30-60 seconds. Claude Code usually retries with backoff automatically. If persistent, check your API tier and usage dashboard.

**Prevent:** Batch operations into single requests. Reference specific files instead of broad searches. Use `/compact` to reduce token count per request.

---

## 3. Context Window Exceeded

**Cause:** Claude has a ~200K token context window. Everything accumulates: messages, responses, file reads, tool results, MCP descriptions, and hook output. Long sessions on large codebases fill this fast.

**Fix:** Start a new session with `/clear`. Before starting fresh, ask Claude to summarize what was accomplished and what remains, then paste that into the next session.

**Prevent:** One major task per session. Use `/compact` proactively. Have Claude read only needed files. Use subagents for exploratory research (results come back summarized).

---

## 4. MCP Server Connection Failed

**Cause:** The MCP server binary does not exist at the configured path, is not executable, or crashes during initialization. Common triggers: wrong path in config, binary not installed, missing env vars, PATH differences.

**Fix:**

1. Verify the binary: `which same && ls -la /usr/local/bin/same`
2. Test manually:
   ```bash
   echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"capabilities":{}}}' | /path/to/binary
   ```
3. Check config: `cat ~/.claude/settings.json | jq '.mcpServers'`
4. Check stderr: MCP servers log errors to stderr.

**Prevent:** Use absolute paths in config. Restart the session after installing or updating a server. Add a smoke test to your setup script.

---

## 5. Tool Execution Failed

**Cause:** The tool ran but the underlying command returned an error. This is not a Claude Code problem. Examples: failing tests, missing files, compilation errors, edit mismatches.

**Fix:** Read the full error output. If Claude paraphrased, ask for the raw output. Fix the underlying issue and retry.

**Prevent:** Be specific about file paths and expected state. Ask Claude to check preconditions before destructive commands. Have Claude read error logs directly rather than guessing.

---

## 6. Authentication Error

**Cause:** API key or session token is invalid, expired, or missing. Happens on first run, after long idle periods, or if credentials are deleted.

**Fix:**

```bash
claude /login
```

Or set the key directly:

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

**Prevent:** Add `ANTHROPIC_API_KEY` to your shell profile if using key-based auth. Re-authenticate before long sessions if using OAuth.

---

## General Debugging Workflow

1. **Read the exact error message.** Do not paraphrase.
2. **Match it** to the table above.
3. **Try the immediate fix.**
4. **If it persists**, use `--verbose` for more details.
5. **If still stuck**, start a fresh session. Many transient issues resolve with a clean context.
