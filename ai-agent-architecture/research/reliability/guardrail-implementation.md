---
title: "How Do I Implement Guardrails and Safety Constraints?"
tags: [guardrails, safety, constraints, validation, permissions, PII-detection, prompt-injection, cost-caps]
content_type: research
domain: engineering
---

# How Do I Implement Guardrails and Safety Constraints?

## The Question

How do you prevent agents from taking harmful, off-scope, or expensive actions?

## The Three-Layer Model

```
Layer 1: Input Guardrails  — Filter what goes into the agent
Layer 2: Action Guardrails — Control what the agent can do
Layer 3: Output Guardrails — Validate what comes out
```

Each layer catches what the previous missed. All three are needed in production.

## Layer 1: Input Guardrails

### Prompt Injection Detection

```python
INJECTION_PATTERNS = [
    r"ignore (?:all )?previous instructions",
    r"you are now",
    r"system:\s*",
    r"<\|.*?\|>",  # Special tokens
]

def check_injection(user_input: str) -> bool:
    for pattern in INJECTION_PATTERNS:
        if re.search(pattern, user_input, re.IGNORECASE):
            return True
    return False
```

### Input Length Limits

```python
MAX_INPUT_TOKENS = 10000

def validate_input(text: str) -> str:
    tokens = count_tokens(text)
    if tokens > MAX_INPUT_TOKENS:
        raise InputTooLongError(f"Input exceeds {MAX_INPUT_TOKENS} token limit")
    return text
```

## Layer 2: Action Guardrails

### Tool Allowlists

```python
ALLOWED_TOOLS = {
    "search_code", "read_file", "write_file",
    "run_tests", "search_docs",
}
APPROVAL_REQUIRED = {
    "delete_file", "execute_command", "send_email",
}
BLOCKED_TOOLS = {
    "drop_database", "format_disk",
}

def check_tool_permission(tool_name: str) -> str:
    if tool_name in BLOCKED_TOOLS:
        return "blocked"
    if tool_name in APPROVAL_REQUIRED:
        return "needs_approval"
    if tool_name in ALLOWED_TOOLS:
        return "allowed"
    return "blocked"  # Default deny
```

### Command Pattern Matching

For agents that execute shell commands, block dangerous patterns:

```python
BLOCKED_COMMANDS = [
    r"rm\s+-rf\s+/",
    r"git\s+push\s+--force",
    r"DROP\s+TABLE",
    r"curl.*\|\s*(?:bash|sh)",
]

def check_command(cmd: str) -> bool:
    for pattern in BLOCKED_COMMANDS:
        if re.search(pattern, cmd, re.IGNORECASE):
            return False
    return True
```

### Cost Caps

```python
class CostTracker:
    def __init__(self, max_cost_usd: float = 5.0):
        self.total_cost = 0.0
        self.max_cost = max_cost_usd

    def add(self, input_tokens: int, output_tokens: int, model: str):
        cost = calculate_cost(input_tokens, output_tokens, model)
        self.total_cost += cost
        if self.total_cost > self.max_cost:
            raise CostLimitExceededError(
                f"Session cost ${self.total_cost:.2f} exceeds ${self.max_cost:.2f} limit"
            )
```

## Layer 3: Output Guardrails

### PII Detection

```python
PII_PATTERNS = {
    "email": r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}",
    "phone": r"\b\d{3}[-.]?\d{3}[-.]?\d{4}\b",
    "ssn": r"\b\d{3}-\d{2}-\d{4}\b",
}

def scan_for_pii(text: str) -> list[str]:
    found = []
    for pii_type, pattern in PII_PATTERNS.items():
        if re.search(pattern, text):
            found.append(pii_type)
    return found
```

### Hallucination Checks

For factual outputs, verify claims against known data:

```python
def verify_file_references(output: str, workspace: str) -> list[str]:
    """Check that referenced files actually exist."""
    file_refs = extract_file_paths(output)
    missing = [f for f in file_refs if not os.path.exists(os.path.join(workspace, f))]
    return missing
```

## See Also

- `hub/reliability.md` — Reliability overview
- `research/tools/tool-security.md` — Tool security
- `research/reliability/human-in-the-loop.md` — Human approval patterns
