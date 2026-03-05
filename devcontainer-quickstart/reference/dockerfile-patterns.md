# Dockerfile Patterns for Devcontainers

## Base Image

Use Microsoft's devcontainer base images for best compatibility:

```dockerfile
FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04
```

## System Dependencies

Install all packages in one layer, clean up after:

```dockerfile
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential gcc \
    jq curl wget zsh \
    ripgrep fd-find bat fzf tree htop \
    && rm -rf /var/lib/apt/lists/*
```

## Common Tool Installs

### golangci-lint (Go)
```dockerfile
RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh \
    | sh -s -- -b /usr/local/bin latest
```

### uv (Python)
```dockerfile
RUN curl -LsSf https://astral.sh/uv/install.sh | sh
```

## Dev Tools

These tools improve the container shell experience:

| Tool | Package | Purpose |
|------|---------|---------|
| ripgrep | `ripgrep` | Fast recursive search |
| fd | `fd-find` | Fast file finder |
| bat | `bat` | cat with syntax highlighting |
| fzf | `fzf` | Fuzzy finder |
| tree | `tree` | Directory tree view |
| htop | `htop` | Process viewer |
| jq | `jq` | JSON processor |
| zstd | `zstd` | Fast compression |

## Layer Ordering

Order Dockerfile instructions from least to most frequently changing:

1. Base image
2. System packages (rarely changes)
3. Tool installs (occasionally changes)
4. Security config (rarely changes)
5. User switch and workdir (never changes)

This maximizes Docker layer cache hits.
