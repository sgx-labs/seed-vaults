---
title: "Third-Party Service Dependency Outage Runbook"
tags: [dependency, third-party, outage, circuit-breaker, graceful-degradation, runbook]
content_type: research
domain: operations
---

# Third-Party Service Dependency Outage

## The Question

What do I do when a service my application depends on goes down?

## Symptoms

- Timeout errors in logs for a specific downstream service
- Application partially broken (feature dependent on the third-party)
- Increased latency because requests wait for timeouts
- Third-party status page showing degraded service

## Severity: P1 (P0 if the dependency blocks core functionality)

## Step 1: Confirm the dependency is down (2 minutes)

```bash
# Check the third-party status page
# Stripe: https://status.stripe.com
# Twilio: https://status.twilio.com
# SendGrid: https://status.sendgrid.com
# GitHub: https://www.githubstatus.com
# AWS: https://health.aws.amazon.com

# Test connectivity directly
curl -o /dev/null -s -w "HTTP %{http_code} in %{time_total}s\n" \
  https://api.thirdparty.com/health

# Check your application logs for the specific error
kubectl logs -n production deploy/api-server --since=10m | \
  grep -i "thirdparty\|timeout\|connection refused" | tail -10
```

## Step 2: Activate circuit breaker / graceful degradation

```bash
# If you have feature flags for dependencies:
# Disable the feature that depends on the third-party
kubectl set env deployment/api-server -n production \
  FEATURE_THIRD_PARTY_INTEGRATION=false
kubectl rollout restart deployment/api-server -n production

# If you have a circuit breaker pattern:
# The circuit breaker should trip automatically after N failures
# Verify it tripped:
kubectl logs -n production deploy/api-server --since=5m | \
  grep "circuit.*open\|breaker.*trip"
```

## Step 3: Reduce blast radius

```bash
# Reduce timeout values to prevent request queuing
# If configurable via environment:
kubectl set env deployment/api-server -n production \
  THIRD_PARTY_TIMEOUT_MS=2000  # Short timeout, fail fast

# Scale up to handle increased latency from retries
kubectl scale deployment/api-server -n production --replicas=8

# If the dependency is for non-critical features:
# Return cached/default data instead of erroring
```

## Step 4: Monitor and wait

```bash
# Set up a check for when the service comes back
watch -n 60 'curl -o /dev/null -s -w "HTTP %{http_code}\n" https://api.thirdparty.com/health'

# Monitor your application error rates
# They should stabilize after circuit breaker activates
```

## Step 5: Recovery

When the third-party service recovers:

```bash
# Re-enable the feature
kubectl set env deployment/api-server -n production \
  FEATURE_THIRD_PARTY_INTEGRATION=true
kubectl rollout restart deployment/api-server -n production

# Process any queued/failed operations
# Check dead letter queue for failed messages
# Retry failed webhook deliveries

# Monitor for 30 minutes to confirm stability
```

## Prevention

1. **Circuit breakers** on all external dependencies
2. **Timeouts** on all outbound HTTP calls (never unbounded)
3. **Retries with exponential backoff** (not infinite retries)
4. **Graceful degradation** — features should degrade, not crash
5. **Cached fallbacks** for data that does not change frequently
6. **Multi-provider strategy** for critical dependencies (e.g., SMS via Twilio + SNS)

## Related

- `research/runbooks/slow-api-debugging.md` — Downstream dependency causing slowness
- `research/monitoring/synthetic-monitoring.md` — Monitor dependency health
- `research/deployment/feature-flags.md` — Feature flags for dependencies
