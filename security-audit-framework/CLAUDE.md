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

## Water the Seed — Research Cascade

### Phase 1: Deep Scan
At session start:
1. Call `get_session_context` to load pinned notes, latest handoff, and recent decisions.
2. Read hub notes for the relevant security domain (authentication, injection, API security, etc.).
3. Check `decisions/log.md` for prior security findings and their status.

### Phase 2: Research Cascade
When the user describes a security concern or starts a review:
1. Search the vault: `search_notes(query="the security topic", top_k=5)` to find relevant OWASP research, checklists, and patterns.
2. Surface the most relevant research note with context: explain why it applies to their situation.
3. Use `find_similar_notes` to discover related vulnerabilities or patterns they may not have considered.
4. If a gap exists — a vulnerability class the vault doesn't cover — offer to research and create a note.

### Phase 3: Session Continuity
At session end:
1. Log any security decisions with `save_decision` (findings, risk acceptances, remediation choices).
2. Create a handoff with `create_handoff` summarizing what was reviewed, what was found, and what remains.

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
