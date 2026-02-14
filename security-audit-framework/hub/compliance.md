---
title: "Security Compliance for Developers"
tags: [compliance, soc2, gdpr, hipaa, pci-dss, privacy, data-protection]
content_type: hub
domain: security
---

# Security Compliance for Developers

Security compliance covers the regulatory frameworks and audit standards that developers must implement, including SOC 2 Trust Service Criteria for B2B SaaS, GDPR data protection rights for EU users, HIPAA Protected Health Information safeguards for healthcare applications, PCI DSS cardholder data requirements for payment processing, and CCPA consumer privacy rights. This hub provides developer-focused checklists, cost estimates, framework comparison tables, and links to detailed research notes with code examples for implementing each compliance requirement.

## Core Principles

1. **Security first, compliance follows** — Good security practices satisfy most frameworks
2. **Document everything** — Compliance is about proving you did the right thing
3. **Privacy by design** — Build data protection into architecture, not as an afterthought
4. **Least data** — Collect only what you need, retain only as long as required

## Framework Quick Reference

| Framework | Who Needs It | Key Focus | Research Note |
|-----------|-------------|-----------|---------------|
| SOC 2 | B2B SaaS | Security controls & audit trail | research/compliance/soc2-basics.md |
| GDPR | Anyone with EU users | Data protection & privacy rights | research/compliance/gdpr-developers.md |
| HIPAA | Healthcare data handlers | PHI protection | research/compliance/hipaa-basics.md |
| PCI DSS | Payment processors | Cardholder data protection | research/compliance/pci-dss-basics.md |
| CCPA/CPRA | California consumer data | Consumer privacy rights | research/compliance/gdpr-developers.md |

## SOC 2 for Startups — What Actually Matters

**Start pursuing SOC 2 when:**
- Enterprise deals ($100K+ ACV) are in your pipeline
- You are at $1M-$2M ARR with budget ($20K-$50K)
- 70% of VCs prefer SOC 2-compliant startups

**The five Trust Service Criteria:**
1. **Security** (required) — Logical/physical access, system operations, change management
2. **Availability** — Uptime, disaster recovery, backups
3. **Processing Integrity** — Data processing accuracy
4. **Confidentiality** — Data classification and protection
5. **Privacy** — PII handling per privacy notice

See: research/compliance/soc2-basics.md

## GDPR Quick Reference

- **Lawful basis required** for every data processing activity
- **Right to deletion** — Users can request data removal
- **Data breach notification** — 72 hours to notify authorities
- **Privacy by design** — Mandatory, not optional
- **Fines** — Up to 4% of global annual revenue or 20M EUR

See: research/compliance/gdpr-developers.md

## Developer Compliance Checklist

- [ ] Data inventory: know what personal data you collect and where it lives
- [ ] Access controls: document who can access what and why
- [ ] Encryption: data at rest and in transit
- [ ] Audit logging: track access to sensitive data
- [ ] Retention policy: delete data you no longer need
- [ ] Incident response plan: documented and tested
- [ ] Vendor assessment: third-party services reviewed for security

## See Also

- hub/owasp-top-10.md — OWASP as compliance baseline
- hub/authentication.md — Auth controls required by all frameworks
- hub/infrastructure.md — Technical controls for compliance
- hub/dependencies.md — SBOM and provenance for compliance
- research/infrastructure/incident-response.md — Required for all frameworks
- research/infrastructure/sensitive-data-logs.md — Logging compliance
- research/compliance/security-policy-templates.md — Policy templates for startups
