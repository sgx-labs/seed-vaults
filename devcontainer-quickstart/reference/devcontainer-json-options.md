# devcontainer.json Reference

## Essential Fields

```json
{
  "name": "Project Dev",
  "build": {
    "dockerfile": "Dockerfile",
    "context": ".."
  },
  "workspaceFolder": "/workspace",
  "remoteUser": "vscode"
}
```

## Features

Pre-built toolchains installed automatically:

```json
{
  "features": {
    "ghcr.io/devcontainers/features/go:1": { "version": "1.25" },
    "ghcr.io/devcontainers/features/node:1": { "version": "22" },
    "ghcr.io/devcontainers/features/python:1": { "version": "3.12" },
    "ghcr.io/devcontainers/features/rust:1": { "version": "latest" },
    "ghcr.io/devcontainers/features/git:1": {},
    "ghcr.io/devcontainers/features/github-cli:1": {}
  }
}
```

## Mounts

Bind-mount host directories into the container:

```json
{
  "workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind",
  "mounts": [
    "source=${localEnv:HOME}/.config,target=/home/vscode/.config,type=bind"
  ]
}
```

## Environment Variables

```json
{
  "containerEnv": {
    "SAME_ENV": "dev",
    "CGO_ENABLED": "1"
  }
}
```

## Port Forwarding

```json
{
  "forwardPorts": [3000, 5173, 8080],
  "portsAttributes": {
    "3000": { "label": "Web App", "onAutoForward": "openBrowser" },
    "8080": { "label": "API", "onAutoForward": "silent" }
  }
}
```

## Post-Create Command

Runs once after the container is first created:

```json
{
  "postCreateCommand": "bash .devcontainer/post-create.sh"
}
```

## VS Code Customizations

Extensions and settings specific to this container:

```json
{
  "customizations": {
    "vscode": {
      "extensions": ["golang.go", "eamodio.gitlens"],
      "settings": {
        "editor.fontSize": 14
      }
    }
  }
}
```
