---
title: "Security Decision Log"
tags: [decisions, log, security-policy, architecture-decisions]
content_type: decision
domain: security
---

# Security Decision Log

## 2026-02-14 — Use OWASP Top 10 2025 as primary framework

**Status:** Accepted

OWASP Top 10 2025 is now the current version with significant changes from 2021: Supply Chain Failures is new at #3, Security Misconfiguration moved to #2, and Mishandling of Exceptional Conditions is new at #10. All research notes reference the 2025 categories.

## 2026-02-14 — Argon2id as recommended password hashing algorithm

**Status:** Accepted

Argon2id is recommended over bcrypt for new implementations. bcrypt remains acceptable (cost factor >= 12) but has a 72-byte input limit. PBKDF2 is legacy-only with 600K+ iterations. MD5/SHA1/SHA256 are never acceptable for password hashing.

## 2026-02-14 — Defensive-only security guidance

**Status:** Accepted

All vulnerability examples must include the secure fix. No exploit-only code. This vault teaches identification and remediation, not exploitation. Every code example follows the pattern: vulnerable code, explanation, secure alternative, testing approach.

## 2026-02-14 — Multi-language code examples (Node.js, Python, Go)

**Status:** Accepted

Research notes include code examples in Node.js, Python, and Go as these are the most common backend languages. Examples use standard library functions and widely-adopted packages. No framework-specific patterns unless clearly labeled.

## 2026-02-14 — FIDO2/Passkeys as recommended auth direction

**Status:** Accepted

FIDO2/WebAuthn (passkeys) are recommended as the direction for new authentication implementations. They provide phishing resistance, eliminate shared secrets, and are now supported across all major platforms. OAuth 2.1 + OIDC remain standard for federated/delegated auth.

## 2026-02-14 — Secrets in vault/KMS, not environment variables

**Status:** Accepted

Environment variables are visible in process listings, crash dumps, and debug endpoints. Production secrets should use a dedicated secrets manager (HashiCorp Vault, AWS Secrets Manager, GCP Secret Manager). Environment variables are acceptable only for local development with non-production values.
