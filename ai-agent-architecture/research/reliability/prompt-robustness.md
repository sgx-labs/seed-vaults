---
title: "How Do I Design Prompts That Work Across Model Versions?"
tags: [prompts, robustness, versioning, prompt-engineering, reliability]
content_type: research
domain: engineering
---

# How Do I Design Prompts That Work Across Model Versions?

## The Question

How do you write system prompts and instructions that do not break when the model is updated?

## Why Prompts Break

Models change behavior between versions. A prompt that works perfectly on one version may behave differently on the next. Common breakage modes:

- Model follows instructions more literally (breaks prompts that relied on inference)
- Model follows instructions less literally (breaks prompts that relied on exact compliance)
- Model has different default behaviors (verbosity, formatting, tool use preferences)
- New capabilities change how the model interprets old instructions

## Principles for Robust Prompts

### 1. Be Explicit, Not Implicit

```
Bad:  "Handle errors appropriately"
Good: "When a tool call fails, retry once. If it fails again, return an error message
       that includes the tool name, error type, and a suggested alternative action."
```

Do not rely on the model "knowing" what you mean. Spell it out.

### 2. Use Structured Formats

```
Bad:  "Return the results in a nice format"
Good: "Return results as a JSON object with keys: status (string: 'success' or 'error'),
       data (array of objects with 'title' and 'score' fields), count (integer)."
```

Structured formats are more robust than natural language descriptions of format.

### 3. Include Examples

```
When classifying severity, use these levels:
- critical: System is down, data loss occurring
- high: Feature broken, workaround possible
- medium: Degraded performance, no data loss
- low: Cosmetic issue, documentation gap

Example: "Login page returns 500 error" -> critical
Example: "Dark mode toggle misaligned on mobile" -> low
```

Examples anchor the model's behavior more reliably than abstract descriptions.

### 4. Version Your Prompts

Treat system prompts like code. Version them. Test them.

```
prompts/
  v1.0/
    system.md
  v1.1/
    system.md   # Updated for newer model
  v1.2/
    system.md   # Added tool use instructions
```

### 5. Test Across Models

Run your quality harness against multiple models and model versions:

```python
MODELS_TO_TEST = [
    "claude-sonnet-4-5-20250929",
    "claude-haiku-4-5-20251001",
    "gpt-4.1",
]

for model in MODELS_TO_TEST:
    results = run_quality_check(agent, dataset, model=model)
    print(f"{model}: pass@1 = {results.pass_rate:.2f}")
```

### 6. Separate Concerns

```
System prompt: WHO the agent is, WHAT it does, CONSTRAINTS it follows
Tool descriptions: WHEN to use each tool, WHAT it returns
Task prompt: WHAT to do right now, WITH what context
```

Keep these separate. A monolithic prompt is harder to maintain and debug.

## The Migration Playbook

When a new model version drops:

1. Run your quality harness against the new model (change nothing else)
2. Compare pass rates to the previous model
3. Identify regressions (tasks that now fail)
4. Update prompts to fix regressions
5. Verify fixes do not break other tasks
6. Deploy with the new model + updated prompts together

## See Also

- `hub/reliability.md` — Reliability overview
- `research/reliability/testing-agents.md` — Agent testing
- `research/reliability/eval-harness-design.md` — Quality harness design
