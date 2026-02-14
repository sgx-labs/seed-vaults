---
title: "OWASP Top 10 (2025)"
tags: [owasp, top-10, vulnerabilities, web-security, security-audit]
content_type: hub
domain: security
---

# OWASP Top 10 — 2025 Edition

The OWASP Top 10 is the industry-standard vulnerability classification for web application security, covering broken access control, injection, cryptographic failures, security misconfiguration, and supply chain attacks. This hub provides the complete 2025 ranking with severity ratings, code examples showing vulnerable and secure patterns, prevention checklists, and links to deep-dive research notes for each category. Use this as the starting point for security audits, code reviews, and penetration testing preparation.

## The List

| # | Category | Severity | Research Note |
|---|----------|----------|---------------|
| A01 | Broken Access Control | CRITICAL | research/owasp/broken-access-control.md |
| A02 | Security Misconfiguration | HIGH | research/owasp/security-misconfiguration.md |
| A03 | Software Supply Chain Failures | CRITICAL | research/owasp/supply-chain-failures.md |
| A04 | Injection | CRITICAL | research/owasp/injection.md |
| A05 | Cryptographic Failures | HIGH | research/owasp/cryptographic-failures.md |
| A06 | Vulnerable and Outdated Components | HIGH | research/dependencies/vulnerable-components.md |
| A07 | Authentication Failures | CRITICAL | research/owasp/authentication-failures.md |
| A08 | Data Integrity Failures | HIGH | research/owasp/data-integrity-failures.md |
| A09 | Security Logging and Monitoring Failures | MEDIUM | research/owasp/logging-monitoring-failures.md |
| A10 | Mishandling of Exceptional Conditions | MEDIUM | research/owasp/exceptional-conditions.md |

## Key Changes from 2021

- **Security Misconfiguration** moved from #5 to #2
- **Software Supply Chain Failures** is NEW at #3 (previously part of A08:2021)
- **SSRF** rolled into Broken Access Control
- **Mishandling of Exceptional Conditions** is NEW at #10
- Focus shifted from symptoms to root causes

## Quick Audit Checklist

- [ ] Access control enforced server-side (not just UI)
- [ ] All user input validated, sanitized, parameterized
- [ ] Dependencies scanned and pinned to known-good versions
- [ ] Secrets in vault/KMS, not in code or env vars
- [ ] Authentication uses proven libraries (not custom crypto)
- [ ] Security headers set (CSP, HSTS, X-Frame-Options)
- [ ] Logging captures auth failures and access violations
- [ ] Error handling never leaks stack traces or internal state

## See Also

- hub/authentication.md — Auth failures (A07), password hashing, session management
- hub/api-security.md — Input validation, security headers, injection prevention
- hub/dependencies.md — Supply chain failures (A03), vulnerable components (A06)
- hub/infrastructure.md — Security misconfiguration (A02), secrets management
- hub/compliance.md — Compliance frameworks that reference OWASP
- entities/owasp.md — OWASP resources and testing guides
- entities/security-tools.md — Scanning tools for each category
