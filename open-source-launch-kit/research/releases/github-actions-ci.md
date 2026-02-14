---
title: "How Do I Set Up GitHub Actions for Testing and Releases?"
tags: [github-actions, ci, cd, automation, testing, releases, workflows, matrix-builds]
content_type: research
domain: engineering
---

# How Do I Set Up GitHub Actions for Testing and Releases?

## The Question

How do you set up CI/CD that runs tests on every PR and automatically builds releases when you push a tag?

## CI Workflow (Tests on Every PR)

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        # Test on multiple versions if applicable
        node-version: [18, 20, 22]

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test
```

## Release Workflow (Build on Tag Push)

Create `.github/workflows/release.yml`:

```yaml
name: Release

on:
  push:
    tags:
      - 'v*'  # Triggers on v1.0.0, v2.1.3, etc.

permissions:
  contents: write  # Needed to create GitHub Releases

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install and build
        run: |
          npm ci
          npm run build

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
          files: |
            dist/*
```

## Multi-Platform Binary Builds (Go Example)

For Go projects that need cross-platform binaries:

```yaml
jobs:
  build:
    strategy:
      matrix:
        include:
          - os: ubuntu-latest
            goos: linux
            goarch: amd64
          - os: macos-latest
            goos: darwin
            goarch: arm64
          - os: windows-latest
            goos: windows
            goarch: amd64
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - name: Build
        env:
          GOOS: ${{ matrix.goos }}
          GOARCH: ${{ matrix.goarch }}
        run: go build -o your-tool-${{ matrix.goos }}-${{ matrix.goarch }} .
```

## Best Practices

1. **Cache dependencies** — Use `cache: 'npm'` or `actions/cache` for faster runs
2. **Pin action versions** — Use `@v4` not `@latest` for reproducibility
3. **Test on multiple OS** — At least ubuntu + macos if you support both
4. **Use matrix builds** — Test across versions in parallel
5. **Protect main branch** — Require CI to pass before merging
6. **Use secrets for tokens** — Settings > Secrets > Actions for API keys
7. **Keep workflows DRY** — Use reusable workflows for shared steps

## Common Actions

| Action | Purpose |
|--------|---------|
| `actions/checkout@v4` | Check out your code |
| `actions/setup-node@v4` | Install Node.js |
| `actions/setup-go@v5` | Install Go |
| `actions/cache@v4` | Cache dependencies |
| `softprops/action-gh-release@v2` | Create GitHub Releases |
| `actions/stale@v9` | Auto-close stale issues |

## Related

- `decisions/log.md` — AD-003 on choosing GitHub Actions
- `entities/github.md` — GitHub Actions free tier details
- `hub/releases.md` — Full release workflow
