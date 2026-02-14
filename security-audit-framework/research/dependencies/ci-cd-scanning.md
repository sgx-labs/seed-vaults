---
title: "CI/CD Security Scanning Setup"
tags: [dependencies, ci-cd, sast, sca, automation, semgrep, gitleaks, github-actions]
content_type: research
domain: security
---

# CI/CD Security Scanning Setup

Automated security scanning in CI/CD catches vulnerabilities before they reach production. This is the minimum viable security automation.

## Recommended Pipeline Stages

```
Code -> Lint -> SAST -> Test -> SCA -> Build -> Container Scan -> Deploy
```

| Stage | Tool | What It Catches |
|-------|------|----------------|
| SAST | Semgrep | Code-level vulnerabilities |
| SCA | npm audit / Dependabot | Known dependency CVEs |
| Secrets | Gitleaks | Accidentally committed credentials |
| Container | Trivy | Base image vulnerabilities |
| IaC | Checkov | Cloud misconfiguration |

## GitHub Actions — Complete Security Pipeline

```yaml
name: Security Pipeline
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  secrets-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11
        with:
          fetch-depth: 0  # Full history for scanning
      - uses: gitleaks/gitleaks-action@cb7149a9b57195b609c63e8518d2c6056677d2d0 # v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11
      - uses: returntocorp/semgrep-action@713efdd345f3035192eaa63f56867b88e63e4e5d
        with:
          config: >-
            p/security-audit
            p/owasp-top-ten
            p/nodejs
            p/python

  dependency-audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11
      - uses: actions/setup-node@60edb5dd545a775178f52524783378180af0d1f8
        with:
          node-version: '20'
      - run: npm ci
      - run: npm audit --audit-level=high

  container-scan:
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    needs: [sast, dependency-audit]
    steps:
      - uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11
      - name: Build image
        run: docker build -t app:${{ github.sha }} .
      - name: Scan with Trivy
        uses: aquasecurity/trivy-action@062f2592684a31eb3aa050cc61e7ca1451cebd99
        with:
          image-ref: 'app:${{ github.sha }}'
          severity: 'CRITICAL,HIGH'
          exit-code: '1'
```

## Semgrep Configuration

Use pre-built rulesets to detect dangerous patterns like dynamic code execution, hardcoded secrets, SQL injection, and XSS.

```yaml
# .semgrep.yml — Reference existing rulesets
rules:
  # Use OWASP and language-specific rulesets via Semgrep CI:
  # semgrep ci --config p/owasp-top-ten --config p/security-audit
  #
  # Key patterns these rulesets detect:
  # - Dynamic code execution (eval, exec, Function constructor)
  # - SQL string concatenation (use parameterized queries instead)
  # - Insecure deserialization
  # - Hardcoded credentials
  # - Command injection via shell execution
  # - XSS through unescaped output
  - id: no-hardcoded-passwords
    patterns:
      - pattern: |
          $KEY = "..."
      - metavariable-regex:
          metavariable: $KEY
          regex: ".*(password|secret|api_key).*"
    message: "Possible hardcoded secret detected"
    languages: [python, javascript]
    severity: WARNING
```

## GitLab CI Equivalent

```yaml
# .gitlab-ci.yml
security-scan:
  stage: test
  image: returntocorp/semgrep
  script:
    - semgrep ci --config p/security-audit --config p/owasp-top-ten
  allow_failure: false

dependency-scan:
  stage: test
  script:
    - npm ci
    - npm audit --audit-level=high
  allow_failure: false
```

## Best Practices

1. **Fail the build** on high/critical findings (do not just warn)
2. **Pin all action SHAs** — never use floating tags
3. **Scan on every PR** — not just on push to main
4. **Use SARIF output** for GitHub Security tab integration
5. **Triage findings weekly** — do not let the backlog grow
6. **Start with 3 tools** — Semgrep + npm audit + Gitleaks covers most risk

## See Also

- hub/dependencies.md
- research/dependencies/npm-security.md
- entities/security-tools.md
- research/infrastructure/cicd-security.md
