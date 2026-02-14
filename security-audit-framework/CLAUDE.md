---
title: "Security Audit Framework — Governance"
content_type: template
tags: [governance, rules, security-review, code-review]
pinned: true
---

# Security Audit Framework — Governance

## Purpose

This vault provides defensive security guidance for AI coding agents performing code reviews, architecture reviews, and security assessments.

## Rules

1. **Never include real credentials, keys, or tokens** in any note or example
2. **Always show the secure pattern** alongside any vulnerable code example
3. **Verify dates** — security guidance expires; check CVE databases for current status
4. **Defensive only** — no exploit code without the corresponding fix
5. **Responsible disclosure mindset** — assume notes could be read by anyone

## How to Use This Vault

- When reviewing code, search research/owasp/ for relevant vulnerability patterns
- Check hub notes for category overviews before diving into specific research notes
- Entity files track tool versions — security tools update frequently
- Cross-reference related notes using tags and see-also links

## Search Patterns

- "SQL injection" → research/owasp/injection.md
- "authentication" → hub/authentication.md + research/authentication/
- "rate limiting" → research/api-security/rate-limiting.md
- "SOC2" → hub/compliance.md + research/compliance/
- "incident response" → research/infrastructure/incident-response.md

## Note Standards

- Every note has YAML frontmatter: title, tags, content_type, domain
- All notes under 200 lines
- Vulnerable code blocks labeled `<!-- VULNERABLE -->` with secure alternative following
- Tags use lowercase-hyphenated format
