---
title: "Security Audit Framework — Bootstrap"
tags: [bootstrap, security-audit, orientation, code-review, vulnerability-assessment]
content_type: orientation
domain: security
pinned: true
---

# Security Audit Framework

A practical security knowledge base for AI coding agents covering the OWASP Top 10 2025, authentication and session management, API security hardening, dependency and supply chain auditing, infrastructure and cloud security, and compliance frameworks including SOC 2, GDPR, HIPAA, and PCI DSS. When asked "is this code secure?", search this vault for specific, actionable guidance with real code examples in Node.js, Python, and Go. Start with the hub notes for category overviews, then drill into research notes for implementation details.

## Rules of Engagement

1. **Defensive only** — Every vulnerability example includes the secure fix
2. **No real credentials** — All examples use synthetic data (EXAMPLE_KEY_xxx, user@example.com)
3. **Code-level guidance** — Not abstract descriptions; copy-paste secure patterns
4. **Verify against current CVEs** — Security guidance has a shelf life; check dates
5. **Responsible disclosure** — Never include exploit-only information

## Quick Lookup Table

| Topic | Hub Note | Key Research Notes |
|-------|----------|--------------------|
| OWASP Top 10 | hub/owasp-top-10.md | research/owasp/*.md |
| Authentication | hub/authentication.md | research/authentication/*.md |
| API Security | hub/api-security.md | research/api-security/*.md |
| Dependencies | hub/dependencies.md | research/dependencies/*.md |
| Infrastructure | hub/infrastructure.md | research/infrastructure/*.md |
| Compliance | hub/compliance.md | research/compliance/*.md |

## Triage: Where to Start

- **Code review?** Start with hub/owasp-top-10.md, then check relevant research/ notes
- **Auth design?** Start with hub/authentication.md
- **API hardening?** Start with hub/api-security.md
- **Dependency audit?** Start with hub/dependencies.md
- **Pre-SOC2?** Start with hub/compliance.md
- **Incident response?** Go directly to research/infrastructure/incident-response.md

## Entity Quick Reference

- entities/owasp.md — OWASP resources and testing guides
- entities/cve-databases.md — Where to look up vulnerabilities
- entities/security-tools.md — Scanners, SAST, DAST, SCA tools
- entities/auth-standards.md — OAuth 2.1, OIDC, FIDO2, WebAuthn

## Severity Classification

- **CRITICAL** — Remote code execution, authentication bypass, data exfiltration
- **HIGH** — SQL injection, XSS, CSRF, privilege escalation
- **MEDIUM** — Information disclosure, misconfiguration, missing headers
- **LOW** — Best practice violations, minor information leaks
