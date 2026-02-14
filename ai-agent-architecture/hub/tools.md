---
title: "Tools — Design, MCP, and Function Calling"
tags: [tools, MCP, function-calling, tool-design, schemas]
content_type: hub
domain: engineering
---

# Tools — Design, MCP, and Function Calling

Overview: This hub covers agent tool design, Model Context Protocol (MCP) server implementation, function calling reliability, structured output with JSON Schema, and tool security patterns. It addresses how to write tool descriptions that agents use correctly, build MCP servers in TypeScript and Python, validate tool parameters, handle tool errors, and implement least-privilege permissions for agent actions. Start here when designing tools, integrating MCP servers, or debugging function calling issues.

## Why Tool Design Matters

Tools are how agents interact with the world. A poorly designed tool causes the agent to hallucinate parameters, call the wrong tool, or misinterpret results. Good tool design is the highest-leverage improvement you can make to agent reliability.

## The Tool Interface Stack

```
Agent (LLM)
  ↓ function call (JSON)
Tool Router (SDK / Framework)
  ↓ validated parameters
Tool Implementation (your code)
  ↓ result
Agent (LLM observes result)
```

## MCP — The Standard

Model Context Protocol (MCP) is the emerging standard for tool interfaces. Adopted by Anthropic, OpenAI, Google, and Microsoft. Define tools once, use them across any provider.

**MCP primitives:**
- **Tools** — Functions the agent can call (search, create, delete)
- **Resources** — Data the agent can read (files, database records)
- **Prompts** — Pre-built prompt templates for common tasks

## Tool Design Principles

1. **Descriptive names.** `search_codebase` not `sc`. The agent reads the name to decide when to use it.
2. **Clear descriptions.** Tell the agent when to use this tool and what it returns.
3. **Typed parameters.** Use JSON Schema. Required vs optional must be explicit.
4. **Small surface area.** One tool per action. `create_file` and `update_file`, not `manage_file`.
5. **Predictable output.** Consistent shape. Always return status + data, or error + message.
6. **Fail loudly.** Return clear error messages. Never silently succeed when something went wrong.

## Structured vs Free-Form Output

| Approach | When to use | Risk |
|----------|------------|------|
| JSON Schema | Critical paths, data extraction, API calls | Over-constraining creative tasks |
| Free-form text | Summaries, explanations, creative content | Unparseable output in downstream systems |
| Hybrid | Structured metadata + free-form body | Slightly more complex tool design |

## Research Notes

- `research/tools/tool-design-patterns.md` — Patterns that make agents use tools reliably
- `research/tools/mcp-server-design.md` — Building MCP servers for agents
- `research/tools/structured-output.md` — When and how to use JSON Schema output
- `research/tools/tool-security.md` — Security considerations for agent tool use
- `research/tools/function-calling-reliability.md` — Making function calls work consistently

## See Also

- `hub/architecture.md` — Agent patterns that define how tools are orchestrated
- `hub/reliability.md` — Guardrails, validation, and safety constraints for tool use
- `hub/frameworks.md` — Framework-specific tool integration patterns
- `hub/deployment.md` — Monitoring tool call success rates in production
