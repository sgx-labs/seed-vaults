---
title: "When Should I Use Structured Output vs Free-Form?"
tags: [structured-output, JSON-schema, validation, tools, reliability]
content_type: research
domain: engineering
---

# When Should I Use Structured Output vs Free-Form?

## The Question

When should I force the agent to return structured JSON, and when is free-form text better?

## The Rule of Thumb

**If a machine reads the output, use structured output. If a human reads it, free-form is fine.**

Structured output (JSON Schema constrained generation) guarantees the response matches a schema. No parsing, no regex, no "hopefully the model returns valid JSON." The provider validates the output against your schema before returning it.

## When to Use Structured Output

| Scenario | Why structured |
|----------|---------------|
| API calls | Downstream systems need exact field names and types |
| Database writes | Schema must match table columns |
| Tool parameters | Next tool call needs specific input format |
| Data extraction | Extract entities, dates, numbers from text |
| Classification | Finite set of categories |
| Multi-step pipelines | Agent output feeds another agent's input |

## When to Use Free-Form

| Scenario | Why free-form |
|----------|--------------|
| Explanations | Natural language is better for humans |
| Creative content | Structured constraints kill creativity |
| Debugging output | Agent reasoning is naturally unstructured |
| Conversational responses | Users expect natural dialogue |

## Implementation

### Claude (Anthropic API)

```python
response = client.messages.create(
    model="claude-sonnet-4-5-20250929",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Extract entities from this text..."}],
    tool_choice={"type": "tool", "name": "extract_entities"},
    tools=[{
        "name": "extract_entities",
        "description": "Extract named entities from text",
        "input_schema": {
            "type": "object",
            "properties": {
                "people": {"type": "array", "items": {"type": "string"}},
                "organizations": {"type": "array", "items": {"type": "string"}},
                "dates": {"type": "array", "items": {"type": "string"}},
            },
            "required": ["people", "organizations", "dates"]
        }
    }]
)
```

### OpenAI (Response Format)

```python
response = client.chat.completions.create(
    model="gpt-4.1",
    messages=[{"role": "user", "content": "Extract entities..."}],
    response_format={
        "type": "json_schema",
        "json_schema": {
            "name": "entity_extraction",
            "schema": {
                "type": "object",
                "properties": {
                    "people": {"type": "array", "items": {"type": "string"}},
                },
                "required": ["people"]
            }
        }
    }
)
```

## The Hybrid Pattern

For many agent tasks, the best approach is structured metadata with a free-form body:

```json
{
  "status": "completed",
  "confidence": 0.92,
  "category": "bug_fix",
  "summary": "Fixed the null pointer exception in the auth middleware by adding a guard clause on line 42. The issue occurred when the session token was expired but the refresh token was still valid.",
  "files_changed": ["src/auth/middleware.py"]
}
```

Machines read `status`, `confidence`, `category`, `files_changed`. Humans read `summary`.

## See Also

- `hub/tools.md` — Tools overview
- `research/tools/tool-design-patterns.md` — Tool design patterns
- `research/reliability/guardrail-implementation.md` — Output validation
