---
title: "CI/CD Pipeline Troubleshooting"
tags: [CI-CD, pipeline, build, troubleshooting, GitHub-Actions, GitLab, stuck, failed]
content_type: research
domain: operations
---

# CI/CD Pipeline Troubleshooting

## The Question

What do I do when the CI/CD pipeline fails or gets stuck?

## Common Failure Categories

### 1. Build failures

```bash
# Dependency resolution failed
# Check: Is the package registry accessible?
npm ping                    # npm
pip install --dry-run flask  # PyPI
mvn dependency:resolve      # Maven

# Docker build failed
# Check: Is the base image available?
docker pull node:20-alpine

# Out of disk on build runner
df -h
docker system prune -a -f

# Fix: Clear build cache
# GitHub Actions:
#   Delete cache from Settings > Actions > Cache management
# GitLab CI:
#   Clear runner cache: gitlab-runner cache-clear
```

### 2. Test failures

```bash
# Flaky tests (pass sometimes, fail others)
# Strategy: Re-run failed tests only
# GitHub Actions: Use the "Re-run failed jobs" button
# CLI: Run the specific failing test locally
pytest tests/test_payment.py::test_checkout -v

# Test timeout
# Check: Is the test waiting on an external service?
# Fix: Mock external dependencies in tests
# Fix: Increase test timeout if the test is legitimately slow

# Test environment issue
# Check: Are test databases/services running?
docker-compose -f docker-compose.test.yml up -d
```

### 3. Deployment step failures

```bash
# Authentication failure
# Check: Are deployment credentials still valid?
aws sts get-caller-identity       # AWS
gcloud auth list                  # GCP
kubectl cluster-info              # Kubernetes

# Permission denied
# Check: Does the deployment role have required permissions?
aws iam simulate-principal-policy --policy-source-arn ROLE_ARN \
  --action-names ecs:UpdateService

# Kubernetes deployment failed
kubectl rollout status deployment/SERVICE -n production --timeout=120s
kubectl describe deployment SERVICE -n production | tail -20
kubectl get events -n production --sort-by='.lastTimestamp' | tail -10
```

### 4. Pipeline stuck (no progress)

```bash
# Check if a manual approval is blocking
# Most CI tools show this in the pipeline UI

# Check if the runner is overloaded
# GitHub Actions: Check runner queue at Settings > Actions > Runners
# Self-hosted: Check runner health
gitlab-runner status
ps aux | grep runner

# Check for resource locks
# If pipeline uses terraform/infrastructure locks:
terraform force-unlock LOCK_ID

# Kill and retry
# Cancel the stuck pipeline and re-run from the failed step
```

## Emergency: Need to deploy but pipeline is broken

```bash
# Option 1: Deploy manually (last resort)
docker build -t your-registry.com/api-server:emergency .
docker push your-registry.com/api-server:emergency
kubectl set image deployment/api-server api-server=your-registry.com/api-server:emergency -n production

# Option 2: Deploy previous known-good version
kubectl rollout undo deployment/api-server -n production

# ALWAYS: File a ticket to fix the pipeline after the emergency
```

## Prevention

1. Keep build times under 10 minutes
2. Use build caching (Docker layer cache, npm cache, pip cache)
3. Pin dependency versions to avoid surprise breakages
4. Run the same tests locally before pushing
5. Monitor pipeline duration and failure rate as metrics

## Related

- `research/deployment/failed-deployment.md` — Full deployment failure runbook
- `research/deployment/rollback-procedures.md` — Rollback strategies
- `hub/deployment.md` — Deployment overview
