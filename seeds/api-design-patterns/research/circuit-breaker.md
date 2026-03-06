---
title: "Circuit Breaker Pattern"
tags: [circuit-breaker, resilience, fault-tolerance, microservices, cascading-failure]
content_type: research
domain: api-design
---

# Circuit Breaker Pattern

## The Core Idea

When a downstream service is failing, continuing to send requests makes everything worse. The circuit breaker pattern stops calling a failing service, gives it time to recover, and gradually resumes traffic. Named after electrical circuit breakers that prevent overload.

## States

```
CLOSED (normal operation)
  -> requests pass through to the service
  -> failures are counted
  -> if failure count exceeds threshold -> switch to OPEN

OPEN (service is down, stop calling it)
  -> all requests fail immediately (no network call)
  -> return fallback response or error
  -> after timeout period -> switch to HALF-OPEN

HALF-OPEN (testing if service recovered)
  -> allow a limited number of test requests through
  -> if test requests succeed -> switch to CLOSED
  -> if test requests fail -> switch back to OPEN
```

## Implementation

```
class CircuitBreaker:
  state = CLOSED
  failureCount = 0
  failureThreshold = 5
  resetTimeout = 30 seconds
  lastFailureTime = null

  function call(request):
    if state == OPEN:
      if (now - lastFailureTime) > resetTimeout:
        state = HALF_OPEN
      else:
        return fallbackResponse()  // fail fast

    try:
      response = makeRequest(request)
      if state == HALF_OPEN:
        state = CLOSED
        failureCount = 0
      return response

    catch error:
      failureCount += 1
      lastFailureTime = now
      if failureCount >= failureThreshold:
        state = OPEN
      throw error
```

## Configuration

| Parameter | Typical Value | Purpose |
|-----------|--------------|---------|
| Failure threshold | 5-10 | Failures before opening |
| Reset timeout | 15-60 seconds | How long to wait before testing |
| Half-open max requests | 1-3 | Test requests in half-open state |
| Failure window | 60 seconds | Time window for counting failures |
| Success threshold | 2-3 | Successes needed to close from half-open |

## What Counts as a Failure?

Define carefully. Not every error should trip the breaker.

**Should trip**: Connection refused, timeout, 503 Service Unavailable, 502 Bad Gateway.

**Should NOT trip**: 400 Bad Request (client error, not service failure), 401/403 (auth issues), 404 Not Found, 422 Validation Error.

```
function isCircuitBreakerFailure(response):
  if response is timeout -> true
  if response is connection error -> true
  if response.status >= 500 -> true
  return false
```

## Fallback Strategies

When the circuit is open, what do you return?

| Strategy | Example | When to Use |
|----------|---------|-------------|
| Cached data | Return last known good response | Read-heavy endpoints |
| Default value | Return empty list or default config | Non-critical data |
| Degraded response | Return partial data without the failing service | Aggregation endpoints |
| Queue for later | Accept the request, process when service recovers | Write operations |
| Error with retry hint | 503 + Retry-After header | When no fallback is possible |

## Per-Service vs Per-Endpoint

**Per-service**: One breaker for all endpoints on a service. Simple but imprecise -- a slow `/search` endpoint trips the breaker for fast `/health` checks.

**Per-endpoint**: Separate breaker per endpoint. More precise but more state to manage.

**Recommendation**: Start per-service. Move to per-endpoint if you see false positives.

## Common Mistakes

- **Threshold too low** -- a single transient error opens the circuit. Use a failure window, not just a count.
- **Timeout too long** -- service recovers in 10 seconds but circuit stays open for 5 minutes.
- **No fallback** -- circuit opens and every request gets a generic 500.
- **Not monitoring state transitions** -- you should alert when a circuit opens.
- **Applying to the database** -- circuit breakers are for remote services. Use connection pools and retries for databases.

## When to Use

- Microservices calling other microservices
- API gateway calling backends
- Any HTTP client calling an external service

## When NOT to Use

- Database connections (use connection pools)
- In-process function calls
- Services that fail for client-side reasons (4xx errors)

## See Also

- `research/retry-strategies.md` -- Retries work with circuit breakers
- `hub/api-gateway.md` -- Gateways often implement circuit breakers
- `research/microservices-communication.md` -- Broader resilience patterns
