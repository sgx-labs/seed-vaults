---
title: "Deployment Strategies — Canary, Blue-Green, Rolling"
tags: [deployment, canary, blue-green, rolling, strategy, zero-downtime, kubernetes, traffic-routing]
content_type: research
domain: operations
---

# Deployment Strategies

## The Question

What deployment strategies minimize risk and enable fast rollback?

## Strategy Comparison

| Strategy | Risk | Rollback Speed | Complexity | Infrastructure Cost |
|----------|------|----------------|------------|-------------------|
| **Recreate** | Highest (downtime) | Slow | Lowest | 1x |
| **Rolling** | Medium | Minutes | Low | 1x |
| **Blue-Green** | Low | Seconds | Medium | 2x |
| **Canary** | Lowest | Seconds | High | 1.1x |

## Rolling Update (Kubernetes default)

Replaces pods one at a time. No downtime but both versions run simultaneously during the update.

```yaml
# Kubernetes Deployment spec
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # 1 extra pod during update
      maxUnavailable: 0   # Never have fewer than desired replicas
```

```bash
# Trigger rolling update
kubectl set image deployment/api-server \
  api-server=your-registry.com/api-server:v2.15.0 -n production

# Watch the rollout
kubectl rollout status deployment/api-server -n production

# Rollback
kubectl rollout undo deployment/api-server -n production
```

**Best for:** Stateless services, low-risk changes.

## Blue-Green Deployment

Run two identical environments. Switch traffic from blue (current) to green (new) at the load balancer level.

```bash
# Step 1: Deploy new version to "green" environment
kubectl apply -f deployment-green.yaml -n production

# Step 2: Verify green is healthy
kubectl get pods -n production -l version=green
curl -s http://green-service.production.svc.cluster.local/health

# Step 3: Switch traffic (update service selector)
kubectl patch service api-server -n production \
  -p '{"spec":{"selector":{"version":"green"}}}'

# Step 4: Verify traffic is flowing to green
curl -s https://api.example.com/health

# Rollback: Switch back to blue
kubectl patch service api-server -n production \
  -p '{"spec":{"selector":{"version":"blue"}}}'
```

**Best for:** Critical services where instant rollback is essential.

## Canary Deployment

Route a small percentage of traffic to the new version. If metrics look good, gradually increase.

```bash
# Step 1: Deploy canary (1 replica alongside 9 existing)
kubectl apply -f deployment-canary.yaml -n production
# Canary deployment has 1 replica, stable has 9
# Same service selector matches both

# Step 2: Monitor canary metrics
# Compare error rate and latency between canary and stable
# Datadog: filter by pod label version=canary vs version=stable

# Step 3: If canary looks good, scale up
kubectl scale deployment/api-server-canary -n production --replicas=5
kubectl scale deployment/api-server-stable -n production --replicas=5

# Step 4: Complete rollout
kubectl scale deployment/api-server-canary -n production --replicas=10
kubectl delete deployment/api-server-stable -n production

# Rollback: Delete canary
kubectl delete deployment/api-server-canary -n production
```

**Best for:** High-traffic services where you want data-driven rollout decisions.

## Choosing a Strategy

```
Is it a high-risk change?
  YES → Canary or Blue-Green
  NO  → Rolling Update

Do you need instant rollback (<10 seconds)?
  YES → Blue-Green
  NO  → Rolling Update

Do you want data-driven rollout?
  YES → Canary
  NO  → Blue-Green or Rolling
```

## Related

- `research/deployment/rollback-procedures.md` — Rollback for each strategy
- `research/deployment/feature-flags.md` — Decouple deploy from release
- `research/deployment/failed-deployment.md` — When things go wrong
- `hub/deployment.md` — Deployment overview
