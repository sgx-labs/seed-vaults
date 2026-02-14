---
title: "npm — Publishing, Scoped Packages, and Provenance"
tags: [npm, entity, publishing, distribution, javascript]
content_type: entity
domain: engineering
---

# npm — Publishing, Scoped Packages, and Provenance

## What It Is
The default package manager for JavaScript/Node.js. Publishing to npm makes your package installable with a single `npm install` command for millions of developers.

## Publishing Basics

```bash
# First-time setup
npm login

# Publish a public package
npm publish

# Publish a scoped package (must specify --access public)
npm publish --access public
```

## Scoped vs Unscoped Packages

| Type | Example | Default Visibility |
|------|---------|-------------------|
| Unscoped | `my-package` | Public |
| Scoped | `@myorg/my-package` | **Private** (must use `--access public`) |

Scoped packages are recommended for organizations. They namespace your packages and prevent name squatting.

## Trusted Publishing (Recommended)

As of 2025, npm supports trusted publishing via GitHub Actions. This eliminates long-lived tokens:

1. Go to npmjs.com > Package > Settings > Publishing Access
2. Configure a trusted publisher (link to your GitHub repo + workflow)
3. Your CI workflow publishes without storing npm tokens as secrets
4. Provenance attestations are generated automatically

Benefits: No long-lived secrets, tamper-evident builds, users can verify the package was built from your repo.

## Provenance

Provenance statements prove your package was built by your CI system from your source repo. Requires:
- npm CLI 9.5.0+
- A supported CI provider (GitHub Actions, GitLab CI)
- Cloud-hosted runner (not self-hosted)

```bash
# With trusted publishing, provenance is automatic
# With legacy tokens, add the flag:
npm publish --provenance
```

Packages with provenance show a green checkmark on npmjs.com.

## package.json Essentials

```json
{
  "name": "@myorg/my-tool",
  "version": "1.0.0",
  "description": "One-line description for npm search",
  "bin": { "my-tool": "./bin/cli.js" },
  "keywords": ["relevant", "searchable", "terms"],
  "license": "MIT",
  "repository": { "type": "git", "url": "https://github.com/myorg/my-tool" },
  "engines": { "node": ">=18" },
  "files": ["dist", "bin"]
}
```

## Important Rules

- npm package names are globally unique and permanent
- Unpublishing is restricted after 72 hours
- Use `npm pack --dry-run` to verify what files will be included
- Always test with `npm pack && npm install ./package.tgz` before publishing

## Last Updated
February 2026
