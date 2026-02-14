---
title: "Homebrew — Tap Setup, Formula Writing, and Updates"
tags: [homebrew, entity, distribution, macos, linux, tap, formula, goreleaser]
content_type: entity
domain: engineering
---

# Homebrew — Tap Setup, Formula Writing, and Updates

## What It Is
The dominant package manager for macOS (and available on Linux). Users install your tool with `brew install yourorg/tap/yourtool`. Essential distribution channel for CLI tools.

## Tap Setup

A tap is a GitHub repository that contains Homebrew formulae. Your repo must be named `homebrew-<name>`.

### Create Your Tap

1. Create a GitHub repo named `homebrew-tap` (or `homebrew-tools`, etc.)
2. Add a `Formula/` directory
3. Users install with: `brew install yourorg/tap/yourtool`

Repository structure:
```
homebrew-tap/
├── Formula/
│   └── yourtool.rb
└── README.md
```

## Writing a Formula

```ruby
class Yourtool < Formula
  desc "One-line description of your tool"
  homepage "https://github.com/yourorg/yourtool"
  url "https://github.com/yourorg/yourtool/releases/download/v1.0.0/yourtool-1.0.0.tar.gz"
  sha256 "abc123..."  # shasum -a 256 yourtool-1.0.0.tar.gz
  license "MIT"

  depends_on "go" => :build  # build dependencies

  def install
    system "go", "build", "-o", bin/"yourtool", "."
  end

  test do
    assert_match "yourtool version", shell_output("#{bin}/yourtool --version")
  end
end
```

## Pre-Built Binaries (Bottles)

For faster installs, provide pre-built binaries:
- Build for each target platform in CI
- Upload as GitHub Release assets
- Reference in your formula's `bottle` block

Common targets: `arm64_sonoma` (Apple Silicon), `sonoma` (Intel Mac), `x86_64_linux`.

## Automating Updates with GoReleaser

GoReleaser can automatically update your Homebrew formula on each release:

```yaml
# .goreleaser.yml
brews:
  - repository:
      owner: yourorg
      name: homebrew-tap
    directory: Formula
    homepage: "https://github.com/yourorg/yourtool"
    description: "Your tool description"
```

## Getting Into homebrew-core

For wider distribution, submit your formula to `homebrew-core`:
- Your tool must be notable (significant users, not just personal projects)
- Must have a stable versioning scheme
- Must build from source
- Submit a PR to `Homebrew/homebrew-core`

Most projects start with their own tap and graduate to homebrew-core later.

## Common Issues

- **SHA mismatch** — Re-download the tarball, recompute the hash
- **Dependencies** — Declare all build and runtime deps
- **License** — Must be an OSI-approved license
- **Tests** — Formula must include a working test block

## Last Updated
February 2026
