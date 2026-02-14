---
title: "Rollback Procedures"
tags: [rollback, deployment, recovery, revert, runbook, kubernetes, ECS, Lambda]
content_type: research
domain: operations
---

# Rollback Procedures

## The Question

How do I roll back a bad deployment quickly and safely?

## Rule: If you cannot roll back in under 5 minutes, your deployment process is broken.

## Decision: When to rollback

| Condition | Action |
|-----------|--------|
| Error rate doubled | Rollback immediately |
| p99 latency >3x baseline | Rollback immediately |
| New pods crash-looping | Rollback immediately |
| Users reporting broken feature | Disable feature flag first, then investigate |
| Small issue, fix is obvious | Fix forward (if <5 minutes) |

## Rollback by Platform

### Kubernetes

```bash
# View rollout history
kubectl rollout history deployment/SERVICE -n production

# Rollback to previous version
kubectl rollout undo deployment/SERVICE -n production

# Rollback to specific revision
kubectl rollout undo deployment/SERVICE -n production --to-revision=N

# Verify rollback completed
kubectl rollout status deployment/SERVICE -n production

# Confirm correct image is running
kubectl get pods -n production -l app=SERVICE \
  -o jsonpath='{.items[0].spec.containers[0].image}'
```

### AWS ECS

```bash
# List recent task definitions
aws ecs list-task-definitions --family-prefix SERVICE --sort DESC --max-items 5

# Update service to previous task definition
aws ecs update-service --cluster production \
  --service SERVICE \
  --task-definition SERVICE:PREVIOUS_REVISION \
  --force-new-deployment

# Wait for deployment to stabilize
aws ecs wait services-stable --cluster production --services SERVICE
```

### AWS Lambda

```bash
# List function versions
aws lambda list-versions-by-function --function-name my-function --max-items 5

# Update alias to previous version
aws lambda update-alias --function-name my-function \
  --name production --function-version PREVIOUS_VERSION
```

### Static Sites (S3/CloudFront)

```bash
# Re-deploy previous build artifact
aws s3 sync s3://backup-bucket/previous-build/ s3://production-bucket/ --delete

# Invalidate CloudFront cache
aws cloudfront create-invalidation --distribution-id DIST_ID --paths "/*"
```

### Database Migrations

Database rollbacks are the hardest part. Plan for them BEFORE deploying.

```bash
# If using a migration tool with rollback support:
# Rails
rake db:rollback STEP=1

# Django
python manage.py migrate app_name PREVIOUS_MIGRATION

# Flyway
flyway undo

# If no rollback migration exists:
# Apply a forward migration that reverses the change
# NEVER manually edit production databases without a script
```

## Post-Rollback Checklist

1. [ ] Verify service is healthy (health check endpoint)
2. [ ] Verify error rates returned to baseline
3. [ ] Verify correct version is running
4. [ ] Notify team that rollback occurred
5. [ ] Create a ticket to investigate the failed deploy
6. [ ] Update incident report if this was an active incident
7. [ ] Do NOT re-deploy the failed version without fixing the root cause

## Related

- `research/deployment/failed-deployment.md` — Full failed deployment runbook
- `research/deployment/deployment-strategies.md` — Safer deployment patterns
- `research/deployment/feature-flags.md` — Rollback without redeployment
- `hub/deployment.md` — Deployment overview
