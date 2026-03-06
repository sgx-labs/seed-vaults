---
title: "Retry Strategies with Backoff"
tags: [retry, backoff, exponential, jitter, resilience, idempotency, fault-tolerance]
content_type: research
domain: api-design
---

# Retry Strategies with Backoff

## The Core Idea

Network calls fail. Transient errors (timeouts, 503s, connection resets) often resolve if you try again. But retrying naively -- immediately and repeatedly -- can overwhelm a struggling service. Smart retry strategies use backoff and jitter to spread load.

## Retry Decision Tree

```
Request failed. Should you retry?

1. Is the error retryable?
   - Timeout -> Yes
   - Connection refused -> Yes
   - 503 Service Unavailable -> Yes
   - 429 Too Many Requests -> Yes (after Retry-After)
   - 500 Internal Server Error -> Maybe (depends on operation)
   - 400 Bad Request -> No (client error, retrying won't help)
   - 401/403 -> No (auth issue)
   - 404 -> No

2. Is the operation idempotent?
   - GET, PUT, DELETE -> Safe to retry
   - POST without idempotency key -> NOT safe to retry
   - POST with idempotency key -> Safe to retry

3. Have you exceeded max retries?
   - Yes -> Give up, return error
   - No -> Retry with backoff
```

## Backoff Strategies

### Constant Backoff
Wait the same amount of time between each retry.

```
Attempt 1: fail -> wait 1s
Attempt 2: fail -> wait 1s
Attempt 3: fail -> wait 1s
```

Simple but bad for thundering herd -- all retries hit the service at the same time.

### Exponential Backoff
Double the wait time between each retry.

```
Attempt 1: fail -> wait 1s
Attempt 2: fail -> wait 2s
Attempt 3: fail -> wait 4s
Attempt 4: fail -> wait 8s
Attempt 5: fail -> wait 16s
```

Better but still synchronizes retries from different clients.

### Exponential Backoff with Jitter
Add randomness to prevent synchronized retries.

```
function backoffWithJitter(attempt, baseDelay, maxDelay):
  exponentialDelay = baseDelay * (2 ^ attempt)
  cappedDelay = min(exponentialDelay, maxDelay)
  jitteredDelay = random(0, cappedDelay)  // full jitter
  return jitteredDelay
```

**Full jitter**: `random(0, cappedDelay)` -- most spread
**Equal jitter**: `cappedDelay/2 + random(0, cappedDelay/2)` -- guaranteed minimum wait
**Decorrelated jitter**: `min(maxDelay, random(baseDelay, previousDelay * 3))` -- each delay independent

AWS recommends full jitter. It provides the best distribution and fastest aggregate recovery.

## Implementation Pattern

```
function retryWithBackoff(operation, config):
  maxRetries = config.maxRetries or 3
  baseDelay = config.baseDelay or 1000ms
  maxDelay = config.maxDelay or 30000ms

  for attempt in 0..maxRetries:
    try:
      return operation()
    catch error:
      if not isRetryable(error):
        throw error
      if attempt == maxRetries:
        throw error

      delay = min(baseDelay * (2 ^ attempt), maxDelay)
      jitteredDelay = random(0, delay)
      sleep(jitteredDelay)
```

## Retry Budgets

Instead of per-request retry limits, set a global retry budget: "no more than 10% of requests should be retries."

```
function shouldRetry():
  totalRequests = recentRequests(last60seconds)
  retryRequests = recentRetries(last60seconds)

  if retryRequests / totalRequests > 0.10:
    return false  // retry budget exhausted
  return true
```

This prevents retry storms when a service is failing -- exactly when retries are most dangerous.

## Retry-After Header

When the server tells you when to retry, listen:

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30

HTTP/1.1 503 Service Unavailable
Retry-After: Wed, 15 Jan 2025 10:31:00 GMT
```

Always respect `Retry-After`. It takes precedence over your backoff calculation.

## Common Mistakes

- **Retrying non-idempotent operations** -- creates duplicates. Use idempotency keys.
- **No maximum retry limit** -- infinite retries keep failing forever.
- **No jitter** -- all clients retry at the same time, recreating the overload.
- **Retrying client errors** -- 400, 401, 422 won't succeed on retry.
- **Not respecting Retry-After** -- the server knows better than your backoff algorithm.
- **Retrying too aggressively** -- if 10,000 clients all retry 5 times, you turn 10K failed requests into 50K.

## See Also

- `hub/idempotency.md` -- Idempotency makes retries safe
- `research/circuit-breaker.md` -- Circuit breakers stop retries when a service is down
- `hub/error-handling.md` -- Retryable vs non-retryable errors
