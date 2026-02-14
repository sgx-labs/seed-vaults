---
title: "npm Security — Audit and Hardening"
tags: [dependencies, npm, node, javascript, audit, lockfile, npmrc, package-lock]
content_type: research
domain: security
---

# npm Security — Audit and Hardening

npm is the largest package registry with over 2 million packages. Its size makes it both incredibly useful and a prime target for supply chain attacks.

## Essential Commands

```bash
# Audit for known vulnerabilities
npm audit

# Audit with severity filter (fail on high/critical only)
npm audit --audit-level=high

# Fix automatically where possible
npm audit fix

# Use lockfile exactly (CI/CD — never npm install)
npm ci

# View dependency tree
npm ls --all

# Check for outdated packages
npm outdated

# Verify package signatures (npm 8.15+)
npm audit signatures
```

## .npmrc Hardening

```ini
# .npmrc — Project-level configuration
# Require exact versions (no semver ranges)
save-exact=true

# Require lockfile to be present
package-lock=true

# Prevent scripts from running during install (security)
ignore-scripts=true

# Audit on every install
audit=true

# For organizations with private packages:
# @company:registry=https://npm.internal.example.com
```

**Note:** `ignore-scripts=true` may break packages that need postinstall scripts. Use `--ignore-scripts` selectively or add a `.npmrc` override for specific packages.

## package.json Security

```json
{
  "scripts": {
    "preinstall": "npx only-allow npm",
    "audit": "npm audit --audit-level=high",
    "audit:fix": "npm audit fix"
  },
  "engines": {
    "node": ">=20.0.0",
    "npm": ">=10.0.0"
  },
  "overrides": {
    "vulnerable-transitive-dep": ">=2.0.1"
  }
}
```

## CI/CD Integration

```yaml
# GitHub Actions — npm security audit
name: Security Audit
on: [push, pull_request]

jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4
      - uses: actions/setup-node@60edb5dd545a775178f52524783378180af0d1f8 # v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm audit --audit-level=high
      - run: npx lockfile-lint --path package-lock.json --type npm --allowed-hosts npm --validate-https
```

## Lockfile Security

```bash
# lockfile-lint — Verify lockfile integrity
npx lockfile-lint \
  --path package-lock.json \
  --type npm \
  --allowed-hosts npm \
  --validate-https \
  --validate-integrity
```

This verifies:
- All packages come from the expected registry
- All URLs use HTTPS
- Integrity hashes are present

## Common Mistakes

- Using `npm install` instead of `npm ci` in CI (ignores lockfile)
- Not committing `package-lock.json` to version control
- Using semver ranges (`^`, `~`) for critical dependencies
- Not reviewing `npm audit` output before deploying
- Running `npm audit fix --force` blindly (may introduce breaking changes)

## See Also

- hub/dependencies.md
- research/dependencies/supply-chain-attacks.md
- research/dependencies/ci-cd-scanning.md
