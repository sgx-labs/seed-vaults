---
title: "Incident Communication — Templates and Protocols"
tags: [incident, communication, status-page, stakeholders, templates]
content_type: research
domain: operations
---

# Incident Communication

## The Question

What should my incident communication template look like?

## Communication Timeline

| When | Who | Channel | What |
|------|-----|---------|------|
| 0 min | On-call team | Slack #incidents | "Investigating [symptom]" |
| 5 min | Status page | Public | Initial status update |
| 15 min | Stakeholders | Email/Slack | Impact summary |
| Every 30 min | Status page | Public | Progress update |
| Resolution | All channels | All | Resolution notice |
| +48 hours | Engineering | Internal | Post-mortem shared |

## Status Page Templates

### Investigating

```
[Service Name] — Investigating increased error rates

We are currently investigating reports of [symptom: increased error rates /
degraded performance / service unavailability] affecting [scope: all users /
users in region X / specific feature].

Our engineering team has been alerted and is actively investigating.

We will provide an update within 30 minutes.
```

### Identified

```
[Service Name] — Issue identified

We have identified the cause of the [symptom]. [One sentence explanation
without technical jargon].

Our team is implementing a fix. We expect resolution within [estimated time].

Next update in 30 minutes.
```

### Monitoring

```
[Service Name] — Fix deployed, monitoring

A fix has been deployed for the [symptom] reported earlier. We are actively
monitoring to confirm full recovery.

If you continue to experience issues, please contact support.

Next update in 30 minutes or upon confirmed resolution.
```

### Resolved

```
[Service Name] — Resolved

The issue causing [symptom] has been resolved. Service has been fully
restored as of [time UTC].

Duration: [X hours Y minutes]
Impact: [Brief impact statement]

We will publish a full incident review within [48/72] hours.

We apologize for any inconvenience.
```

## Internal Communication (Slack/Chat)

### Opening an incident channel

```
INCIDENT: [Brief description]
Severity: P[0/1/2]
Impact: [Who is affected]
IC: [Incident Commander role]
Runbook: [Link to relevant runbook]
Status page: [Updated / Not yet]
Bridge: [Video call link if P0/P1]
```

### Periodic updates (every 30 minutes)

```
UPDATE [HH:MM UTC]:
Status: [Investigating / Identified / Mitigating]
What we know: [1-2 sentences]
Current action: [What is being done right now]
Next step: [What happens next]
ETA: [If known]
```

## Stakeholder Communication

### For P0/P1 — Send to leadership within 15 minutes

```
Subject: [P0/P1] [Service Name] incident — [brief description]

Impact: [X% of users affected / revenue impact / specific features down]
Started: [time UTC]
Status: [Investigating / Identified / Mitigating]
ETA: [If known, otherwise "Investigating"]
IC: [Role of incident commander]

Updates will follow every 30 minutes.
```

## Anti-Patterns

1. **Over-promising ETAs.** Say "investigating" not "should be fixed in 10 minutes."
2. **Technical jargon on status page.** Users do not care about your database replication lag.
3. **Radio silence.** No update IS an update (a bad one). Update every 30 minutes even if nothing changed.
4. **Blame in public communication.** Never mention individuals or vendors by name during an active incident.

## Related

- `templates/incident-report.md` — Incident documentation template
- `research/incidents/incident-classification.md` — When to communicate what
- `hub/incident-response.md` — Full incident response overview
