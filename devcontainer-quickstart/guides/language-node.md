# Node.js Devcontainer Setup

## devcontainer.json Features

```json
{
  "features": {
    "ghcr.io/devcontainers/features/node:1": { "version": "22" },
    "ghcr.io/devcontainers/features/git:1": {}
  }
}
```

## VS Code Extensions

```json
{
  "extensions": [
    "dbaeumer.vscode-eslint",
    "esbenp.prettier-vscode"
  ]
}
```

## post-create.sh

```bash
#!/bin/bash
set -e
if [ -f /workspace/package.json ]; then
  cd /workspace && npm install
fi
```

## Tips

- Pin Node version in the feature config for reproducibility
- Use `npm ci` instead of `npm install` in CI-like setups
- Forward common ports (3000, 5173, 8080) in devcontainer.json
