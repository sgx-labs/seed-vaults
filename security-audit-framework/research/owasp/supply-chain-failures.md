---
title: "A03:2025 — Software Supply Chain Failures"
tags: [owasp, supply-chain, dependencies, sbom, integrity]
content_type: research
domain: security
---

# A03:2025 — Software Supply Chain Failures

NEW in the 2025 OWASP Top 10. Supply chain attacks grew 93% in 2025. The code you pull from registries is part of YOUR attack surface.

## Attack Vectors

1. **Typosquatting** — `lodahs` instead of `lodash`
2. **Dependency confusion** — Public package overriding private scope
3. **Maintainer compromise** — Legitimate account takeover
4. **Build pipeline injection** — Compromised CI/CD actions
5. **Protestware/sabotage** — Intentional destructive updates

## Real-World Impact (2025)

- **s1ngularity campaign**: Compromised Nx packages, harvested 2,349 credentials
- Supply chain attacks claimed by threat groups: 297 (up from 154 in 2024)

## Vulnerable: Unpinned Dependencies

```json
{
  "dependencies": {
    "express": "^4.18.0",
    "lodash": "*",
    "some-util": "latest"
  }
}
```

**Why it is vulnerable:** Semver ranges and `latest` allow automatic installation of compromised versions. An attacker who publishes a malicious minor/patch version will be pulled in automatically.

## Secure: Pinned with Lockfile Verification

```json
{
  "dependencies": {
    "express": "4.21.1",
    "lodash": "4.17.21"
  }
}
```

```bash
# Verify lockfile integrity in CI
npm ci  # Uses package-lock.json exactly (not npm install)
```

## Vulnerable: Dependency Confusion (Python)

```
# requirements.txt — internal package name
my-company-utils==1.2.3
# Attacker publishes "my-company-utils" on PyPI at version 99.0.0
# pip installs the public version (higher version number)
```

## Secure: Namespace Protection

```
# Use scoped registries or explicit index URLs
--index-url https://pypi.internal.example.com/simple/
--extra-index-url https://pypi.org/simple/

# Or use pip.conf to set priority
# Or prefix private packages with organization scope
```

## Secure: Go Module Verification

```go
// go.sum provides cryptographic checksums
// Go proxy (proxy.golang.org) caches and verifies modules
// GONOSUMCHECK and GONOSUMDB should NOT be set in production

// Verify in CI:
// go mod verify
```

## Prevention Checklist

- [ ] Pin exact versions in dependency files
- [ ] Use lockfiles (`package-lock.json`, `poetry.lock`, `go.sum`)
- [ ] Run `npm ci` (not `npm install`) in CI
- [ ] Enable Dependabot or Renovate for automated updates
- [ ] Use scoped registries for private packages
- [ ] Generate and verify SBOMs (Software Bill of Materials)
- [ ] Review new dependencies before adding (check maintainers, activity, scope)
- [ ] Pin GitHub Actions to commit SHAs, not tags

## How to Test

1. Run `npm audit` / `pip-audit` / `govulncheck`
2. Check for unpinned versions in dependency files
3. Verify lockfile is committed and used in CI
4. Review CI/CD pipeline for third-party actions/orbs without SHA pinning
5. Use Socket.dev or Snyk to analyze dependency risk

## See Also

- hub/dependencies.md
- research/dependencies/supply-chain-attacks.md
- research/dependencies/npm-security.md
- research/dependencies/sbom-provenance.md
