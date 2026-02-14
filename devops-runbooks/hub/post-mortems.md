---
title: "Post-Mortems — Learning From Incidents"
tags: [post-mortem, blameless, lessons-learned, continuous-improvement, hub]
content_type: hub
domain: operations
---

# Post-Mortems

## Overview

Post-mortems are how you turn incidents into improvements. Without them, you will have the same incident again. The goal is not blame — the goal is making your systems and processes better.

## When Is A Post-Mortem Required?

| Severity | Post-Mortem Required? | Timeline |
|----------|----------------------|----------|
| P0 | Yes, mandatory | Draft within 48 hours |
| P1 | Yes, mandatory | Draft within 72 hours |
| P2 | Yes, recommended | Draft within 1 week |
| P3 | Optional | At team discretion |
| P4 | No | N/A |

## Key Research Notes

| Topic | Note |
|-------|------|
| Blameless post-mortem guide | `research/postmortems/blameless-postmortem.md` |
| Writing effective action items | `research/postmortems/effective-action-items.md` |
| Post-mortem facilitation | `research/postmortems/facilitation-guide.md` |
| Incident metrics and tracking | `research/incidents/incident-metrics.md` |

## Blameless Post-Mortem Principles

1. **Assume good intent.** Everyone did the best they could with the information they had.
2. **Focus on systems, not people.** "The deploy pipeline lacked a rollback check" not "Engineer X forgot to check."
3. **Ask "how" not "why."** "How did this change get to production?" not "Why did you push that?"
4. **Action items need owners.** Every follow-up has one person responsible and a deadline.
5. **Share widely.** Post-mortems only work if the whole org learns from them.

## Post-Mortem Meeting Structure (60 minutes)

1. **Timeline review** (15 min) — Walk through what happened, when
2. **Impact assessment** (5 min) — Users affected, revenue impact, duration
3. **Contributing factors** (20 min) — What conditions led to the incident
4. **What went well** (5 min) — What prevented worse outcomes
5. **Action items** (15 min) — Concrete follow-ups with owners and deadlines

## Templates

- `templates/postmortem.md` — Full post-mortem template
- `templates/incident-report.md` — Use during the incident itself
