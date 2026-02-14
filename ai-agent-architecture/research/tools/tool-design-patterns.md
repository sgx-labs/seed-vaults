---
title: "How Do I Design Tools That Agents Can Use Effectively?"
tags: [tools, design, patterns, function-calling, reliability]
content_type: research
domain: engineering
---

# How Do I Design Tools That Agents Can Use Effectively?

## The Question

What makes a tool easy for an agent to use correctly, and what design mistakes cause agents to misuse tools?

## The Core Insight

The agent reads tool names and descriptions to decide WHEN to use a tool. It reads parameter schemas to decide HOW to use it. If either is ambiguous, the agent will guess — and guessing causes errors.

## Design Principles

### 1. Name It Like You Would Name a Function

```
Good: search_codebase, create_pull_request, read_file
Bad:  handle_request, process, do_thing, manage_resource
```

The name should tell the agent what the tool DOES. Verbs are better than nouns.

### 2. Write Descriptions for the Agent, Not for Humans

```json
{
  "name": "search_notes",
  "description": "Search the knowledge base for notes matching a query. Use this when you need background information, prior decisions, or context about a topic. Returns ranked results with titles and snippets. Use top_k to control how many results you get."
}
```

Tell the agent:
- **When** to use this tool (trigger condition)
- **What** it returns (output shape)
- **When NOT** to use it (if there is a common confusion with another tool)

### 3. Make Parameters Obvious

```json
{
  "properties": {
    "query": {
      "type": "string",
      "description": "Natural language search query (e.g., 'authentication patterns')"
    },
    "top_k": {
      "type": "integer",
      "description": "Number of results to return. Default 5. Max 20.",
      "default": 5
    }
  },
  "required": ["query"]
}
```

- Use `description` on every parameter
- Provide examples in descriptions
- Set sensible defaults
- Mark required vs optional explicitly

### 4. One Tool, One Action

```
Good: create_file, read_file, delete_file (three tools)
Bad:  manage_file(action="create"|"read"|"delete") (one tool)
```

Agents choose between tools more reliably than they fill in mode parameters. More small tools beats fewer complex tools.

### 5. Return Consistent, Parseable Output

```json
{
  "status": "success",
  "data": { "file_path": "/src/auth.py", "lines_changed": 12 },
  "message": "File updated successfully"
}
```

Always include a status indicator. Always return the same shape. The agent's next thought depends on understanding what happened.

### 6. Fail Loudly

```json
{
  "status": "error",
  "error": "File not found: /src/auth.py",
  "suggestion": "Use search_files to find the correct path"
}
```

Never return an empty success when something went wrong. Include actionable error messages that help the agent recover.

## Common Mistakes

| Mistake | Consequence | Fix |
|---------|------------|-----|
| Vague description | Agent uses tool at wrong time | Be specific about when/why |
| No examples | Agent guesses parameter format | Add examples in descriptions |
| Too many parameters | Agent leaves optionals blank or hallucinates | Fewer params with defaults |
| Silent failure | Agent thinks action succeeded | Always return clear status |
| God tool | Agent confused by many modes | Split into focused tools |

## See Also

- `hub/tools.md` — Tools overview
- `research/tools/mcp-server-design.md` — Building MCP servers
- `research/tools/function-calling-reliability.md` — Making calls work consistently
