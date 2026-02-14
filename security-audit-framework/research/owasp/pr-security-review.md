---
title: "Security Review of Pull Requests"
tags: [code-review, security, pull-request, checklist]
content_type: research
domain: security
---

# Security Review of Pull Requests

A security-focused code review catches vulnerabilities before they reach production. Use this checklist alongside functional review.

## Quick Security Review Checklist

### Authentication & Authorization
- [ ] New endpoints have authentication middleware
- [ ] Authorization checks exist for all data access (not just UI hiding)
- [ ] No IDOR vulnerabilities (ownership checks on resource access)
- [ ] Privilege escalation not possible via API manipulation

### Input Handling
- [ ] All user input validated server-side (type, length, format)
- [ ] SQL queries use parameterized statements (never string concatenation)
- [ ] Output is encoded for the correct context (HTML, URL, JS)
- [ ] File paths are validated against traversal attacks

### Secrets & Configuration
- [ ] No hardcoded secrets, API keys, or credentials
- [ ] No new environment variables containing secrets in plaintext
- [ ] Configuration changes do not weaken security defaults
- [ ] Debug/verbose modes not enabled for production

### Dependencies
- [ ] New dependencies are from reputable sources
- [ ] Dependency versions are pinned (not ranges)
- [ ] No known vulnerabilities in new dependencies
- [ ] License is compatible with your project

### Data Handling
- [ ] Sensitive data is not logged (passwords, tokens, PII)
- [ ] Error responses do not leak internal details
- [ ] New database fields with PII have appropriate access controls
- [ ] Data retention considerations addressed

### Cryptography
- [ ] Passwords hashed with bcrypt/Argon2 (not MD5/SHA)
- [ ] Random values use CSPRNG (not Math.random)
- [ ] Encryption uses current algorithms (AES-256-GCM, not DES)
- [ ] TLS/HTTPS enforced for external communications

## Red Flags to Watch For

These patterns should trigger deeper review during code review:

**Dynamic query construction:**
- String concatenation in SQL: `"SELECT * FROM " + tableName`
- Template literals in queries: `` `SELECT * FROM users WHERE ${condition}` ``

**Shell execution with user input:**
- Using `shell=True` with variable arguments in Python subprocess
- Using shell-based execution methods with template strings in Node.js

**Disabled security features:**
- `// eslint-disable` comments for security rules
- `# nosec` annotations
- `verify=False` in HTTP requests
- `rejectUnauthorized: false` in TLS configuration

**Overly broad permissions:**
- `"Action": "*"` or `"Resource": "*"` in IAM policies
- `chmod 777` on files or directories

**Insecure randomness:**
- `Math.random()` in JavaScript for security-sensitive values
- `random.random()` in Python (use `secrets` module instead)

**Dangerous deserialization:**
- `yaml.load()` without SafeLoader
- Native serialization libraries with untrusted input

## Review Process

1. **Read the PR description** — Understand what changed and why
2. **Check the diff** for red flag patterns (above)
3. **Trace data flow** — Follow user input from entry to output
4. **Check authorization** — Can the wrong user trigger this code?
5. **Review error handling** — Do errors fail open or closed?
6. **Check dependencies** — Any new packages? Check their reputation.
7. **Verify tests** — Are there security-relevant test cases?

## Automated Checks to Require

Set up CI to run Semgrep with `p/security-audit` ruleset, dependency audit (`npm audit --audit-level=high`), and Gitleaks for secrets detection on every pull request.

## See Also

- hub/owasp-top-10.md — What to look for
- research/owasp/injection.md — SQL/XSS patterns
- research/owasp/broken-access-control.md — Auth review
- entities/security-tools.md — Automated scanning tools
