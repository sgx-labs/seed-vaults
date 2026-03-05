# Rust Devcontainer Setup

## Dockerfile

```dockerfile
FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    jq curl wget zsh ripgrep fd-find bat fzf tree htop \
    && rm -rf /var/lib/apt/lists/*

RUN echo "umask 077" >> /etc/profile.d/dev-security.sh
USER vscode
WORKDIR /workspace
```

## devcontainer.json Features

```json
{
  "features": {
    "ghcr.io/devcontainers/features/rust:1": { "version": "latest" },
    "ghcr.io/devcontainers/features/git:1": {}
  }
}
```

## VS Code Extensions

```json
{
  "extensions": [
    "rust-lang.rust-analyzer"
  ]
}
```

## post-create.sh

```bash
#!/bin/bash
set -e
if [ -f /workspace/Cargo.toml ]; then
  cd /workspace && cargo build 2>/dev/null || true
fi
```

## Tips

- `build-essential` is needed for Rust crates that use C FFI
- rust-analyzer provides inline type hints, completion, and diagnostics
- Pre-build in post-create to avoid slow first compile in the editor
