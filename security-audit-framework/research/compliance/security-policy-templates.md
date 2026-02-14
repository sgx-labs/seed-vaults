---
title: "Security Policy Templates for Startups"
tags: [compliance, policy, security-policy, responsible-disclosure, security-md, data-classification]
content_type: research
domain: security
---

# Security Policy Templates for Startups

Every production application needs these policies. Start with templates, customize for your context.

## SECURITY.md (for GitHub Repositories)

```markdown
# Security Policy

## Reporting a Vulnerability

We take security seriously. If you discover a vulnerability,
please report it responsibly.

**DO NOT** open a public GitHub issue for security vulnerabilities.

### How to Report

Email: security@example.com
PGP Key: [link to PGP key] (optional)

### What to Include

- Description of the vulnerability
- Steps to reproduce
- Impact assessment
- Any potential fixes you have identified

### Response Timeline

- **Acknowledgment**: Within 48 hours
- **Assessment**: Within 5 business days
- **Fix timeline**: Depends on severity
  - Critical: 72 hours
  - High: 1 week
  - Medium: 2 weeks
  - Low: Next release

### Recognition

We credit researchers who report valid vulnerabilities
(unless they prefer to remain anonymous).

### Scope

In scope:
- Application at app.example.com
- API at api.example.com

Out of scope:
- Third-party services
- Social engineering attacks
- Denial of service attacks
```

## Acceptable Use Policy (Internal)

Key sections:
1. **Password requirements** — MFA mandatory, password manager required
2. **Device security** — Disk encryption, screen lock, OS updates
3. **Data handling** — Classification levels, sharing rules, retention
4. **Incident reporting** — How and when to report security concerns
5. **Access management** — Request process, periodic review, offboarding

## Data Classification Policy

| Level | Examples | Handling |
|-------|---------|---------|
| Public | Marketing content, open-source code | No restrictions |
| Internal | Internal docs, non-sensitive configs | Share within org only |
| Confidential | Customer data, financial records | Encrypted, access-controlled |
| Restricted | Credentials, PHI, PII, encryption keys | Need-to-know, encrypted, audited |

## Incident Response Policy

See: research/infrastructure/incident-response.md

Key elements:
1. Definition of a security incident
2. Severity levels and response times
3. Roles and responsibilities
4. Communication procedures
5. Post-incident review process

## Data Retention Policy

| Data Type | Retention | Deletion Method |
|-----------|-----------|-----------------|
| Application logs | 90 days | Automated deletion |
| Security logs | 1 year | Automated deletion after review |
| User data | Duration of service + 30 days | Soft delete + scheduled hard delete |
| Financial records | 7 years | Archived, encrypted |
| Backups | 90 days | Automated rotation |

## How to Start

1. Create `SECURITY.md` in your GitHub repository (today)
2. Set up security@example.com email alias
3. Write a one-page incident response plan
4. Document who has access to production
5. Expand policies as needed for compliance (SOC 2, GDPR)

## See Also

- hub/compliance.md
- research/compliance/soc2-basics.md
- research/infrastructure/incident-response.md
