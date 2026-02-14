---
title: "Security Tools — Entity Reference"
tags: [tools, sast, dast, sca, scanning, entity]
content_type: entity
domain: security
---

# Security Tools Reference

## Tool Categories

### SAST (Static Application Security Testing)

Analyzes source code without running it.

| Tool | Languages | License | Notes |
|------|-----------|---------|-------|
| Semgrep | 30+ languages | Free + Enterprise | Custom rules, fast, CI-friendly |
| CodeQL | 10+ languages | Free for OSS | GitHub-native, deep analysis |
| Bandit | Python | Free | Python-specific, fast |
| Brakeman | Ruby/Rails | Free | Rails-specific |
| gosec | Go | Free | Go-specific security linter |
| ESLint security plugins | JavaScript | Free | eslint-plugin-security |

### DAST (Dynamic Application Security Testing)

Tests running applications from the outside.

| Tool | License | Notes |
|------|---------|-------|
| OWASP ZAP | Free | Industry standard, scriptable |
| Burp Suite | Free + Pro | Manual + automated testing |
| Nuclei | Free | Template-based, community-driven |

### SCA (Software Composition Analysis)

Scans dependencies for known vulnerabilities.

| Tool | Ecosystems | License | Notes |
|------|-----------|---------|-------|
| Dependabot | npm, pip, go, etc. | Free (GitHub) | Automatic PRs for updates |
| Snyk | All major | Free tier | Detailed remediation |
| Trivy | Containers + code | Free | Also scans IaC and containers |
| Grype | Containers | Free | Anchore project, fast |
| govulncheck | Go | Free | Official Go tool |
| pip-audit | Python | Free | Uses OSV database |
| npm audit | Node.js | Free (built-in) | Uses GitHub Advisory DB |

### Infrastructure/Container Scanning

| Tool | Focus | License |
|------|-------|---------|
| Trivy | Containers, IaC, SBOM | Free |
| Checkov | Terraform, CloudFormation | Free |
| tfsec | Terraform | Free |
| kube-bench | Kubernetes CIS benchmarks | Free |
| ScoutSuite | Multi-cloud audit | Free |

### Secrets Detection

| Tool | License | Notes |
|------|---------|-------|
| Gitleaks | Free | Git history scanning |
| TruffleHog | Free | Entropy + pattern detection |
| detect-secrets (Yelp) | Free | Pre-commit hook friendly |

## Recommended Starter Stack

For a startup with limited security budget:

1. **Semgrep** — SAST in CI (free)
2. **Dependabot** — SCA (free on GitHub)
3. **Trivy** — Container scanning (free)
4. **Gitleaks** — Secrets detection (free)
5. **OWASP ZAP** — Periodic DAST (free)

## See Also

- hub/dependencies.md — Dependency scanning workflows
- research/dependencies/ci-cd-scanning.md — CI/CD integration
- entities/cve-databases.md — Where tools get their data
