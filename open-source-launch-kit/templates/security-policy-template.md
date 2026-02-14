---
title: "SECURITY.md Template"
tags: [template, security, vulnerability, disclosure]
content_type: research
domain: engineering
---

# SECURITY.md Template

Copy this template and customize for your project. Remove guidance comments before publishing.

---

# Security Policy

## Reporting a Vulnerability

<!-- Provide a private way to report vulnerabilities. -->
<!-- NEVER ask people to open public GitHub issues for security bugs. -->

If you discover a security vulnerability in this project, please report it responsibly.

**Do not open a public GitHub issue for security vulnerabilities.**

### How to Report

<!-- Choose ONE of these methods: -->

**Option A: GitHub Private Reporting (Recommended)**
Use GitHub's private vulnerability reporting feature:
[Report a vulnerability](https://github.com/yourorg/your-project/security/advisories/new)

**Option B: Email**
Send details to: security@yourproject.dev

### What to Include

- Description of the vulnerability
- Steps to reproduce
- Potential impact
- Suggested fix (if you have one)

### What to Expect

- **Acknowledgment:** Within 48 hours
- **Status update:** Within 1 week
- **Resolution:** Within 30 days for critical issues

We will credit you in the security advisory unless you prefer to remain anonymous.

## Supported Versions

<!-- List which versions receive security updates. -->

| Version | Supported |
|---------|-----------|
| 1.x.x   | Yes       |
| 0.x.x   | No        |

## Security Update Process

<!-- Explain how security fixes are released. -->

1. Security report received and acknowledged
2. Vulnerability confirmed and assessed for severity
3. Fix developed and tested
4. New version released with security fix
5. Security advisory published on GitHub
6. CVE requested for significant vulnerabilities

## Disclosure Policy

We follow coordinated disclosure:
- We work with the reporter to agree on a disclosure timeline
- Typical timeline: 90 days from initial report
- We may request an extension for complex vulnerabilities
- We will not disclose before a fix is available

---

<!-- END OF TEMPLATE -->
<!-- Tips: -->
<!-- 1. Enable GitHub's private vulnerability reporting in Settings > Security -->
<!-- 2. Set up a dedicated email if using email reporting -->
<!-- 3. Test the reporting flow yourself before publishing -->
<!-- See research/sustainability/security-vulnerability-handling.md for details -->
