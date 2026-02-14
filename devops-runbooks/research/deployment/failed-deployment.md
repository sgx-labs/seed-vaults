---
title: "Failed Deployment and Stuck Pipeline Runbook"
tags: [deployment, failed, pipeline, stuck, CI-CD, rollback, runbook]
content_type: research
domain: operations
---

# Failed Deployment / Stuck Pipeline Runbook

## The Question

What do I do when a deployment fails or a pipeline is stuck?

## Symptoms

- CI/CD pipeline stuck on a step for >15 minutes
- Deployment marked as failed in deployment tool
- New pods not starting (Kubernetes)
- Health checks failing after deploy
- Error rate spiked after deploy

## Severity: P1 (P0 if causing outage)

## Step 1: Assess the situation (2 minutes)

```bash
# Check deployment status (Kubernetes)
kubectl rollout status deployment/api-server -n production --timeout=30s

# Check if new pods are running
kubectl get pods -n production -l app=api-server
kubectl describe pod NEW_POD_NAME -n production | tail -30

# Check for image pull errors
kubectl get events -n production --sort-by='.lastTimestamp' | grep -i "pull\|image\|error" | tail -10

# Check error rate in monitoring
# Look at your APM dashboard for the last 15 minutes
```

## Step 2: Decide — rollback or fix forward

```
Error rate increased after deploy?     → ROLLBACK
New pods not starting?                 → ROLLBACK
Pipeline stuck but old version healthy? → Fix pipeline, no urgency
Config error that's obvious?           → Fix forward (if <5 min fix)

When in doubt: ROLLBACK FIRST, investigate later.
```

## Step 3: Rollback (if needed)

### Kubernetes rollback

```bash
# Rollback to previous version
kubectl rollout undo deployment/api-server -n production

# Verify rollback
kubectl rollout status deployment/api-server -n production

# Check which version is running now
kubectl get deployment/api-server -n production \
  -o jsonpath='{.spec.template.spec.containers[0].image}'

# If you need a specific revision
kubectl rollout history deployment/api-server -n production
kubectl rollout undo deployment/api-server -n production --to-revision=5
```

### Docker/Compose rollback

```bash
# Pull previous image tag
docker pull your-registry.com/api-server:v2.14.2

# Stop current and start previous
docker-compose down
# Update docker-compose.yml with previous tag
docker-compose up -d
```

### Feature flag rollback (no redeploy needed)

```bash
# Disable the feature flag via your feature flag service
# LaunchDarkly example:
curl -X PATCH "https://app.launchdarkly.com/api/v2/flags/production/new-checkout-flow" \
  -H "Authorization: YOUR_LD_API_KEY" \
  -d '[{"op":"replace","path":"/environments/production/on","value":false}]'
```

## Step 4: Fix stuck pipeline

### Common causes and fixes

```bash
# Cause: Stuck test
# Check which test is hanging
# Most CI systems show live logs — find the stuck step

# Cause: Docker build cache corrupted
# CI: Add --no-cache flag
docker build --no-cache -t api-server .

# Cause: Dependency download timeout
# Retry the pipeline — if persistent, check registry availability
npm ping  # npm registry
pip install --index-url https://pypi.org/simple/ --dry-run requests  # PyPI

# Cause: Resource limits on CI runner
# Check if the runner is out of disk space
df -h  # On the CI runner
docker system prune -a -f  # Clean up Docker

# Cause: Approval gate blocking
# Check if a manual approval step is waiting
# Most CI tools: check the pipeline UI for "waiting for approval"
```

## Step 5: Post-deploy verification

After a successful deploy or rollback:

```bash
# Health check
curl -s https://api.example.com/health | jq .

# Error rate check (should be back to baseline)
# Check monitoring dashboard

# Smoke test critical paths
curl -s -o /dev/null -w "%{http_code}" https://api.example.com/v1/status
curl -s -o /dev/null -w "%{http_code}" https://api.example.com/v1/users/me

# Check logs for errors
kubectl logs -n production deploy/api-server --since=5m | grep -i error | head -10
```

## Prevention

1. Deploy during business hours when possible
2. Use canary deploys for high-risk changes
3. Always have a rollback plan before deploying
4. Run smoke tests automatically after deploy
5. Notify on-call before deploying

## Related

- `research/deployment/rollback-procedures.md` — Detailed rollback strategies
- `research/deployment/deployment-strategies.md` — Canary, blue-green, rolling
- `research/deployment/feature-flags.md` — Feature flag management
- `hub/deployment.md` — Deployment overview
