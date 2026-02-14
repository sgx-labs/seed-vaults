---
title: "How Do I Build a Quality Harness for Agents?"
tags: [quality, harness, metrics, benchmarks, assessment]
content_type: research
domain: engineering
---

# How Do I Build a Quality Harness for Agents?

## The Question

How do you build infrastructure that measures agent quality at scale?

## What a Quality Harness Does

A quality harness is the infrastructure that:
1. Takes a dataset of tasks with expected outcomes
2. Runs the agent against each task (often multiple times)
3. Scores each result
4. Aggregates metrics across all tasks and trials
5. Reports quality trends over time

## Architecture

```
Task Dataset (tasks + expectations)
  |
Runner (executes agent N times per task, in parallel)
  |
Scorer (checks each result against expectations)
  |
Aggregator (computes metrics: pass rate, latency, cost)
  |
Reporter (dashboard, CI gate, alerts)
```

## Building the Task Dataset

The dataset is the most important component. Bad assessments give false confidence.

```python
task_cases = [
    {
        "id": "auth-001",
        "task": "Add rate limiting to the login endpoint",
        "context": {"files": ["src/auth/routes.py"]},
        "checks": {
            "type": "code_check",
            "assertions": [
                "rate_limit decorator exists on login route",
                "tests pass after change",
                "no existing tests broken",
            ]
        },
        "difficulty": "medium",
        "tags": ["auth", "security"],
    }
]
```

### Dataset Best Practices

- **50+ cases minimum** for statistical significance
- **Diverse difficulty** — easy, medium, hard
- **Tagged by category** — so you can analyze failure patterns
- **Versioned** — track dataset changes alongside agent changes
- **Ground truth from humans** — not from another LLM

## Scoring Functions

### Code Execution Scorer

```python
def score_code_task(result, case):
    workspace = create_sandbox(case["context"])
    apply_agent_changes(workspace, result)

    # Does it compile?
    compile_ok = run_command(workspace, "python -m py_compile *.py")

    # Do tests pass?
    test_result = run_command(workspace, "pytest")

    return {
        "compiles": compile_ok.returncode == 0,
        "tests_pass": test_result.returncode == 0,
        "assertions": check_assertions(workspace, case["checks"]["assertions"]),
    }
```

### LLM-as-Judge Scorer

```python
def score_with_llm(result, case):
    judgment = llm.generate(
        f"Assess this agent output against the expected behavior.\n"
        f"Task: {case['task']}\n"
        f"Expected: {case['checks']['assertions']}\n"
        f"Actual output: {result}\n"
        f"Score 1-5 and explain your reasoning."
    )
    return parse_score(judgment)
```

**Warning:** LLM-as-judge requires calibration. Run it against human-scored examples to verify alignment.

## CI Integration

Run quality checks on every significant change. Gate merges on quality thresholds:

```yaml
# .github/workflows/quality.yml
quality:
  runs-on: ubuntu-latest
  steps:
    - run: python run_checks.py --dataset tasks/core.json --trials 3
    - run: python check_threshold.py --min-pass-rate 0.85
```

## Quality Tracking

Track metrics over time to catch regressions:

| Metric | v1.0 | v1.1 | v1.2 | Trend |
|--------|------|------|------|-------|
| pass@1 | 0.72 | 0.78 | 0.81 | Up |
| pass@5 | 0.89 | 0.92 | 0.94 | Up |
| Avg cost | $0.42 | $0.38 | $0.35 | Down |
| Avg latency | 12s | 11s | 10s | Down |

## See Also

- `research/reliability/testing-agents.md` — Testing overview
- `research/deployment/monitoring-observability.md` — Production monitoring
- `hub/reliability.md` — Reliability overview
