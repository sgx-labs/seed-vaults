---
title: "Incident Metrics — MTTR, MTTD, Frequency"
tags: [metrics, MTTR, MTTD, MTTF, incident-tracking, SRE]
content_type: research
domain: operations
---

# Incident Metrics

## The Question

How do I track incident metrics like MTTR, MTTD, and frequency?

## Core Metrics

### MTTD — Mean Time To Detect

Time between the incident starting and someone knowing about it.

```
MTTD = time_alert_fired - time_incident_started
```

**Good:** <5 minutes for P0/P1
**Acceptable:** <15 minutes
**Bad:** >30 minutes (means your monitoring has gaps)

### MTTR — Mean Time To Resolve

Time between detection and full resolution.

```
MTTR = time_resolved - time_alert_fired
```

**Good:** <30 minutes for P0/P1
**Acceptable:** <2 hours
**Bad:** >4 hours (means your runbooks need work)

### MTTF — Mean Time Between Failures

Time between incidents of the same type.

```
MTTF = total_time / number_of_incidents
```

**Trending down?** Your reliability efforts are not working. Root causes are not being addressed.

### MTTA — Mean Time To Acknowledge

Time between alert firing and on-call acknowledging.

```
MTTA = time_acknowledged - time_alert_fired
```

**Good:** <5 minutes
**Bad:** >15 minutes (check on-call process, alerting channels)

## How To Calculate

```bash
# Example: tracking in a spreadsheet or database
# For each incident, record:
#   incident_id, severity, started_at, detected_at, acked_at, resolved_at

# MTTD
SELECT AVG(detected_at - started_at) AS avg_mttd
FROM incidents
WHERE severity IN ('P0', 'P1')
AND resolved_at > NOW() - INTERVAL '90 days';

# MTTR
SELECT AVG(resolved_at - detected_at) AS avg_mttr
FROM incidents
WHERE severity IN ('P0', 'P1')
AND resolved_at > NOW() - INTERVAL '90 days';

# Incident frequency by severity
SELECT severity, COUNT(*) AS count,
  COUNT(*) / 3.0 AS per_month
FROM incidents
WHERE resolved_at > NOW() - INTERVAL '90 days'
GROUP BY severity;
```

## Dashboard Layout

Track these metrics on a monthly basis:

| Metric | This Month | Last Month | Trend | Target |
|--------|-----------|------------|-------|--------|
| P0/P1 incidents | | | | <2/month |
| MTTD (P0/P1) | | | | <5 min |
| MTTR (P0/P1) | | | | <1 hour |
| MTTA | | | | <5 min |
| Alert signal-to-noise | | | | >70% |
| On-call pages/week | | | | <5 |
| Post-mortems completed | | | | 100% P0-P2 |

## What Good Looks Like

| Maturity Level | MTTD | MTTR | Post-Mortems | Repeat Incidents |
|----------------|------|------|--------------|-----------------|
| **Starting out** | 30+ min | 4+ hours | Sometimes | Frequent |
| **Developing** | 10-30 min | 1-4 hours | Most P0/P1 | Occasional |
| **Mature** | <5 min | <1 hour | All P0-P2 | Rare |
| **Elite** | <1 min | <15 min | All, with action items tracked | Almost never |

## Common Pitfalls

1. **Measuring MTTR from detection, not from start.** If MTTD is 30 minutes, your MTTR metric hides that.
2. **Averaging across severities.** A P4 with 0 MTTR dilutes your P0 numbers.
3. **Not tracking repeat incidents.** Same root cause = the post-mortem action items were not completed.
4. **Gaming metrics.** Downgrading severity to improve numbers defeats the purpose.

## Related

- `research/incidents/incident-classification.md` — Severity definitions
- `research/postmortems/blameless-postmortem.md` — Learning from incidents
- `research/monitoring/slo-sli-guide.md` — SLO-based metrics
- `hub/post-mortems.md` — Post-mortem overview
