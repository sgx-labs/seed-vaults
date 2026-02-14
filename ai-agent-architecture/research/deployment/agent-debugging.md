---
title: "How Do I Debug Agent Behavior (Tracing, Logging, Replay)?"
tags: [debugging, tracing, logging, replay, observability, prompt-inspection, diff-analysis, time-travel]
content_type: research
domain: engineering
---

# How Do I Debug Agent Behavior (Tracing, Logging, Replay)?

## The Question

When an agent does the wrong thing, how do you figure out why?

## Why Agent Debugging Is Hard

Traditional software debugging: set a breakpoint, inspect variables, step through code. Agent debugging: the "code" is a natural language conversation with an LLM. You cannot set a breakpoint in a thought process. The same input may produce different behavior on different runs.

## The Debugging Toolkit

### 1. Structured Logging

Log every decision point with context:

```python
import structlog

logger = structlog.get_logger()

def agent_step(messages, tools):
    logger.info("agent.step.start",
        message_count=len(messages),
        tool_count=len(tools),
        last_message_role=messages[-1]["role"],
    )

    response = llm.generate(messages, tools=tools)

    logger.info("agent.step.complete",
        stop_reason=response.stop_reason,
        tool_calls=len(response.tool_calls),
        output_tokens=response.usage.output_tokens,
    )

    return response
```

### 2. Trace Reconstruction

Reconstruct the agent's full decision path from logs:

```
Session: sess_abc123
  Turn 1: User asks "Fix the auth bug"
  Turn 2: Agent thinks, calls search_code("auth error handling")
  Turn 3: search_code returns 3 results
  Turn 4: Agent thinks, calls read_file("src/auth/middleware.py")
  Turn 5: read_file returns file content (245 lines)
  Turn 6: Agent thinks, calls write_file("src/auth/middleware.py", ...)
  Turn 7: write_file succeeds
  Turn 8: Agent responds "I fixed the null check on line 42"
```

Each turn shows what the agent saw and what it decided. If it made a bad decision at Turn 4, you can see exactly what input led to that choice.

### 3. Prompt Inspection

Save the full prompt (system + messages + tools) for each LLM call:

```python
def log_prompt(messages, tools, response):
    debug_record = {
        "timestamp": datetime.now().isoformat(),
        "full_prompt": messages,
        "tools": tools,
        "response": response.content,
        "stop_reason": response.stop_reason,
        "tokens": {
            "input": response.usage.input_tokens,
            "output": response.usage.output_tokens,
        }
    }
    save_debug_record(debug_record)
```

This lets you replay the exact prompt that produced the bad output.

### 4. LangGraph Time-Travel Debugging

LangGraph's checkpoint system enables time-travel:

```python
# Get all checkpoints for a session
checkpoints = list(checkpointer.list(config))

# Inspect state at any checkpoint
for cp in checkpoints:
    print(f"Step: {cp.metadata['step']}")
    print(f"State: {cp.channel_values}")

# Resume from a specific checkpoint
result = app.invoke(None, config={"configurable": {
    "thread_id": "task-123",
    "checkpoint_id": cp.checkpoint_id,
}})
```

### 5. Diff Analysis

Compare successful and failed runs of the same task:

```python
def compare_runs(success_trace, failure_trace):
    for i, (s, f) in enumerate(zip(success_trace, failure_trace)):
        if s["action"] != f["action"]:
            print(f"Divergence at step {i}:")
            print(f"  Success: {s['action']} with {s['params']}")
            print(f"  Failure: {f['action']} with {f['params']}")
            break
```

## Common Root Causes

| Symptom | Common cause | How to diagnose |
|---------|-------------|----------------|
| Wrong tool called | Ambiguous tool descriptions | Inspect tool definitions |
| Hallucinated data | Missing context | Check what was in the prompt |
| Infinite loop | No stop condition | Count iterations in logs |
| Inconsistent results | Temperature > 0 | Set temperature=0 for debugging |
| Ignoring instructions | Buried in long context | Check instruction position in prompt |

## See Also

- `research/deployment/monitoring-observability.md` — Production monitoring
- `research/reliability/testing-agents.md` — Testing agents
- `hub/deployment.md` — Deployment overview
