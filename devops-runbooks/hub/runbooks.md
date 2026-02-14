---
title: "Runbooks — Index of All Operational Runbooks"
tags: [runbooks, operations, index, troubleshooting, outage, debugging, hub]
content_type: hub
domain: operations
---

# Runbooks Index

## Overview

This hub indexes all operational runbooks for database outages, DNS failures, high CPU and memory, slow API debugging, SSL/TLS certificate issues, cache failures, message queue backups, and service dependency outages. Each runbook provides step-by-step diagnostic commands, fix procedures, rollback steps, and escalation paths. Runbooks are organized by category: database, compute, networking, deployment, security, and infrastructure. Use this index to find the right procedure during an incident.

## What Is A Runbook?

A runbook is a step-by-step procedure for handling a specific operational scenario. Every step has a concrete command or action. No ambiguity, no "use your judgment" — just do step 1, then step 2, then step 3.

## Runbooks By Category

### Database

| Runbook | When To Use |
|---------|-------------|
| `research/runbooks/database-outage.md` | Database not responding, connection errors, replication lag |
| `research/incidents/data-loss-response.md` | Data corruption, accidental deletion, backup restore |

### Compute and Resources

| Runbook | When To Use |
|---------|-------------|
| `research/runbooks/high-cpu-memory-disk.md` | CPU >90%, OOM kills, disk >85% full |
| `research/runbooks/slow-api-debugging.md` | API latency spikes, p99 degradation, timeout errors |

### Networking

| Runbook | When To Use |
|---------|-------------|
| `research/runbooks/dns-outage.md` | DNS resolution failures, propagation issues |
| `research/incidents/ddos-response.md` | Traffic spike, suspected DDoS attack |

### Deployment

| Runbook | When To Use |
|---------|-------------|
| `research/deployment/failed-deployment.md` | Deploy failed, pipeline stuck, bad release |
| `research/deployment/rollback-procedures.md` | Need to revert to previous version |
| `research/deployment/feature-flags.md` | Feature flag management, kill switches |

### Security

| Runbook | When To Use |
|---------|-------------|
| `research/incidents/security-incident-response.md` | Breach detected, unauthorized access |
| `research/incidents/credential-rotation.md` | Compromised API keys, passwords, certificates |

### Infrastructure

| Runbook | When To Use |
|---------|-------------|
| `research/infrastructure/kubernetes-failure-modes.md` | Pod crashes, node failures, cluster issues |
| `research/infrastructure/controlled-failover.md` | Planned or emergency region/zone failover |
| `research/infrastructure/scaling-procedures.md` | Auto-scaling failures, manual scaling needed |

## Runbook Quality Checklist

Every runbook in this vault follows these rules:

- [ ] Numbered steps with actual commands
- [ ] Expected output for each diagnostic command
- [ ] Clear escalation point if the runbook does not resolve the issue
- [ ] Estimated time to resolution
- [ ] Rollback steps if the fix makes things worse
- [ ] Link to related runbooks

## Template

Use `templates/runbook-template.md` when creating new runbooks.

## See Also

- `hub/incident-response.md` — Incident classification and escalation that triggers runbook use
- `hub/monitoring.md` — Alerting that detects the symptoms runbooks address
- `hub/infrastructure.md` — Infrastructure-level runbooks for Kubernetes and cloud
- `hub/deployment.md` — Deployment failure and rollback runbooks
