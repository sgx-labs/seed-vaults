---
title: "Security Incident Response — The First 30 Minutes"
tags: [infrastructure, incident-response, breach, forensics, containment, vulnerability-report]
content_type: research
domain: security
---

# Security Incident Response — The First 30 Minutes

The first 30 minutes of a security incident determine the outcome. Having a plan before the incident matters more than what you do during it.

## Incident Severity Levels

| Level | Examples | Response Time |
|-------|---------|---------------|
| P1 — Critical | Active data breach, RCE in production, ransomware | Immediate (drop everything) |
| P2 — High | Credential leak, privilege escalation, malware detected | Within 1 hour |
| P3 — Medium | Vulnerability discovered, suspicious activity | Within 24 hours |
| P4 — Low | Security misconfiguration, policy violation | Within 1 week |

## The First 30 Minutes (P1/P2)

### 1. Confirm and Contain (0-10 min)

- **Confirm** the incident is real (not a false positive)
- **Contain** the blast radius immediately:
  - Revoke compromised credentials
  - Block attacker IP addresses
  - Isolate affected systems (do NOT turn them off — preserve evidence)
  - Disable compromised user accounts
- **Do NOT** start fixing or patching yet

### 2. Communicate (10-15 min)

- Alert the incident response team
- Designate an incident commander
- Open a dedicated communication channel (Slack channel, war room)
- Notify management if data breach is likely
- **Do NOT** communicate on potentially compromised channels

### 3. Investigate (15-30 min)

- Preserve logs and evidence (snapshot VMs, export logs)
- Determine scope: What was accessed? What was modified?
- Identify the attack vector: How did they get in?
- Check for persistence mechanisms (backdoors, new accounts)
- Timeline the attack (when did it start?)

## After the First 30 Minutes

### Eradicate (1-4 hours)
- Remove attacker access completely
- Patch the vulnerability that was exploited
- Rotate ALL potentially compromised credentials
- Verify no persistence mechanisms remain

### Recover (4-24 hours)
- Restore from clean backups if needed
- Gradually restore services
- Monitor closely for re-compromise
- Verify data integrity

### Post-Incident (24-72 hours)
- Write incident report (timeline, impact, response)
- Conduct blameless post-mortem
- Identify improvements to prevent recurrence
- Update runbooks and monitoring
- Notify affected users if required (GDPR: 72 hours, varies by regulation)

## Vulnerability Report Response

When someone reports a vulnerability to you:

1. **Acknowledge** within 24 hours (ideally same day)
2. **Validate** the report — reproduce the issue
3. **Communicate** a timeline for the fix
4. **Fix** the vulnerability
5. **Credit** the reporter (if they want)
6. **Disclose** responsibly (after the fix is deployed)

### Template Response

```
Thank you for reporting this security issue. We take all reports
seriously and will investigate immediately.

We will provide an update within [48 hours] on our assessment
and planned remediation timeline.

If you have additional information, please send it to
security@example.com.
```

## Preparation Checklist

- [ ] Incident response plan documented and accessible
- [ ] Contact list for incident response team (phone, not just Slack)
- [ ] Runbooks for common scenarios (credential leak, data breach, ransomware)
- [ ] Logging and monitoring in place BEFORE the incident
- [ ] Regular incident response drills (tabletop exercises)
- [ ] Legal counsel identified for data breach notification
- [ ] Communication templates pre-written

## See Also

- hub/infrastructure.md
- hub/compliance.md — Breach notification requirements
- research/owasp/logging-monitoring-failures.md
- research/infrastructure/sensitive-data-logs.md
