---
title: "What Is Context Engineering and Why Does It Matter?"
tags: [context-engineering, tokens, prompt-design, architecture]
content_type: research
domain: engineering
---

# What Is Context Engineering and Why Does It Matter?

## The Question

What is context engineering, and why is it becoming the most important skill for agent builders?

## Definition

Context engineering is the discipline of curating the smallest, highest-signal set of tokens the model sees at each step. It is the bridge between "my agent sometimes works" and "my agent reliably works."

Every token in the context window either helps or hurts. Irrelevant tokens dilute the signal. Missing tokens cause the model to guess. Context engineering is about getting this balance right.

## Why It Matters More Than Prompt Engineering

Prompt engineering focuses on HOW you ask the model to do something. Context engineering focuses on WHAT information the model has when it makes decisions.

```
Prompt engineering: "Think step by step and be thorough"
Context engineering: Ensuring the right 3 code files and 2 decisions are in context
                    when the agent decides what to refactor
```

For agents, context engineering has a larger impact on reliability than prompt wording.

## The Context Budget

```
Total context window: 200,000 tokens
  - System prompt:           2,000 tokens (1%)
  - Tool definitions:        3,000 tokens (1.5%)
  - Retrieved knowledge:     5,000 tokens (2.5%)
  - Conversation history:    40,000 tokens (20%)
  - Current tool results:    10,000 tokens (5%)
  - Reserved for output:     8,000 tokens (4%)
  - Available headroom:      132,000 tokens (66%)
```

The goal is to keep the non-headroom portion as small and relevant as possible.

## Context Engineering Techniques

### 1. Just-in-Time Retrieval

Do not pre-load everything. Retrieve what is needed, when it is needed.

```python
# Bad: Load all project docs at session start
context = load_all_docs()  # 50K tokens of mixed relevance

# Good: Retrieve relevant docs per query
context = search_knowledge_base(current_query, top_k=3)  # 3K tokens, high relevance
```

### 2. Progressive Disclosure

Start with summaries. Expand to details only when the agent needs them.

```
Level 1: File list with summaries (200 tokens)
Level 2: Specific file contents (2000 tokens)
Level 3: Related files and test files (5000 tokens)
```

### 3. Context Pruning

Remove information that is no longer relevant:
- Old tool results that have been superseded
- Conversation turns about completed subtasks
- Error messages from resolved issues

### 4. Structured Notes Over Raw Data

```
Bad context (5000 tokens):
  Full 200-line file dump

Good context (500 tokens):
  "auth/middleware.py: JWT validation middleware. Key function:
   validate_token() on line 42. Known issue: doesn't handle
   expired refresh tokens (see decision AD-007)."
```

### 5. Metadata-First Retrieval

Return metadata before content. Let the agent decide what to expand:

```json
{
  "results": [
    {"title": "JWT Auth Decision", "relevance": 0.95, "tokens": 450},
    {"title": "Rate Limiting Guide", "relevance": 0.72, "tokens": 1200},
    {"title": "API Design Standards", "relevance": 0.65, "tokens": 800}
  ]
}
```

## The Context Engineering Loop

```
1. Agent receives task
2. Minimal context loaded (system prompt + task)
3. Agent decides what it needs to know
4. Retrieval provides targeted context
5. Agent acts with augmented context
6. Old context pruned if no longer relevant
7. Repeat from step 3
```

## See Also

- `entities/context-window.md` — Token limits by model
- `research/memory/context-window-management.md` — Window management
- `research/memory/rag-for-agents.md` — RAG for agents
