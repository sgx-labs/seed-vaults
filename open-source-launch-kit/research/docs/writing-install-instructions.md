---
title: "How Do I Write Installation Instructions That Actually Work?"
tags: [installation, documentation, onboarding, setup, developer-experience, prerequisites, troubleshooting]
content_type: research
domain: engineering
---

# How Do I Write Installation Instructions That Actually Work?

## The Question

Your install instructions work on your machine. They fail for everyone else. How do you write install docs that work reliably for strangers?

## The Testing Rule

**Test every install command on a clean machine.** Not your development machine with 47 tools pre-installed. A clean Docker container, a fresh VM, or a coworker's laptop. If it fails there, it will fail for users.

```bash
# Quick test with Docker
docker run -it --rm ubuntu:latest bash
# Now try following your own install instructions
```

## Structure of Good Install Instructions

### 1. Prerequisites (List Everything)

Do not assume anything is installed. List every dependency with minimum versions.

```markdown
## Prerequisites

- Node.js 18+ (`node --version` to check)
- npm 9+ (comes with Node.js)
- Git (`git --version` to check)
```

### 2. Primary Install Method (Simplest First)

```markdown
## Installation

npm install -g your-project
```

One command. Copy-pasteable. If your install requires more than 3 commands, simplify it.

### 3. Alternative Install Methods

```markdown
### Homebrew (macOS)
brew install yourorg/tap/your-project

### From Source
git clone https://github.com/yourorg/your-project
cd your-project
make build
```

### 4. Verification

Always include a verification step so users know it worked.

```markdown
### Verify Installation
your-project --version
# Expected output: your-project v1.2.3
```

### 5. Troubleshooting

Add a section for common issues AFTER you discover them (not before).

```markdown
### Common Issues

**"command not found"** — Ensure the install directory is in your PATH.
Run `echo $PATH` and verify it includes the install location.

**Permission denied** — Do not use `sudo npm install -g`. Fix your npm
permissions instead: https://docs.npmjs.com/resolving-eacces-permissions-errors
```

## Platform-Specific Instructions

If your tool runs on multiple platforms, provide tabs or clear sections:

```markdown
### macOS
brew install yourorg/tap/your-project

### Linux
curl -fsSL https://get.your-project.dev | sh

### Windows
winget install yourorg.your-project
```

## Common Mistakes

1. **Untested instructions** — The most common failure. Always test on clean machines.
2. **Missing prerequisites** — "Obviously you need Python" is not obvious to everyone.
3. **Assuming sudo** — Never tell users to run `sudo npm install` or `sudo pip install`.
4. **No verification step** — Users do not know if the install succeeded.
5. **Stale instructions** — Update install docs when install methods change.
6. **Only one method** — Provide at least 2 install options.

## Related

- `research/docs/readme-that-converts.md` — README structure
- `research/launch/minimum-viable-oss-project.md` — MVP project checklist
- `hub/docs.md` — Documentation strategy
