---
title: "How Do I Set Up npm, Homebrew, and Binary Distribution?"
tags: [distribution, npm, homebrew, binaries, packaging, releases]
content_type: research
domain: engineering
---

# How Do I Set Up npm, Homebrew, and Binary Distribution?

## The Question

How do you distribute your project so users can install it with a single command, regardless of their platform?

## Distribution Strategy

Choose based on your project type:

| Project Type | Primary Distribution | Secondary |
|-------------|---------------------|-----------|
| Node.js CLI/library | npm | Homebrew, GitHub Releases |
| Go CLI | Homebrew + GitHub Releases | go install |
| Rust CLI | crates.io + GitHub Releases | Homebrew |
| Python CLI/library | PyPI | Homebrew, pipx |
| Any compiled binary | GitHub Releases | Homebrew, system packages |

## npm Distribution

For JavaScript/TypeScript projects. See `entities/npm.md` for details.

```bash
# Publish publicly
npm publish --access public

# With provenance (recommended)
npm publish --provenance --access public
```

Automate with GitHub Actions:
```yaml
- uses: actions/setup-node@v4
  with:
    registry-url: 'https://registry.npmjs.org'
- run: npm publish --access public
  env:
    NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

Better: Use npm trusted publishing to avoid storing tokens.

## Homebrew Distribution

For macOS/Linux CLI tools. See `entities/homebrew.md` for details.

1. Create a tap repo: `github.com/yourorg/homebrew-tap`
2. Add a formula in `Formula/your-tool.rb`
3. Users install with: `brew install yourorg/tap/your-tool`
4. Automate formula updates with GoReleaser or a GitHub Action

## GitHub Releases (Binary Distribution)

Works for any compiled language. Upload pre-built binaries as release assets.

Naming convention:
```
your-tool-v1.2.0-linux-amd64.tar.gz
your-tool-v1.2.0-linux-arm64.tar.gz
your-tool-v1.2.0-darwin-amd64.tar.gz
your-tool-v1.2.0-darwin-arm64.tar.gz
your-tool-v1.2.0-windows-amd64.zip
```

Always include:
- Checksums file (SHA256)
- Installation instructions in the release notes
- Separate archives for each platform

## Install Script

A curl-pipe-bash install script for quick installs:

```bash
curl -fsSL https://get.your-project.dev | sh
```

The script should:
1. Detect OS and architecture
2. Download the correct binary from GitHub Releases
3. Verify checksum
4. Move to a standard location (e.g., `/usr/local/bin`)
5. Print a success message with next steps

Note: Some users distrust curl-pipe-bash scripts. Always provide alternative install methods.

## GoReleaser (Go Projects)

GoReleaser automates everything: cross-compilation, checksums, GitHub Releases, Homebrew formula updates, Docker images.

```yaml
# .goreleaser.yml
builds:
  - binary: your-tool
    goos: [linux, darwin, windows]
    goarch: [amd64, arm64]

archives:
  - format: tar.gz
    format_overrides:
      - goos: windows
        format: zip

brews:
  - repository:
      owner: yourorg
      name: homebrew-tap
    directory: Formula
```

Run: `goreleaser release --clean` on tag push.

## Related

- `entities/npm.md` — npm publishing details
- `entities/homebrew.md` — Homebrew tap and formula details
- `research/releases/github-actions-ci.md` — CI/CD automation
- `hub/releases.md` — Release workflow overview
