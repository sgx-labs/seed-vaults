# Python Devcontainer Setup

## Dockerfile

```dockerfile
FROM mcr.microsoft.com/devcontainers/base:ubuntu-24.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    python3-venv python3-pip \
    jq curl wget zsh ripgrep fd-find bat fzf tree htop \
    && rm -rf /var/lib/apt/lists/*

# Install uv (fast Python package manager)
RUN curl -LsSf https://astral.sh/uv/install.sh | sh

RUN echo "umask 077" >> /etc/profile.d/dev-security.sh
USER vscode
WORKDIR /workspace
```

## devcontainer.json Features

```json
{
  "features": {
    "ghcr.io/devcontainers/features/python:1": { "version": "3.12" },
    "ghcr.io/devcontainers/features/git:1": {}
  }
}
```

## VS Code Extensions

```json
{
  "extensions": [
    "ms-python.python",
    "ms-python.vscode-pylance",
    "charliermarsh.ruff"
  ]
}
```

## post-create.sh

```bash
#!/bin/bash
set -e
if [ -f /workspace/pyproject.toml ]; then
  cd /workspace && uv sync 2>/dev/null || pip install -e ".[dev]" 2>/dev/null || true
elif [ -f /workspace/requirements.txt ]; then
  cd /workspace && pip install -r requirements.txt
fi
```

## Tips

- Use `uv` for fast dependency management (10-100x faster than pip)
- Pin Python version in the feature config
- Use `ruff` instead of flake8+black for faster linting and formatting
