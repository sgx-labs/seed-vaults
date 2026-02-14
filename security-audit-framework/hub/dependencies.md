---
title: "Dependency & Supply Chain Security"
tags: [dependencies, supply-chain, sca, npm, pip, go-modules]
content_type: hub
domain: security
---

# Dependency & Supply Chain Security

Your code is only as secure as your weakest dependency. Supply chain attacks nearly doubled in 2025 — this is not theoretical.

## Core Principles

1. **Pin versions** — Use lockfiles and exact versions, not ranges
2. **Audit regularly** — Automated scanning in CI, manual review for critical deps
3. **Minimize dependencies** — Every dependency is attack surface
4. **Verify integrity** — Use checksums, signatures, and provenance attestations

## Ecosystem-Specific Auditing

| Ecosystem | Audit Command | Lockfile |
|-----------|--------------|----------|
| npm | `npm audit` | package-lock.json |
| Python/pip | `pip-audit` | requirements.txt / poetry.lock |
| Go | `govulncheck ./...` | go.sum |
| Ruby | `bundle audit` | Gemfile.lock |
| Rust | `cargo audit` | Cargo.lock |

## Supply Chain Attack Vectors

- **Typosquatting** — Packages with names similar to popular ones
- **Dependency confusion** — Public packages overriding private ones
- **Maintainer compromise** — Legitimate packages with malicious updates
- **Build pipeline attacks** — Compromised CI/CD injecting malware
- **Protestware** — Maintainers adding destructive code intentionally

See: research/dependencies/supply-chain-attacks.md

## CI/CD Integration

```yaml
# Example: GitHub Actions dependency scanning
- name: Run dependency audit
  run: |
    npm audit --audit-level=high
    # Fail build on high/critical vulnerabilities
```

See: research/dependencies/ci-cd-scanning.md

## Research Notes

| Topic | Note |
|-------|------|
| Supply Chain Attacks | research/dependencies/supply-chain-attacks.md |
| npm Security | research/dependencies/npm-security.md |
| Vulnerable Components | research/dependencies/vulnerable-components.md |
| CI/CD Scanning | research/dependencies/ci-cd-scanning.md |
| SBOM and Provenance | research/dependencies/sbom-provenance.md |

## Recent Notable Supply Chain Incidents (2025)

- **s1ngularity campaign** (Aug-Nov 2025): Compromised Nx packages, harvested 2,349 credentials from 1,079 developer systems
- Supply chain attacks grew 93% year-over-year (154 to 297 claimed incidents)

## See Also

- hub/owasp-top-10.md — A03: Supply Chain, A06: Vulnerable Components
- entities/security-tools.md — SCA tools (Snyk, Dependabot, Trivy)
- entities/cve-databases.md — Where to look up vulnerabilities
