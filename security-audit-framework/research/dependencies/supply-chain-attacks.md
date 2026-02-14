---
title: "Supply Chain Attack Vectors and Prevention"
tags: [dependencies, supply-chain, typosquatting, dependency-confusion]
content_type: research
domain: security
---

# Supply Chain Attack Vectors and Prevention

Your application includes hundreds of third-party packages. Each one is a trust relationship. Supply chain attacks exploit that trust.

## Attack Taxonomy

### 1. Typosquatting

Attacker publishes a package with a name similar to a popular one.

**Examples:**
- `lodahs` instead of `lodash`
- `colars` instead of `colors`
- `crossenv` instead of `cross-env`

**Prevention:**
- Double-check package names before installing
- Use `npm search` or `pip search` to verify publisher
- Enable typosquatting detection (Socket.dev, Snyk)

### 2. Dependency Confusion

Public package registries take priority over private ones when the version is higher.

**Prevention:**
```
# npm — Use scoped packages (@company/package-name)
# .npmrc
@company:registry=https://npm.internal.example.com

# pip — Use explicit index URL
pip install --index-url https://pypi.internal.example.com/simple/ my-package
```

### 3. Maintainer Compromise

Legitimate maintainer account is compromised, or a new malicious maintainer takes over an abandoned package.

**Prevention:**
- Pin exact versions and review changelogs before updating
- Use lockfiles and verify checksums
- Monitor for maintainer changes on critical dependencies
- Use `npm audit signatures` to verify package provenance

### 4. Build Pipeline Attacks

CI/CD pipelines pull code/actions that can be modified by attackers.

**Prevention:**
```yaml
# Pin GitHub Actions to commit SHAs
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11
# Never use @main or @latest for actions
```

### 5. Protestware / Sabotage

Maintainers intentionally add destructive or political code.

**Notable incidents:**
- colors.js / faker.js (2022) — Maintainer added infinite loop
- node-ipc (2022) — Added code targeting specific geolocations

**Prevention:**
- Pin versions, review updates before merging
- Use Dependabot/Renovate for controlled, reviewable updates
- Monitor for unexpected behavior changes in dependencies

## Dependency Risk Assessment

Before adding a new dependency, evaluate:

| Factor | Low Risk | High Risk |
|--------|----------|-----------|
| Maintainers | Multiple, active | Single, inactive |
| Downloads | Millions/week | Hundreds/week |
| Age | 3+ years | < 6 months |
| License | MIT, Apache, BSD | No license, unusual |
| Dependencies | Few, well-known | Many, unknown |
| Security policy | SECURITY.md present | None |

## Incident Response

If you discover a compromised dependency:

1. **Immediately** pin to a known-good version
2. Check if the malicious version was installed in production
3. Audit what the malicious code could have accessed
4. Rotate all secrets that were accessible to the application
5. Report to the package registry (npm, PyPI, etc.)

## See Also

- hub/dependencies.md
- research/owasp/supply-chain-failures.md
- research/dependencies/npm-security.md
