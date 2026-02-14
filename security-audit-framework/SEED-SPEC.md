# Security Audit Framework Seed

## Metadata
- **Name**: security-audit-framework
- **Type**: Knowledge
- **Audience**: Backend developers, AppSec engineers, security-conscious teams
- **Note count target**: 50-70
- **Agent build time**: 3-4 hours

## Purpose
A structured security knowledge base that helps your AI agent perform security reviews, identify vulnerabilities, and recommend fixes. Covers OWASP Top 10, authentication patterns, API security, dependency auditing, and secure coding practices.

## Hub Categories

1. hub/owasp-top-10.md — Current OWASP Top 10 with examples and fixes
2. hub/authentication.md — Auth patterns, session management, OAuth, JWT
3. hub/api-security.md — Rate limiting, input validation, CORS, headers
4. hub/dependencies.md — Supply chain security, auditing, CVE monitoring
5. hub/infrastructure.md — Cloud security, secrets management, network
6. hub/compliance.md — SOC2, GDPR, HIPAA basics for developers

## Key Questions

1. What are the OWASP Top 10 and how do I check for each one?
2. How do I implement secure authentication (bcrypt, Argon2, session tokens)?
3. How do I prevent SQL injection, XSS, and CSRF?
4. How do I manage secrets in production (never env vars, use vaults)?
5. How do I audit npm/pip/go dependencies for known vulnerabilities?
6. How do I set up Content Security Policy headers correctly?
7. How do I implement rate limiting to prevent abuse?
8. How do I handle file uploads securely?
9. How do I implement proper CORS configuration?
10. How do I do a security review of a pull request?
11. What are the common API security mistakes?
12. How do I handle password reset flows securely?
13. How do I implement role-based access control (RBAC)?
14. How do I secure WebSocket connections?
15. How do I handle sensitive data in logs?
16. What are the security implications of server-side rendering vs SPA?
17. How do I set up automated security scanning in CI/CD?
18. How do I respond to a security vulnerability report?
19. How do I implement secure JWT handling (rotation, revocation)?
20. What are the basics of SOC2 compliance for a startup?

## Entities

1. entities/owasp.md — Current version, key resources, testing guide
2. entities/cve-databases.md — NVD, GitHub Advisory, Snyk DB
3. entities/security-tools.md — Semgrep, Dependabot, Trivy, SAST/DAST tools
4. entities/auth-standards.md — OAuth 2.1, OIDC, FIDO2/WebAuthn current state

## CLAUDE.md Governance

- Security notes are reference — always verify against current CVE databases
- When reviewing code, check research/owasp/ for relevant vulnerability patterns
- Never include real credentials, keys, or tokens in any note
- Entity files track tool versions — security tools update frequently

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1600
  max_results = 5
  composite_threshold = 0.65
```
