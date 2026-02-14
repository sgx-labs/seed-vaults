---
title: "How Do I Measure Agent Quality (Metrics, Benchmarks, A/B Tests)?"
tags: [quality, metrics, benchmarks, measurement, AB-testing]
content_type: research
domain: engineering
---

# How Do I Measure Agent Quality (Metrics, Benchmarks, A/B Tests)?

## The Question

What metrics define "good" for an agent, and how do you compare agent versions?

## Core Quality Metrics

### Task-Level Metrics

| Metric | What it measures | How to compute |
|--------|-----------------|---------------|
| pass@1 | First-try success rate | successes / total_attempts |
| pass@k | Success within k tries | 1 - (1 - pass@1)^k |
| Task completion rate | End-to-end success | completed_tasks / total_tasks |
| Partial credit | How close to correct | rubric-based scoring (0-1) |

### Operational Metrics

| Metric | What it measures | Target |
|--------|-----------------|--------|
| Latency (p50, p95) | Response speed | < 10s (p50), < 30s (p95) |
| Cost per task | Efficiency | Varies by task type |
| Tool call efficiency | Minimal unnecessary calls | < 2x optimal path |
| Context utilization | How much context used vs available | 30-60% is healthy |

### Safety Metrics

| Metric | What it measures | Target |
|--------|-----------------|--------|
| Guardrail trigger rate | How often safety catches fire | Stable or decreasing |
| Harmful action rate | Actions blocked by guardrails | 0% reaching users |
| Off-topic rate | Agent goes outside scope | < 5% |

## A/B Testing Agents

### Design

```python
def ab_test_agent(task, agent_a, agent_b, n_trials=50):
    results_a = [agent_a.run(task) for _ in range(n_trials)]
    results_b = [agent_b.run(task) for _ in range(n_trials)]

    score_a = sum(assess(r) for r in results_a) / n_trials
    score_b = sum(assess(r) for r in results_b) / n_trials

    # Statistical significance
    from scipy.stats import mannwhitneyu
    stat, p_value = mannwhitneyu(
        [assess(r) for r in results_a],
        [assess(r) for r in results_b],
    )

    return {
        "agent_a_score": score_a,
        "agent_b_score": score_b,
        "p_value": p_value,
        "significant": p_value < 0.05,
    }
```

### Sample Size

Agent outputs are stochastic. You need enough trials for statistical significance:
- **Minimum 30 trials** per variant for rough comparison
- **50-100 trials** for reliable p-values
- **More trials for small effect sizes** (1% improvement needs hundreds of trials)

### What to A/B Test

- Model versions (Sonnet 4 vs Sonnet 4.5)
- System prompt variations
- Tool configurations
- Context management strategies (RAG vs full-context)
- Guardrail settings

## Benchmarking Against Baselines

Create internal benchmarks that reflect your actual workload:

```python
BENCHMARK_SUITE = {
    "simple_tasks": [
        "Fix the typo in README.md",
        "Add a docstring to the main function",
    ],
    "medium_tasks": [
        "Add input validation to the user registration endpoint",
        "Write unit tests for the payment module",
    ],
    "hard_tasks": [
        "Refactor the monolithic handler into separate service classes",
        "Diagnose and fix the intermittent race condition in the cache layer",
    ],
}
```

Track benchmark scores over time. Any agent change that drops scores below the baseline is a regression.

## See Also

- `research/reliability/testing-agents.md` — Testing agents
- `research/reliability/eval-harness-design.md` — Building quality infrastructure
- `hub/deployment.md` — Deployment overview
