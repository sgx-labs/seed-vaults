---
title: "How Do I Manage Context Windows in Long-Running Agents?"
tags: [context-window, tokens, management, compaction, long-running]
content_type: research
domain: engineering
---

# How Do I Manage Context Windows in Long-Running Agents?

## The Question

How do you keep a long-running agent effective when the context window fills up?

## The Problem

Every message, tool call, and tool result consumes tokens. A typical agent session:

```
System prompt:          ~2,000 tokens
Tool definitions (10):  ~3,000 tokens
User messages:          ~500 tokens each
Agent responses:        ~1,000 tokens each
Tool results:           ~500-5,000 tokens each
```

After 20 turns with tool use, you are easily at 50K+ tokens. After 50 turns, you may hit 200K. Quality degrades before you hit the hard limit.

## Strategies

### 1. Context Budgeting

Allocate token budgets to each category. Track usage.

```python
BUDGET = {
    "system_prompt": 2000,
    "tool_definitions": 3000,
    "conversation": 100000,  # most of the window
    "reserved_output": 8000,
}

def tokens_remaining():
    return MODEL_LIMIT - sum(count_tokens(msg) for msg in messages)
```

When conversation budget runs low, trigger compaction.

### 2. Sliding Window

Keep the last N messages. Drop the oldest.

```python
MAX_MESSAGES = 40

if len(messages) > MAX_MESSAGES:
    # Keep system prompt + last N messages
    messages = [messages[0]] + messages[-MAX_MESSAGES:]
```

**Limitation:** Loses early context. The agent forgets what it did at the start of the session.

### 3. Summarize-and-Continue

When approaching the limit, summarize the conversation and continue with the summary.

```python
if tokens_remaining() < COMPACTION_THRESHOLD:
    summary = llm.generate(
        "Summarize this conversation. Include: what was accomplished, "
        "what is pending, key decisions made, and current state."
    )
    messages = [
        {"role": "system", "content": system_prompt},
        {"role": "system", "content": f"Previous conversation summary:\n{summary}"},
    ]
```

**Limitation:** Lossy compression. Details get dropped. The summary model might miss important nuances.

### 4. Just-in-Time Retrieval

Instead of keeping everything in context, retrieve only when needed.

```
Turn 1: User asks about auth → Agent searches knowledge base → relevant notes injected
Turn 2: User asks about database → Agent searches again → different notes injected
```

Each turn gets fresh, relevant context instead of accumulating everything.

### 5. Multi-Window Agents

For tasks that genuinely exceed one context window, chain multiple windows:

```
Window 1: Research phase → write structured summary to memory
Window 2: Implementation phase (reads summary from Window 1)
Window 3: Testing phase (reads summary from Window 2)
```

Each window starts clean but inherits state through external memory.

## The Quality Cliff

Models do not degrade gracefully. Performance is stable until about 50-60% of the context window, then drops off. The practical limit is often half the advertised limit. Build compaction triggers well before you hit the wall.

## See Also

- `entities/context-window.md` — Current token limits by model
- `research/memory/persistent-memory-patterns.md` — Cross-session memory
- `hub/memory.md` — Memory overview
