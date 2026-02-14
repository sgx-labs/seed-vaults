---
title: "Decision Log"
tags: [decisions, architecture, log, runbook-standards, alerting-philosophy]
content_type: decision
domain: operations
---

# Decision Log

## AD-001: Runbooks must contain executable commands

**Status:** Accepted
**Date:** 2026-02-14

Every step in a runbook must be a concrete command or specific action. "Investigate the issue" is not a step. "Run `kubectl get pods -n production | grep -v Running` and check the STATUS column" is a step. On-call engineers at 3am cannot interpret vague instructions.

**Alternative considered:** High-level decision trees. Rejected because they require too much interpretation under pressure.

## AD-002: P0-P2 incidents require post-mortems

**Status:** Accepted
**Date:** 2026-02-14

All incidents classified P0, P1, or P2 must have a blameless post-mortem within 48-72 hours. Without post-mortems, incidents repeat. The template in `templates/postmortem.md` is the starting point. Action items must have owners and deadlines.

**Alternative considered:** Post-mortems only for P0. Rejected because P1-P2 incidents contain equally valuable lessons.

## AD-003: Alert on symptoms, not causes

**Status:** Accepted
**Date:** 2026-02-14

Alerts should fire on user-visible symptoms (error rate, latency, availability) rather than causes (CPU usage, disk I/O). Users do not care about your CPU — they care that the page loads. Cause-based alerts generate noise and create alert fatigue.

**Exception:** Disk space alerts are cause-based but necessary because running out of disk is catastrophic and the symptom (total outage) comes too late.

**Alternative considered:** Alert on everything. Rejected because alert fatigue kills on-call effectiveness.

## AD-004: Severity classification uses impact, not root cause

**Status:** Accepted
**Date:** 2026-02-14

Incident severity (P0-P4) is based on user impact and business impact, not on the technical root cause. A trivial bug that takes down the payment flow is P0. A complex infrastructure failure that affects an internal tool is P3. Classify based on who is affected and how badly.

**Alternative considered:** Technical complexity-based severity. Rejected because it does not correlate with business impact.

## AD-005: Blameless post-mortems are non-negotiable

**Status:** Accepted
**Date:** 2026-02-14

Post-mortems must never name individuals as root causes. "The monitoring gap allowed the issue to go undetected" not "Engineer X did not set up monitoring." Blame creates fear. Fear creates silence. Silence creates repeat incidents.

**Alternative considered:** Accountability-focused reviews. Rejected because they reduce psychological safety and suppress honest reporting.

## AD-006: Feature flags for every user-facing change

**Status:** Accepted
**Date:** 2026-02-14

All new user-facing features ship behind feature flags. This allows instant rollback without redeployment, gradual rollouts, and A/B testing. The deploy and the release are separate events.

**Alternative considered:** Deploy-based releases. Rejected because rollback requires a new deploy, which is slow and risky.
