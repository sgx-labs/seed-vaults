---
title: "SOC 2 Compliance for Startups"
tags: [compliance, soc2, audit, controls, startups]
content_type: research
domain: security
---

# SOC 2 Compliance for Startups

SOC 2 is the compliance framework most B2B SaaS companies need first. It proves to enterprise customers that you take security seriously.

## When to Start

- Enterprise deals ($100K+ ACV) in your pipeline
- $1M-$2M ARR (budget: $20K-$50K for audit)
- Customers asking for your SOC 2 report
- 70% of VCs prefer SOC 2-compliant startups

## Type I vs Type II

| | Type I | Type II |
|---|--------|---------|
| What it proves | Controls EXIST | Controls WORK over time |
| Observation period | Point in time | 3-12 months |
| Time to achieve | 2-4 months | 6-12 months |
| Cost | $15K-$30K | $20K-$50K |
| What customers want | Acceptable for first audit | Required for renewal |

**Strategy:** Get Type I first (faster), then Type II.

## The Five Trust Service Criteria

### 1. Security (Required)

**What auditors check:**
- Access control: Who can access what? How are permissions managed?
- Network security: Firewalls, VPNs, segmentation
- Change management: How do code changes get to production?
- Incident response: Do you have a plan? Has it been tested?
- Monitoring: Can you detect and alert on security events?

**Developer actions:**
- [ ] Implement RBAC with documented access reviews
- [ ] Enable MFA for all production access
- [ ] Set up audit logging for sensitive operations
- [ ] Document the deployment process
- [ ] Create and test an incident response plan

### 2. Availability (Optional)

- Uptime SLAs documented and monitored
- Disaster recovery plan tested
- Backups verified (actually restore them)
- Capacity planning documented

### 3. Processing Integrity (Optional)

- Data processing accuracy verified
- Error handling and correction documented
- Quality assurance processes in place

### 4. Confidentiality (Optional)

- Data classification policy
- Encryption at rest and in transit
- Data retention and disposal procedures
- NDA processes for employees and vendors

### 5. Privacy (Optional)

- Privacy notice published
- Consent mechanisms for data collection
- Data subject rights handled (access, deletion)
- Privacy impact assessments for new features

## Minimum Viable SOC 2 (Developer Focus)

These are the controls that matter most for the Security criterion:

```
1. Access Control
   - SSO/SAML for production systems
   - MFA everywhere
   - Quarterly access reviews
   - Offboarding checklist

2. Change Management
   - All code changes via pull request
   - Peer review required before merge
   - Automated testing in CI
   - Production deployments logged

3. Monitoring
   - Centralized logging (security events)
   - Alert on authentication failures
   - Uptime monitoring
   - Vulnerability scanning in CI

4. Data Protection
   - Encryption at rest (database, backups)
   - Encryption in transit (TLS everywhere)
   - Secrets in vault, not in code
   - Data backup and tested restore

5. Incident Response
   - Documented response plan
   - Contact list (who to call)
   - Communication templates
   - Annual tabletop exercise
```

## Tools That Help

| Tool | Purpose | Cost |
|------|---------|------|
| Vanta, Drata, Secureframe | Compliance automation | $10K-$25K/yr |
| GitHub (branch protection) | Change management evidence | Free |
| AWS CloudTrail | Audit logging | Included |
| PagerDuty/Opsgenie | Incident management | $20+/user/mo |

## How to Test Readiness

1. Can you show who has access to production? When was it last reviewed?
2. Can you show the audit trail for your last deployment?
3. Do you have an incident response plan? When was it last tested?
4. Are all production secrets in a secrets manager?
5. Can you produce a data inventory (what data, where stored, who accesses)?

## See Also

- hub/compliance.md
- research/compliance/gdpr-developers.md
- research/infrastructure/incident-response.md
- research/infrastructure/secrets-management.md
