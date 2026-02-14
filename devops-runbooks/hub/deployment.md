---
title: "Deployment — Release Process, Rollback, Feature Flags"
tags: [deployment, release, rollback, feature-flags, CI-CD, hub]
content_type: hub
domain: operations
---

# Deployment

## Overview

Deployment is the most common cause of production incidents. A good deployment process makes rollback easy, limits blast radius, and gives you confidence that every release is safe. If you cannot roll back in under 5 minutes, your deployment process is broken.

## Deployment Checklist (Pre-Deploy)

1. All tests pass in CI
2. Code review approved
3. Staging environment validated
4. Feature flags ready for new features
5. Rollback plan documented
6. On-call engineer aware of the deploy
7. Deploy window is not during peak traffic

## Key Research Notes

| Topic | Note |
|-------|------|
| Failed deployment recovery | `research/deployment/failed-deployment.md` |
| Rollback procedures | `research/deployment/rollback-procedures.md` |
| Feature flag management | `research/deployment/feature-flags.md` |
| CI/CD pipeline troubleshooting | `research/deployment/cicd-troubleshooting.md` |
| Canary and blue-green deploys | `research/deployment/deployment-strategies.md` |

## Deployment Strategies

| Strategy | Risk Level | Rollback Speed | Best For |
|----------|-----------|----------------|----------|
| **Rolling update** | Medium | Minutes | Stateless services |
| **Blue-green** | Low | Seconds (DNS/LB switch) | Critical services |
| **Canary** | Lowest | Seconds (route change) | High-traffic services |
| **Recreate** | Highest | Slow (full redeploy) | Dev/staging only |

## Rollback Decision Tree

```
Deploy went out → Monitor for 15 min
  ├─ Error rate increased? → ROLLBACK NOW
  ├─ Latency p99 doubled? → ROLLBACK NOW
  ├─ Feature not working?  → Disable feature flag, investigate
  └─ All green?            → Continue monitoring for 1 hour
```

## Related

- `research/monitoring/slo-sli-guide.md` — Know when a deploy breaks your SLO
- `entities/kubernetes.md` — K8s-specific deployment patterns
