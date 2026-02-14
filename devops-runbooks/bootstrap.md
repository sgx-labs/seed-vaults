---
title: "Bootstrap — Incident Response Quick Start"
tags: [bootstrap, onboarding, session-start, critical, incident-response, emergency, quick-start]
content_type: hub
domain: operations
pinned: true
---

# Bootstrap — Incident Response Quick Start

## Overview

This is the emergency quick-start guide for DevOps incident response. It provides a lookup table mapping symptoms to runbooks, incident classification from P0 through P4, and a directory of all vault resources for runbooks, monitoring, deployment, infrastructure, and post-mortems. Start here when something is broken in production and you need step-by-step commands immediately. For ongoing operational work, use the hub notes indexed below.

## EMERGENCY LOOKUP TABLE

**Something is broken RIGHT NOW? Find your runbook:**

| Symptom | Runbook | Priority |
|---------|---------|----------|
| Site is completely down | `research/runbooks/database-outage.md` | P0 |
| API returning 5xx errors | `research/runbooks/slow-api-debugging.md` | P0-P1 |
| Database not responding | `research/runbooks/database-outage.md` | P0 |
| Deploy went bad, need rollback | `research/deployment/failed-deployment.md` | P1 |
| CPU/memory/disk maxed out | `research/runbooks/high-cpu-memory-disk.md` | P1 |
| DNS not resolving | `research/runbooks/dns-outage.md` | P0 |
| Suspected security breach | `research/incidents/security-incident-response.md` | P0 |
| DDoS attack in progress | `research/incidents/ddos-response.md` | P0 |
| Credentials compromised | `research/incidents/credential-rotation.md` | P0 |
| Data loss or corruption | `research/incidents/data-loss-response.md` | P0 |
| Kubernetes pods crashing | `research/infrastructure/kubernetes-failure-modes.md` | P1 |
| Need to failover | `research/infrastructure/controlled-failover.md` | P1 |
| Pipeline stuck/failing | `research/deployment/failed-deployment.md` | P2 |
| Alerts firing nonstop | `research/monitoring/alert-fatigue.md` | P2 |

## 1. What This Is

A production-ready vault of runbooks, incident response procedures, and post-mortem templates. When something breaks at 3am, search this vault and get step-by-step instructions with actual commands.

## 2. Incident Classification (Quick Reference)

| Level | Criteria | Response Time | Who Gets Paged |
|-------|----------|---------------|----------------|
| **P0** | Total outage, data loss, security breach | Immediate | On-call + engineering lead + management |
| **P1** | Major degradation, >25% users affected | 15 min | On-call + engineering lead |
| **P2** | Partial degradation, workaround exists | 1 hour | On-call engineer |
| **P3** | Minor issue, low user impact | Next business day | Ticket assigned |
| **P4** | Cosmetic, no user impact | Sprint planning | Backlog |

Full classification guide: `research/incidents/incident-classification.md`

## 3. Where To Find Things

| Need | Search for | Or read directly |
|------|-----------|-----------------|
| Incident procedures | `incident classification escalation` | `hub/incident-response.md` |
| Runbook index | `runbook database DNS CPU` | `hub/runbooks.md` |
| Monitoring setup | `alerting SLO dashboard` | `hub/monitoring.md` |
| Deployment process | `deploy rollback feature flag` | `hub/deployment.md` |
| Infrastructure | `scaling networking cloud` | `hub/infrastructure.md` |
| Post-mortems | `blameless review lessons` | `hub/post-mortems.md` |
| Kubernetes help | `pod crash kubectl debug` | `entities/kubernetes.md` |

## 4. How To Use This Vault

```bash
# Search for a runbook
same search "database is not responding"

# Via MCP during an incident
mcp__same__search_notes(query="rollback failed deployment", top_k=3)

# Find related procedures
mcp__same__find_similar_notes(path="research/runbooks/database-outage.md", top_k=3)
```

## 5. After The Incident

1. Document timeline in `templates/incident-report.md`
2. Schedule post-mortem within 48 hours
3. Use `templates/postmortem.md` template
4. Log action items with owners and deadlines
5. Update runbooks with anything you learned
