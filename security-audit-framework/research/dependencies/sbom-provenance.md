---
title: "SBOM and Software Provenance"
tags: [dependencies, sbom, provenance, supply-chain, attestation, cyclonedx, slsa, spdx]
content_type: research
domain: security
---

# SBOM and Software Provenance

An SBOM (Software Bill of Materials) is a complete inventory of all components in your software. Provenance proves where your software came from and how it was built.

## Why SBOMs Matter

- Required by US Executive Order 14028 for government suppliers
- Required by many enterprise customers
- Enables rapid response to new CVEs (search your SBOM, not your code)
- Growing compliance requirement (SOC 2, ISO 27001)

## SBOM Formats

| Format | Maintained By | Best For |
|--------|--------------|----------|
| SPDX | Linux Foundation | License compliance + security |
| CycloneDX | OWASP | Security-focused, widely supported |
| SWID | ISO/IEC | Software identification tags |

## Generating SBOMs

```bash
# CycloneDX — Node.js
npx @cyclonedx/cyclonedx-npm --output-file sbom.json

# CycloneDX — Python
pip install cyclonedx-bom
cyclonedx-py environment --output sbom.json

# CycloneDX — Go
go install github.com/CycloneDX/cyclonedx-gomod/cmd/cyclonedx-gomod@latest
cyclonedx-gomod mod -json -output sbom.json

# Syft — Multi-ecosystem (Anchore)
syft dir:. -o cyclonedx-json > sbom.json
syft docker:myapp:latest -o spdx-json > sbom.json

# Trivy — Generate SBOM + vulnerability scan
trivy fs --format cyclonedx --output sbom.json .
```

## CI/CD Integration

```yaml
# Generate and store SBOM in CI
- name: Generate SBOM
  run: |
    npx @cyclonedx/cyclonedx-npm --output-file sbom.json
- name: Upload SBOM
  uses: actions/upload-artifact@5d5d22a31266ced268874388b861e4b58bb5c2f3
  with:
    name: sbom
    path: sbom.json
```

## Software Provenance with SLSA

SLSA (Supply chain Levels for Software Artifacts) is a framework for ensuring the integrity of software artifacts.

**Levels:**
- **Level 1**: Documentation of build process
- **Level 2**: Hosted build platform, signed provenance
- **Level 3**: Hardened build platform, non-falsifiable provenance
- **Level 4**: Two-party review, hermetic builds

## npm Package Provenance

```bash
# Publish with provenance (npm 9.5+)
npm publish --provenance

# Verify provenance of installed packages
npm audit signatures
```

## Vulnerability Scanning Against SBOM

```bash
# Scan an existing SBOM for known vulnerabilities
grype sbom:sbom.json
trivy sbom sbom.json
```

When a new CVE is announced, scan your stored SBOMs to determine exposure within minutes instead of hours.

## See Also

- hub/dependencies.md
- research/dependencies/supply-chain-attacks.md
- research/dependencies/ci-cd-scanning.md
- hub/compliance.md
