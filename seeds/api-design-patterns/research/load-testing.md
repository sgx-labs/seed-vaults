---
title: "Load Testing Strategies"
tags: [load-testing, performance, stress-test, benchmarking, capacity, latency]
content_type: research
domain: api-design
---

# Load Testing Strategies

## The Core Idea

Load testing answers the question: "How does my API behave under realistic and extreme traffic?" Without it, you discover performance limits in production -- the worst possible time. Test early, test often, test with realistic scenarios.

## Types of Load Tests

### Smoke Test
Minimal load to verify the API works under test conditions.
```
Users: 1-5
Duration: 1-2 minutes
Purpose: Validate test setup, catch config errors
```

### Load Test
Expected production traffic to verify acceptable performance.
```
Users: Expected concurrent users (e.g., 100)
Duration: 10-30 minutes
Purpose: Verify SLA compliance, baseline metrics
```

### Stress Test
Gradually increase load beyond expected levels to find the breaking point.
```
Users: Ramp from 100 to 1000 over 10 minutes
Duration: 15-30 minutes
Purpose: Find max capacity, identify bottlenecks
```

### Spike Test
Sudden traffic burst followed by return to normal.
```
Users: 50 -> 500 (instant) -> 50
Duration: 10 minutes
Purpose: Test auto-scaling, queue behavior, error handling under burst
```

### Soak Test
Sustained load over a long period to detect memory leaks and degradation.
```
Users: Normal load (e.g., 100)
Duration: 4-24 hours
Purpose: Find memory leaks, connection pool exhaustion, log rotation issues
```

## What to Measure

| Metric | Target | Red Flag |
|--------|--------|----------|
| Response time (p50) | < 200ms | > 500ms |
| Response time (p99) | < 1s | > 3s |
| Error rate | < 0.1% | > 1% |
| Throughput (RPS) | Meets requirements | Declining under load |
| CPU usage | < 70% at expected load | > 90% |
| Memory usage | Stable | Growing over time (leak) |
| Database connections | < 80% of pool | Pool exhausted |

## Test Design

### Realistic Scenarios

Don't just hammer one endpoint. Model real usage patterns:

```
Scenario: E-commerce API
  70% - Browse products (GET /products, GET /products/:id)
  15% - Search (GET /products?search=...)
  10% - Cart operations (POST /cart, PATCH /cart)
  4%  - Checkout (POST /orders)
  1%  - Account management (GET/PATCH /users/me)
```

### Test Data
- Use realistic data sizes (not empty databases)
- Pre-populate enough data to match production volumes
- Use unique test data to avoid cache inflation
- Clean up after tests (or use isolated environments)

### Authentication
- Pre-generate auth tokens (don't load test your login endpoint accidentally)
- Use a pool of test users to avoid per-user rate limit issues

## Tools

| Tool | Language | Best For |
|------|----------|----------|
| k6 | JavaScript | Scriptable, CI-friendly, modern |
| Locust | Python | Distributed, easy scenarios |
| Artillery | JavaScript | API-focused, YAML config |
| wrk | C | Raw HTTP benchmarking, simple |
| Gatling | Scala | Enterprise, detailed reports |
| Apache Bench (ab) | C | Quick one-liner tests |

## Running in CI/CD

```
1. Deploy to staging
2. Run smoke test (fail fast if broken)
3. Run load test with production-like traffic
4. Compare metrics against thresholds
5. Fail the pipeline if metrics degrade
```

Set thresholds:
```
p95 response time < 500ms
Error rate < 0.5%
Throughput > 200 RPS
```

## Common Mistakes

- **Testing from the same machine as the server** -- network is not a bottleneck, results are misleading.
- **Not warming up** -- cold starts, empty caches, and JIT compilation skew early results.
- **Testing one endpoint** -- real traffic is mixed. Test realistic scenarios.
- **Ignoring database state** -- an empty database is fast. A database with 10M rows is not.
- **No baseline** -- you can't detect regression without a baseline to compare against.
- **Testing in production** -- test in staging with production-like data. Never load test production unless you have safeguards.

## See Also

- `research/api-monitoring.md` -- Monitoring the metrics you load tested
- `hub/rate-limiting.md` -- Rate limiting behavior under load
- `hub/caching.md` -- Cache behavior under load
