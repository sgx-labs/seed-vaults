---
title: "OWASP — Entity Reference"
tags: [owasp, entity, standards, testing, web-security]
content_type: entity
domain: security
pinned: true
---

# OWASP (Open Worldwide Application Security Project)

## Overview

OWASP is a nonprofit foundation that produces freely available security documentation, tools, and methodologies. Their resources are the de facto standard for application security.

## Key Resources

| Resource | URL | Purpose |
|----------|-----|---------|
| OWASP Top 10 (2025) | owasp.org/Top10/2025/ | Current top web app risks |
| OWASP ASVS | owasp.org/www-project-application-security-verification-standard/ | Detailed security requirements |
| OWASP Testing Guide | owasp.org/www-project-web-security-testing-guide/ | How to test for each vulnerability |
| OWASP Cheat Sheets | cheatsheetseries.owasp.org | Quick reference for secure patterns |
| OWASP SAMM | owaspsamm.org | Software security maturity model |
| OWASP API Security Top 10 | owasp.org/API-Security/ | API-specific security risks |

## OWASP Top 10 Version History

- **2025** — Current. Added Supply Chain Failures, Exceptional Conditions
- **2021** — Previous. Added Insecure Design, SSRF
- **2017** — Added XXE, Insecure Deserialization
- **2013** — Earlier version, still referenced in some compliance docs

## OWASP ASVS Levels

- **Level 1** — Minimum for all applications (automated verification)
- **Level 2** — Standard for most applications (manual + automated)
- **Level 3** — Critical applications (financial, healthcare, safety)

## Testing Methodology

The OWASP Testing Guide provides step-by-step procedures for testing each vulnerability category. Use it as a checklist during security assessments:

1. Information Gathering
2. Configuration and Deployment Management
3. Identity Management
4. Authentication
5. Authorization
6. Session Management
7. Input Validation
8. Error Handling
9. Cryptography
10. Business Logic
11. Client-Side Testing

## See Also

- hub/owasp-top-10.md — Current Top 10 with code examples
- entities/security-tools.md — Tools that implement OWASP checks
