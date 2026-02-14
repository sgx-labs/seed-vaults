---
title: "How Do I Test Agents (Unit Tests, Integration Tests, Quality Harnesses)?"
tags: [testing, quality, harness, stochastic, metrics, unit-test, integration-test, red-teaming, pass-at-1]
content_type: research
domain: engineering
---

# How Do I Test Agents (Unit Tests, Integration Tests, Quality Harnesses)?

## The Question

How do you test a system whose outputs are non-deterministic?

## The Testing Pyramid for Agents

```
                    /\
                   /  \     Quality Harness (statistical, N trials)
                  /    \
                 /------\
                / Integ. \  Integration Tests (agent + tools, scripted)
               /----------\
              / Unit Tests  \ Tool logic, parsers, validators (deterministic)
             /________________\
```

The base is standard software testing. The top is unique to agents.

## Level 1: Unit Tests (Deterministic)

Test everything that does NOT involve the LLM:

```python
# Test tool implementations
def test_search_returns_results():
    db = create_test_db()
    results = search_code(db, "auth middleware")
    assert len(results) > 0
    assert all(r.score > 0 for r in results)

# Test output validators
def test_pii_detection():
    assert scan_for_pii("Contact user@example.com") == ["email"]
    assert scan_for_pii("No PII here") == []

# Test guardrails
def test_blocked_commands():
    assert not check_command("rm -rf /")
    assert check_command("ls -la")
```

Unit tests should cover tools, validators, guardrails, parsers, and state management logic.

## Level 2: Integration Tests (Scripted Scenarios)

Test the agent end-to-end with controlled inputs:

```python
def test_agent_creates_file():
    agent = create_test_agent(tools=[read_file, write_file, search])
    result = agent.run("Create a Python file called hello.py that prints 'hello world'")

    assert os.path.exists("test_workspace/hello.py")
    content = read_file("test_workspace/hello.py")
    assert "hello" in content.lower()
```

**Tips for integration tests:**
- Use a sandboxed workspace
- Mock external services (APIs, databases)
- Assert on outcomes, not exact text
- Allow for variation in how the agent accomplishes the task

## Level 3: Quality Harness (Statistical)

Run the agent against a dataset of tasks. Measure aggregate quality.

```python
QUALITY_DATASET = [
    {"task": "Fix the type error in auth.py", "expected": "auth.py compiles", "check": "code_compiles"},
    {"task": "Add input validation to the API", "expected": "validation exists", "check": "pattern_exists"},
    {"task": "Write tests for the user model", "expected": "tests pass", "check": "tests_pass"},
]

def run_quality_check(agent, dataset, n_trials=5):
    results = []
    for case in dataset:
        passes = 0
        for _ in range(n_trials):
            result = agent.run(case["task"])
            if assess_result(result, case["check"], case["expected"]):
                passes += 1
        results.append({
            "task": case["task"],
            "pass_rate": passes / n_trials,
        })
    return results
```

### Key Quality Metrics

| Metric | Formula | Meaning |
|--------|---------|---------|
| pass@1 | P(success in 1 try) | First-try reliability |
| pass@5 | P(at least 1 success in 5 tries) | Reliability with retries |
| pass^5 | P(all 5 succeed) | Consistency (high bar) |
| MRR | Mean Reciprocal Rank | How quickly the right answer appears |

### Assessor Types

- **Code execution** — Run the output, check if it compiles/passes tests
- **Pattern matching** — Check for required elements in the output
- **LLM-as-judge** — Use another model to grade quality (calibrate carefully)
- **Human review** — Gold standard, does not scale

## Red Teaming

Test with adversarial inputs specifically designed to break the agent:

- Prompt injection attempts
- Ambiguous instructions
- Requests for out-of-scope actions
- Edge cases in tool parameters
- Very long inputs
- Empty inputs

## See Also

- `hub/reliability.md` — Reliability overview
- `research/reliability/eval-harness-design.md` — Building quality infrastructure
- `research/deployment/monitoring-observability.md` — Production monitoring
