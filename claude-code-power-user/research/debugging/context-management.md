---
title: "Managing the Context Window Effectively"
tags: [context-window, optimization, session-management, workflow, token-budget, compaction, file-reads]
content_type: research
domain: engineering
---

# Managing the Context Window Effectively

The context window is a fixed resource. Everything Claude sees competes for space. Managing it well is the difference between productive multi-hour sessions and grinding to a halt after 30 minutes.

---

## What Is the Context Window?

Claude Code has approximately 200K tokens. Roughly 150K are usable for conversation. The rest goes to system prompts, tool definitions, MCP descriptions, and overhead.

Every token consumed is unavailable for new reasoning. As context fills, responses slow and Claude attends less to earlier information.

---

## What Fills Context

| Source | Typical Size | Accumulates? |
|---|---|---|
| Your messages | 50-500 tokens each | Yes |
| Claude's responses | 200-2000 tokens each | Yes |
| File reads | 500-5000 tokens per file | Yes |
| Tool results (Bash, Grep, Glob) | 100-3000 tokens per call | Yes |
| MCP tool descriptions | 100-500 tokens per tool | Fixed at start |
| Hook output | 200-1000 tokens per hook | At session start |
| System prompt | ~2000 tokens | Fixed |
| Edit results | 100-500 tokens per edit | Yes |
| Error messages and retries | 200-1000 tokens per failure | Yes |

The biggest consumers are **file reads** and **search results**. A single large file can take 5000+ tokens. A broad Grep can take 3000+.

---

## Context Budget

```
Total:                          ~200K tokens
System + tool defs + MCP:        ~17K tokens
Available:                      ~183K tokens

Typical file read:                ~2K tokens
Typical message pair:             ~1K tokens
Typical tool result:              ~1K tokens

Roughly: 90 file reads, OR 180 message exchanges, OR a mix.
```

A complex debugging session:

```
20 file reads:       40K     |  Comfortable at ~90K total
30 message pairs:    30K     |  But add 40 more file reads
15 tool results:     15K     |  and you're at 170K with
5 error cycles:       5K     |  little room left.
```

---

## Strategy 1: One Task Per Session

Start a new session for each distinct task.

**Good scoping:**
- "Fix the authentication bug in the login handler"
- "Add pagination to the users API"
- "Write tests for payment processing"

**Bad scoping:**
- "Fix all bugs, add the feature, refactor the module, and update docs"

Each topic shift carries dead weight from previous topics.

**Transition:** Before ending, ask Claude to summarize accomplishments and remaining work. Paste that summary into the next session.

---

## Strategy 2: Subagents for Research

Exploration fills main context with file reads that are never referenced again.

**Without subagent:**
```
Search the codebase for database query functions without error handling.
```
Claude reads 30 files (60K tokens) to find 4 functions.

**With subagent:**
```
Use a subagent to find database query functions lacking error handling.
I need file paths and function names.
```
Subagent reads 30 files in its own context. Returns ~500 token summary.

**Use subagents for:** Exploring unfamiliar codebases, finding patterns across many files, any task where you need a summary not raw content.

---

## Strategy 3: Reference by Path, Don't Paste Code

**Wastes context (code appears twice -- in your message and when Claude reads the file):**
```
Here's my function, fix the bug:
function processPayment(amount, currency, userId) {
  // ... 50 lines ...
}
```

**Conserves context:**
```
Bug in processPayment in src/payments/processor.ts, line 34.
Exchange rate conversion doesn't handle null rates.
```

Claude reads the file once. Your message is 30 tokens instead of 500.

---

## Strategy 4: /compact When Bloated

The `/compact` command summarizes earlier messages while preserving recent ones.

**How it works:**
1. Early conversation gets summarized into a shorter form
2. Recent messages preserved verbatim
3. Token count drops, freeing space

**Use after:** Long exploratory phases, completing a sub-task, noticing slower responses, Claude repeating itself.

**What it loses:** Exact wording of early messages, specific details from early file reads, nuances of early decisions.

**Prefer a new session when:** Switching tasks entirely, early context is misleading (false starts), past 120K tokens.

---

## Strategy 5: Control What Gets Read

**High cost:** `Read all files in src/auth/ and explain authentication.`

**Low cost:** `Read src/auth/middleware.ts and explain token validation.`

**Lowest cost:** `What does validateToken do in src/auth/middleware.ts? Focus on lines 20-45.`

Use line ranges when you know them. Go broad only when you genuinely do not know where something is.

---

## What Happens When Context Is Full

1. **Responses slow down** -- more input to process per response
2. **Early context fades** -- less attention to older messages
3. **Claude contradicts itself** -- lost nuance from compressed history
4. **Hard limit hit** -- Claude Code says to start a new session

**Signs you're approaching the limit:** Slower responses, Claude asking about things already discussed, re-reading files, redundant tool calls.

---

## Cheat Sheet

| Situation | Action |
|---|---|
| Starting a new task | New session |
| 60+ minutes in | Consider /compact |
| Need to explore many files | Use a subagent |
| Want to reference code | Give a file path, not a paste |
| Session feels slow | /compact or new session |
| Switching topics | New session |
| Multi-file refactor | One sub-task per session |
| Completed a milestone | Summarize, then /compact or new session |

---

## Token Cost Reference

| Operation | Approximate Tokens |
|---|---|
| Read a 100-line file | 1,000-1,500 |
| Read a 500-line file | 4,000-6,000 |
| Grep with 20 matches | 1,500-2,500 |
| Glob listing 50 files | 500-800 |
| Bash with moderate output | 500-1,500 |
| Your detailed prompt | 200-500 |
| Claude's typical response | 500-2,000 |
| A code edit | 300-800 |
| MCP tool call + result | 500-2,000 |

If you have done 40 file reads and 50 exchanges, you have likely used 100K+ tokens. Time to compact or start fresh.
