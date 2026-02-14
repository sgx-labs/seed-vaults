---
title: "How Do I Make Function Calling Work Consistently?"
tags: [function-calling, reliability, tools, validation, errors]
content_type: research
domain: engineering
---

# How Do I Make Function Calling Work Consistently?

## The Question

Why do agents sometimes call the wrong tool, pass wrong parameters, or ignore available tools entirely?

## Common Failure Modes

| Failure | Cause | Fix |
|---------|-------|-----|
| Wrong tool selected | Ambiguous tool descriptions | Make descriptions mutually exclusive |
| Hallucinated parameters | Missing or vague param descriptions | Add examples, tighten schema |
| Tool not called | Agent thinks it can answer without tools | Add "always use X tool for Y task" to system prompt |
| Infinite tool loops | No termination condition | Max iterations + explicit stop condition |
| Partial parameters | Optional params with no defaults | Set defaults in schema |

## Strategies That Improve Reliability

### 1. Reduce Tool Count

Agents choose more accurately from fewer options. If you have 20 tools, the agent spends more tokens deciding which one to use. Consider:
- Grouping related tools behind a single tool with a `action` parameter (only when actions are truly related)
- Lazy loading tools — only expose tools relevant to the current task phase
- Using a tool-selection step before the actual tool call

### 2. Add Negative Examples to Descriptions

```json
{
  "name": "search_code",
  "description": "Search for code patterns in the codebase. Use for finding function definitions, class names, imports. Do NOT use for searching documentation (use search_docs instead)."
}
```

Telling the agent when NOT to use a tool is as important as telling it when to use it.

### 3. Validate Before Executing

```python
def execute_tool(name: str, params: dict) -> dict:
    schema = get_tool_schema(name)
    errors = validate_params(params, schema)
    if errors:
        return {
            "status": "error",
            "message": f"Invalid parameters: {errors}",
            "hint": f"Expected: {schema['required']}"
        }
    return tool_handlers[name](**params)
```

Catch bad parameters before they cause downstream failures. Return helpful errors so the agent can self-correct.

### 4. System Prompt Guidance

For critical tools, add explicit guidance to the system prompt:

```
When the user asks about code structure, ALWAYS use the search_code tool first.
Do not attempt to answer code questions from memory alone.
```

This overrides the model's tendency to generate answers without checking.

### 5. Tool Result Formatting

Make tool results easy for the model to parse:

```json
{
  "status": "success",
  "results": [
    {"file": "src/auth.py", "line": 42, "snippet": "def validate_token(token):"},
    {"file": "src/auth.py", "line": 87, "snippet": "def refresh_token(refresh):"}
  ],
  "total_matches": 2,
  "query": "token validation"
}
```

Include the original query so the model can verify the results are relevant.

## See Also

- `research/tools/tool-design-patterns.md` — Tool design principles
- `hub/tools.md` — Tools overview
- `research/reliability/error-handling-patterns.md` — Error handling
