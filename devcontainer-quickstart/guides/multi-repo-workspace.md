# Multi-Repo Workspaces

## Problem

Many projects span multiple repositories. You want one container that gives you access to all of them with color-coded terminals and shared tools.

## Solution: VS Code Multi-Root Workspace

### 1. Mount sibling repos in devcontainer.json

```json
{
  "workspaceFolder": "/workspace",
  "workspaceMount": "source=${localWorkspaceFolder}/..,target=/workspace/code,type=bind",
  "mounts": [
    "source=${localEnv:HOME}/Projects/my-company,target=/workspace/company,type=bind",
    "source=${localEnv:HOME}/.config,target=/home/vscode/.config,type=bind"
  ]
}
```

### 2. Create a .code-workspace file

```json
{
  "folders": [
    { "name": "backend (core)", "path": "/workspace/code/backend" },
    { "name": "frontend (web)", "path": "/workspace/code/frontend" },
    { "name": "company (HQ)", "path": "/workspace/company" }
  ],
  "settings": {},
  "tasks": {
    "version": "2.0.0",
    "tasks": [
      {
        "label": "Build backend",
        "type": "shell",
        "command": "make build",
        "options": { "cwd": "/workspace/code/backend" }
      }
    ]
  }
}
```

### 3. Open inside the container

- File > Open Workspace from File
- Pick the `.code-workspace` file
- Each repo gets its own root in the sidebar

## Color-Coded Terminals

Add terminal profiles in devcontainer.json to distinguish repos:

```json
"terminal.integrated.profiles.linux": {
  "backend": {
    "path": "zsh",
    "icon": "server",
    "color": "terminal.ansiGreen"
  },
  "frontend": {
    "path": "zsh",
    "icon": "globe",
    "color": "terminal.ansiCyan"
  }
}
```
