# DevOps Runbooks Seed

## Metadata
- **Name**: devops-runbooks
- **Type**: Hybrid (starter runbooks + templates to customize)
- **Audience**: SREs, platform engineers, on-call developers, DevOps teams
- **Note count target**: 50-70
- **Agent build time**: 3-4 hours

## Purpose
Production-ready runbooks and incident response procedures. When something breaks at 3am, your agent has the playbook. Covers incident classification, escalation, common failure modes, post-mortems, and monitoring setup.

## Hub Categories

1. hub/incident-response.md — Classification, escalation, communication
2. hub/runbooks.md — Index of all runbooks by category
3. hub/monitoring.md — Alerting strategy, dashboards, SLOs/SLIs
4. hub/deployment.md — Release process, rollback procedures, feature flags
5. hub/infrastructure.md — Cloud resources, networking, scaling
6. hub/post-mortems.md — Learning from incidents, blameless culture

## Key Questions

1. How do I classify incidents (P0-P4) and who gets paged?
2. What's the runbook for a database outage?
3. How do I roll back a bad deployment?
4. What are the steps for responding to a security incident?
5. How do I set up effective alerting without alert fatigue?
6. What SLOs should I define for my service?
7. How do I write a blameless post-mortem?
8. What's the runbook for high CPU/memory/disk?
9. How do I handle a DDoS attack?
10. What's the procedure for rotating compromised credentials?
11. How do I debug a slow API endpoint in production?
12. What's the runbook for a failed deploy/stuck pipeline?
13. How do I manage on-call rotations without burnout?
14. What should my incident communication template look like?
15. How do I set up synthetic monitoring?
16. What's the runbook for a DNS outage?
17. How do I handle data loss or corruption?
18. What are the common Kubernetes failure modes and fixes?
19. How do I do a controlled failover?
20. How do I track incident metrics (MTTR, MTTD, frequency)?

## Entities

1. entities/pagerduty.md — Integration, escalation policies, schedules
2. entities/datadog.md — Monitoring setup, dashboards, APM
3. entities/aws.md — Common AWS failure modes, status page, support tiers
4. entities/kubernetes.md — K8s debugging, common issues, kubectl patterns

## CLAUDE.md Governance

- Runbooks must be actionable — every step is a concrete command or action
- During an incident, surface the relevant runbook immediately
- Post-mortems are required for all P0-P2 incidents
- Entity files track current infrastructure state — update after changes
- Templates in templates/ are starting points — customize for your stack

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "templates"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1600
  max_results = 5
  composite_threshold = 0.60
```
