---
title: "Incident Response — Classification, Escalation, Communication"
tags: [incident-response, classification, escalation, communication, paging, severity, P0, hub]
content_type: hub
domain: operations
---

# Incident Response

## Overview

Incident response covers the full lifecycle of production incidents: detection, classification, escalation, communication, resolution, and post-mortem review. This hub indexes runbooks for security breaches, DDoS attacks, credential rotation, data loss, and on-call procedures. It defines severity levels P0 through P4, escalation paths, war room protocols, and incident communication templates. A well-practiced incident response process reduces MTTR, prevents repeat outages, and protects users during critical failures.

## Incident Lifecycle

```
Detect → Classify → Respond → Communicate → Resolve → Post-Mortem
```

1. **Detect** — Monitoring fires an alert (or a user reports it)
2. **Classify** — Assign severity P0-P4 based on impact
3. **Respond** — Follow the runbook, assign incident commander
4. **Communicate** — Status page, stakeholders, customers
5. **Resolve** — Fix the issue, verify recovery
6. **Post-Mortem** — Document what happened and prevent recurrence

## Key Research Notes

| Topic | Note |
|-------|------|
| Severity classification (P0-P4) | `research/incidents/incident-classification.md` |
| Security incident response | `research/incidents/security-incident-response.md` |
| DDoS attack response | `research/incidents/ddos-response.md` |
| Credential rotation | `research/incidents/credential-rotation.md` |
| Data loss response | `research/incidents/data-loss-response.md` |
| Incident communication | `research/incidents/incident-communication.md` |
| On-call rotation management | `research/incidents/oncall-rotation.md` |
| Incident metrics (MTTR, MTTD) | `research/incidents/incident-metrics.md` |

## Quick Reference: Who To Page

| Severity | First Responder | Escalation | Timeline |
|----------|----------------|------------|----------|
| P0 | On-call engineer | Eng lead + VP Eng + Comms | Immediately |
| P1 | On-call engineer | Eng lead | 15 min if no progress |
| P2 | On-call engineer | Team lead | 1 hour if stuck |
| P3 | Assigned engineer | None | Next business day |
| P4 | Backlog | None | Sprint planning |

## Templates

- `templates/incident-report.md` — Use during active incidents
- `templates/postmortem.md` — Use within 48 hours after resolution

## See Also

- `hub/runbooks.md` — Index of all operational runbooks referenced during incidents
- `hub/monitoring.md` — Alerting and detection that triggers incident response
- `hub/post-mortems.md` — Learning from incidents after resolution
- `hub/deployment.md` — Deployment failures are the leading cause of incidents
