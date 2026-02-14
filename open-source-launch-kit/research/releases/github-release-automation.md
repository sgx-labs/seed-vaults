---
title: "How Do I Automate the Full Release Process?"
tags: [release-automation, github-actions, ci-cd, goreleaser, semantic-release]
content_type: research
domain: engineering
---

# How Do I Automate the Full Release Process?

## The Question

Manually building binaries, updating changelogs, creating GitHub releases, and publishing to package managers is tedious and error-prone. How do you automate the entire process?

## The Release Pipeline

A fully automated release does this when you push a tag:

1. Run tests (one final check)
2. Build for all target platforms
3. Generate or finalize changelog
4. Create GitHub Release with artifacts
5. Publish to package managers (npm, Homebrew, PyPI)
6. Post release announcement (optional)

## Option 1: Tag-Triggered Workflow (Manual Tag)

You decide when to release by pushing a tag. CI handles the rest.

```bash
# Update CHANGELOG.md manually
# Bump version in package.json / go.mod / etc.
git add -A
git commit -m "Release v1.3.0"
git tag -a v1.3.0 -m "v1.3.0"
git push origin main v1.3.0
# CI takes over from here
```

This is the recommended approach for most projects. You control the timing and changelog content.

## Option 2: release-please (Google)

Fully automated versioning and changelog from conventional commits.

How it works:
1. You merge PRs with conventional commit messages (`feat:`, `fix:`, etc.)
2. release-please opens a "Release PR" that bumps the version and updates CHANGELOG
3. You merge the Release PR when ready
4. release-please creates the tag and GitHub Release

```yaml
# .github/workflows/release-please.yml
name: release-please
on:
  push:
    branches: [main]

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          release-type: node  # or: go, python, etc.
```

## Option 3: semantic-release

Fully automated from commit to publish. No human intervention.

```yaml
# .github/workflows/release.yml
name: Release
on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npx semantic-release
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

Good for: Teams that want zero-touch releases.
Not ideal for: Solo maintainers who want to curate release notes.

## Option 4: GoReleaser (Go Projects)

Cross-compile, checksum, GitHub Release, Homebrew formula — all in one.

```yaml
# .goreleaser.yml
builds:
  - binary: your-tool
    goos: [linux, darwin, windows]
    goarch: [amd64, arm64]
    ldflags: -s -w -X main.version={{.Version}}

archives:
  - format: tar.gz
    format_overrides:
      - goos: windows
        format: zip

checksum:
  name_template: 'checksums.txt'

brews:
  - repository:
      owner: yourorg
      name: homebrew-tap
    directory: Formula
    homepage: "https://github.com/yourorg/your-tool"
    description: "Description here"
```

## Comparison

| Tool | Changelog | Version Bump | GitHub Release | npm Publish | Best For |
|------|-----------|-------------|----------------|-------------|----------|
| Manual tag | You write | You decide | Workflow | Workflow | Full control |
| release-please | Auto from commits | Auto | Auto | You add step | Curated auto |
| semantic-release | Auto from commits | Auto | Auto | Built-in | Zero-touch |
| GoReleaser | You write | You decide | Auto | N/A | Go projects |

## Recommendation

Start with **manual tag + CI workflow**. It is the simplest, gives you full control, and teaches you what each step does. Automate more once the manual process feels tedious.

## Related

- `research/releases/github-actions-ci.md` — CI/CD setup basics
- `research/releases/writing-changelogs.md` — Changelog best practices
- `hub/releases.md` — Release workflow overview
