---
title: "A06:2025 — Vulnerable and Outdated Components"
tags: [owasp, dependencies, components, patching, sca]
content_type: research
domain: security
---

# A06:2025 — Vulnerable and Outdated Components

You are responsible for every line of code in your application — including the code you did not write. 80%+ of modern applications are third-party code.

## The Problem

- Applications use hundreds to thousands of dependencies
- Transitive dependencies (dependencies of dependencies) are invisible
- Known vulnerabilities are published daily
- Outdated components miss security patches

## Audit Commands by Ecosystem

```bash
# Node.js
npm audit --audit-level=high
npx better-npm-audit audit

# Python
pip-audit
safety check

# Go
govulncheck ./...

# Ruby
bundle audit check --update

# Rust
cargo audit

# Java/Kotlin (Gradle)
./gradlew dependencyCheckAnalyze
```

## Automated Scanning Setup

### Dependabot (GitHub)

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
    reviewers:
      - "security-team"
    labels:
      - "dependencies"
      - "security"

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
```

### Renovate (More Configurable)

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["config:base", ":semanticCommits"],
  "vulnerabilityAlerts": { "enabled": true },
  "packageRules": [
    {
      "matchUpdateTypes": ["patch"],
      "automerge": true
    },
    {
      "matchUpdateTypes": ["major"],
      "reviewers": ["security-team"]
    }
  ]
}
```

## Triage Workflow

When a vulnerability is reported:

1. **Check exploitability** — Is the vulnerable code path actually used?
2. **Check severity** — CVSS score + real-world exploit availability
3. **Check fix availability** — Is there a patched version?
4. **Act based on severity:**
   - Critical: Patch within 24 hours
   - High: Patch within 1 week
   - Medium: Patch within 1 sprint
   - Low: Address in normal maintenance

## Using `overrides` for Transitive Vulnerabilities

```json
{
  "overrides": {
    "vulnerable-package": ">=2.0.1"
  }
}
```

```python
# pip — Use constraints file
# constraints.txt
vulnerable-package>=2.0.1

# pip install -c constraints.txt -r requirements.txt
```

## How to Test

1. Run ecosystem-specific audit command
2. Check for outdated dependencies (`npm outdated`, `pip list --outdated`)
3. Verify Dependabot or Renovate is configured
4. Review transitive dependency tree for hidden vulnerabilities
5. Check that CI fails on high/critical vulnerabilities

## See Also

- hub/dependencies.md
- hub/owasp-top-10.md
- research/dependencies/ci-cd-scanning.md
- entities/cve-databases.md
