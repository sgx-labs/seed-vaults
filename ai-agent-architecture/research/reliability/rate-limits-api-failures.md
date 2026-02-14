---
title: "How Do I Handle Rate Limits and API Failures Gracefully?"
tags: [rate-limits, API, failures, retry, backoff, resilience]
content_type: research
domain: engineering
---

# How Do I Handle Rate Limits and API Failures Gracefully?

## The Question

How do you build agents that stay reliable when LLM APIs throttle or go down?

## Rate Limit Landscape

Every LLM provider has rate limits. They apply at multiple levels:

| Limit type | What it restricts | Typical values |
|-----------|-------------------|---------------|
| RPM | Requests per minute | 50-500 |
| TPM | Tokens per minute | 40K-1M |
| RPD | Requests per day | 500-10,000 |
| Concurrent | Simultaneous requests | 5-50 |

Rate limits vary by model, tier, and provider. Higher tiers get higher limits.

## Pattern 1: Exponential Backoff with Jitter

```python
import random
import time

def retry_with_backoff(fn, max_retries=5, base_delay=1.0):
    for attempt in range(max_retries):
        try:
            return fn()
        except RateLimitError as e:
            if attempt == max_retries - 1:
                raise

            # Check for Retry-After header
            retry_after = getattr(e, 'retry_after', None)
            if retry_after:
                delay = retry_after
            else:
                delay = base_delay * (2 ** attempt)

            # Add jitter to prevent thundering herd
            jitter = random.uniform(0, delay * 0.1)
            time.sleep(delay + jitter)
```

## Pattern 2: Token Bucket Rate Limiter

Pre-emptively throttle requests instead of waiting for 429 errors:

```python
import time
from threading import Lock

class TokenBucket:
    def __init__(self, rate: float, capacity: int):
        self.rate = rate          # tokens per second
        self.capacity = capacity  # max burst
        self.tokens = capacity
        self.last_refill = time.time()
        self.lock = Lock()

    def acquire(self, tokens: int = 1):
        with self.lock:
            now = time.time()
            elapsed = now - self.last_refill
            self.tokens = min(self.capacity, self.tokens + elapsed * self.rate)
            self.last_refill = now

            if self.tokens >= tokens:
                self.tokens -= tokens
                return True
            return False

# Usage
limiter = TokenBucket(rate=10, capacity=50)  # 10 req/s, burst of 50
while not limiter.acquire():
    time.sleep(0.1)
```

## Pattern 3: Multi-Provider Failover

If one provider is rate-limited, try another:

```python
PROVIDERS = [
    {"base_url": "https://api.anthropic.com", "model": "claude-sonnet-4-5-20250929"},
    {"base_url": "https://openrouter.ai/api/v1", "model": "anthropic/claude-sonnet-4-5-20250929"},
    {"base_url": "https://api.openai.com", "model": "gpt-4.1"},
]

def call_with_failover(messages, tools):
    for provider in PROVIDERS:
        try:
            return call_provider(provider, messages, tools)
        except (RateLimitError, ServerError):
            continue
    raise AllProvidersUnavailableError()
```

## What NOT to Do

- **Retry immediately** — Hammers the API, extends rate limiting
- **Ignore 429 responses** — Gets you temporarily blocked
- **Retry indefinitely** — Agent hangs forever
- **Retry on 400 errors** — Bad request will never succeed
- **Hide failures from the user** — Silent retries that burn tokens

## See Also

- `research/reliability/error-handling-patterns.md` — Error handling overview
- `entities/openrouter.md` — Multi-provider routing
- `hub/reliability.md` — Reliability overview
