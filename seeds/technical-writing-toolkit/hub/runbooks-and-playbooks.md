---
title: "Runbooks and Playbooks"
tags: [runbook, playbook, incident-response, on-call, operations, documentation]
content_type: hub
domain: technical-writing
---

# Runbooks and Playbooks

## The Difference

- **Runbook**: Step-by-step procedure for a specific operational task. "How to restart the payment service."
- **Playbook**: Decision framework for a category of situations. "How to handle a database outage."

Runbooks are prescriptive -- follow the steps exactly. Playbooks are adaptive -- use judgment within a framework.

## Runbook Structure

Every runbook needs these sections:

```markdown
# Restart Payment Service

**When to use:** Payment service is unresponsive or returning 503 errors.
**Who can run this:** On-call engineer (requires prod SSH access).
**Expected duration:** 5-10 minutes.
**Risk level:** Low — service restarts gracefully with no data loss.

## Prerequisites
- SSH access to production jump host
- Access to Grafana dashboard (payment-service-health)

## Steps

1. Check current service status:
   ssh prod-jump 'systemctl status payment-service'

2. Verify the service is actually down (not just slow):
   curl -s https://api.example.com/health | jq .payment

3. Restart the service:
   ssh prod-jump 'sudo systemctl restart payment-service'

4. Wait 30 seconds, then verify recovery:
   curl -s https://api.example.com/health | jq .payment
   # Expected: {"status": "healthy", "latency_ms": < 100}

5. Check Grafana for error rate returning to baseline.

## If It Doesn't Work
- If the service won't start, check logs: journalctl -u payment-service -n 100
- If logs show OOM, escalate to backend team (see Playbook: Resource Exhaustion)
- If logs show database connection errors, see Runbook: Database Connection Reset

## Post-Incident
- Note the restart in #incidents Slack channel
- If this is the third restart this week, file a stability ticket
```

## Playbook Structure

```markdown
# Database Outage Playbook

**Scope:** Primary PostgreSQL database is unreachable or degraded.
**Severity:** SEV-1 if writes are failing, SEV-2 if reads are degraded.

## Assess
1. Is the database process running? (Check monitoring)
2. Is it a connection issue or a database issue?
3. Are replicas affected or just the primary?

## Respond
- **If process is down:** Follow Runbook: Database Restart
- **If connections exhausted:** Follow Runbook: Connection Pool Reset
- **If disk full:** Follow Runbook: Emergency Disk Cleanup
- **If replication lag > 60s:** Notify DBA, consider failover

## Communicate
- Post in #incidents within 5 minutes of detection
- Update every 15 minutes until resolved
- Notify affected teams if write path is down

## Recover
- Verify all services reconnected
- Check for data consistency (run validation queries)
- Schedule post-mortem within 48 hours
```

## Writing Tips

- **Use numbered steps, not paragraphs.** Someone is reading this at 3 AM during an outage.
- **Include exact commands.** Don't say "restart the service." Show the exact command.
- **Show expected output.** So they know if the step worked.
- **Link to next steps.** "If this doesn't work, see X." Never leave someone stuck.
- **Test your runbook.** Have someone unfamiliar follow it. If they get stuck, fix the doc.

## Common Mistakes

- **Assuming knowledge.** The person running this might be a new hire on their first on-call shift.
- **Missing prerequisites.** List required access, tools, and permissions upfront.
- **No "if it doesn't work" section.** The happy path isn't always happy.
- **Stale commands.** Review runbooks quarterly. Infrastructure changes break old commands.

## See Also

- `hub/onboarding-documentation.md` -- Runbooks as part of on-call onboarding
- `hub/error-messages.md` -- Writing error messages that point to runbooks
- `entities/common-documentation-templates.md` -- Runbook template
- `hub/writing-style-guide.md` -- Clarity principles for operational docs
