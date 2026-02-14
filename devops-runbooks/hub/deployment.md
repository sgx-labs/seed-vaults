---
title: "Deployment — Release Process, Rollback, Feature Flags"
tags: [deployment, release, rollback, feature-flags, CI-CD, canary, blue-green, hub]
content_type: hub
domain: operations
---

# Deployment

## Overview

Deployment covers release management, CI/CD pipelines, rollback procedures, feature flags, and deployment strategies including canary, blue-green, and rolling updates. This hub indexes runbooks for failed deployments, stuck pipelines, database migration safety, and rollback across Kubernetes, AWS ECS, Lambda, and static sites. Deployments are the leading cause of production incidents. A good deployment process limits blast radius, automates rollback when error rates spike, and uses feature flags to decouple code shipping from feature release.

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

## See Also

- `hub/runbooks.md` — Runbooks triggered when deployments fail
- `hub/monitoring.md` — Monitoring that detects deployment-caused regressions
- `hub/incident-response.md` — Incident response for deployment-related outages
- `hub/infrastructure.md` — Infrastructure that deployments target (K8s, cloud)

## Related

- `research/monitoring/slo-sli-guide.md` — Know when a deploy breaks your SLO
- `entities/kubernetes.md` — K8s-specific deployment patterns
