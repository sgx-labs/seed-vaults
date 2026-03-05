# Go Devcontainer Setup

## Dockerfile

```dockerfile
FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential gcc sqlite3 libsqlite3-dev \
    jq curl wget zsh ripgrep fd-find bat fzf tree htop \
    && rm -rf /var/lib/apt/lists/*

# Install golangci-lint
RUN curl -sSfL https://raw.githubusercontent.com/golangci/golangci-lint/master/install.sh \
    | sh -s -- -b /usr/local/bin latest

RUN echo "umask 077" >> /etc/profile.d/dev-security.sh

USER vscode
WORKDIR /workspace
```

## devcontainer.json Features

```json
{
  "features": {
    "ghcr.io/devcontainers/features/go:1": { "version": "1.25" },
    "ghcr.io/devcontainers/features/git:1": {}
  },
  "containerEnv": {
    "CGO_ENABLED": "1",
    "GOPATH": "/home/vscode/go"
  }
}
```

## VS Code Go Settings

```json
{
  "go.testOnSave": true,
  "go.lintTool": "golangci-lint",
  "go.lintFlags": ["--fast"],
  "go.formatTool": "goimports",
  "go.useLanguageServer": true,
  "go.testFlags": ["-v", "-count=1"],
  "go.coverageDecorator": {
    "type": "gutter",
    "coveredGutterStyle": "blockgreen",
    "uncoveredGutterStyle": "blockred"
  }
}
```

## CGO Requirements

If your Go project uses CGO (e.g., SQLite via go-sqlite3):
- `CGO_ENABLED=1` must be set
- `build-essential` and `gcc` must be installed
- For SQLite: also install `sqlite3` and `libsqlite3-dev`

## post-create.sh

```bash
#!/bin/bash
set -e
if [ -f /workspace/go.mod ]; then
  cd /workspace && go mod download
fi
```
