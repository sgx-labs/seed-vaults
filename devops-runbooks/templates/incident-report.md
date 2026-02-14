---
title: "Template — Incident Report"
tags: [template, incident-report, communication]
content_type: template
domain: operations
---

# Incident Report Template

*Copy this template and fill it out during an active incident.*

---

## Incident Summary

- **Incident ID:** INC-YYYY-NNN
- **Severity:** P0 / P1 / P2 / P3
- **Status:** Investigating / Identified / Monitoring / Resolved
- **Started:** YYYY-MM-DD HH:MM UTC
- **Detected:** YYYY-MM-DD HH:MM UTC (how: alert / user report / manual)
- **Resolved:** YYYY-MM-DD HH:MM UTC
- **Duration:** X hours Y minutes
- **Incident Commander:** [Role, not name]

## Impact

- **Users affected:** [Number or percentage]
- **Services affected:** [List services]
- **Revenue impact:** [Estimated, if applicable]
- **Data impact:** [Any data loss or corruption]

## Timeline

| Time (UTC) | Event |
|------------|-------|
| HH:MM | First alert fired |
| HH:MM | On-call engineer acknowledged |
| HH:MM | Root cause identified |
| HH:MM | Fix deployed |
| HH:MM | Service fully recovered |
| HH:MM | Incident declared resolved |

## Root Cause

[One paragraph: what broke and why]

## Resolution

[What was done to fix it — specific commands, config changes, rollbacks]

## Action Items

| Action | Owner | Priority | Deadline |
|--------|-------|----------|----------|
| [Specific action] | [Role] | P0/P1/P2 | YYYY-MM-DD |

## Communication Log

| Time (UTC) | Channel | Message |
|------------|---------|---------|
| HH:MM | Status page | "Investigating increased error rates" |
| HH:MM | Slack #incidents | "Root cause identified, deploying fix" |
| HH:MM | Status page | "Issue resolved, monitoring" |

## Post-Mortem

- [ ] Post-mortem scheduled (within 48 hours)
- [ ] Post-mortem document created (use `templates/postmortem.md`)
- [ ] Action items tracked in issue tracker
