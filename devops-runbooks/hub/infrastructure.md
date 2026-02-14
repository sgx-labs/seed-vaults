---
title: "Infrastructure — Cloud Resources, Networking, Scaling"
tags: [infrastructure, cloud, networking, scaling, kubernetes, failover, disaster-recovery, hub]
content_type: hub
domain: operations
---

# Infrastructure

## Overview

Infrastructure covers Kubernetes cluster management, AWS cloud resources, networking and DNS, horizontal and vertical scaling, controlled failover, disaster recovery testing, and cloud cost optimization. This hub indexes runbooks for Kubernetes failure modes (CrashLoopBackOff, OOMKilled, ImagePullBackOff), region failover procedures, network troubleshooting, and capacity planning. When infrastructure fails, every service on top of it fails. Proactive scaling, DR drills, and cost management prevent cascading outages and budget overruns.

## Key Research Notes

| Topic | Note |
|-------|------|
| Kubernetes failure modes | `research/infrastructure/kubernetes-failure-modes.md` |
| Controlled failover | `research/infrastructure/controlled-failover.md` |
| Scaling procedures | `research/infrastructure/scaling-procedures.md` |
| Network troubleshooting | `research/infrastructure/network-troubleshooting.md` |
| Cloud cost management | `research/infrastructure/cloud-cost-management.md` |

## Infrastructure Health Checks

Run these during any infrastructure incident:

```bash
# DNS resolution
dig example.com +short

# Network connectivity
curl -o /dev/null -s -w "%{http_code} %{time_total}s\n" https://your-service.example.com/health

# Kubernetes cluster health
kubectl get nodes
kubectl get pods --all-namespaces | grep -v Running

# Cloud provider status
# Check: https://status.aws.amazon.com/
# Check: https://status.cloud.google.com/
# Check: https://status.azure.com/
```

## Capacity Planning Quick Reference

| Resource | Warning Threshold | Critical Threshold | Action |
|----------|------------------|-------------------|--------|
| CPU | >70% sustained | >90% sustained | Scale up or optimize |
| Memory | >75% sustained | >90% sustained | Scale up or find leak |
| Disk | >80% used | >90% used | Expand or clean up |
| Connections | >70% pool | >90% pool | Increase pool or fix leaks |

## Entity References

- `entities/aws.md` — AWS failure modes, support tiers
- `entities/kubernetes.md` — K8s debugging patterns

## See Also

- `hub/runbooks.md` — Operational runbooks that depend on infrastructure
- `hub/monitoring.md` — Monitoring infrastructure health and capacity
- `hub/deployment.md` — Deployments targeting infrastructure (K8s, cloud)
- `hub/incident-response.md` — Incident response for infrastructure failures
