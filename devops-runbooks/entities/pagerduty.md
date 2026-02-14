---
title: "PagerDuty — Integration, Escalation Policies, Schedules"
tags: [pagerduty, alerting, on-call, escalation, incident-management, entity]
content_type: entity
domain: operations
---

# PagerDuty — Current State

PagerDuty is the incident management and on-call alerting platform used for paging engineers, managing escalation policies, configuring on-call rotation schedules, and coordinating incident response. This entity note covers API integration, CLI commands, Events API v2 for programmatic alerts, and escalation policy best practices for routing critical notifications from Datadog, Prometheus, and CloudWatch.

## Key Concepts

| Concept | Purpose |
|---------|---------|
| **Service** | A monitored system (e.g., "API Gateway", "Payment Service") |
| **Escalation Policy** | Rules for who gets paged and when to escalate |
| **Schedule** | On-call rotation (who is on-call when) |
| **Integration** | Connection from monitoring tool to PagerDuty |
| **Incident** | An active alert requiring human response |

## Common Integrations

```bash
# Datadog → PagerDuty: Use integration key in Datadog notification channel
# Prometheus → PagerDuty: Use Alertmanager with PagerDuty receiver
# CloudWatch → PagerDuty: SNS topic → PagerDuty integration endpoint
# Custom → PagerDuty: Events API v2
```

## Events API v2 (Triggering Alerts Programmatically)

```bash
curl -X POST https://events.pagerduty.com/v2/enqueue \
  -H "Content-Type: application/json" \
  -d '{
    "routing_key": "YOUR_INTEGRATION_KEY",
    "event_action": "trigger",
    "payload": {
      "summary": "Database connection pool exhausted",
      "severity": "critical",
      "source": "db-primary.example.com",
      "component": "postgresql",
      "group": "database",
      "class": "connection_pool"
    }
  }'
```

## Escalation Policy Best Practices

```
Level 1: On-call engineer (immediate)
Level 2: Engineering lead (after 15 min with no ack)
Level 3: VP Engineering (after 30 min with no ack)
Level 4: CTO (after 45 min with no ack)
```

## CLI Commands

```bash
# List open incidents
pd incident list --statuses triggered,acknowledged

# Acknowledge an incident
pd incident acknowledge --id INCIDENT_ID

# Resolve an incident
pd incident resolve --id INCIDENT_ID

# List on-call schedules
pd schedule list

# Who is currently on call
pd oncall list
```

## Related

- `research/incidents/oncall-rotation.md` — On-call rotation best practices
- `research/incidents/incident-classification.md` — When to page vs. ticket
- `research/monitoring/alert-fatigue.md` — Reducing unnecessary pages

## Last Updated

February 2026
