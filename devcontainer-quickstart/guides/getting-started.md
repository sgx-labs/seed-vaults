# Getting Started with Devcontainers

## Prerequisites

1. **Docker Desktop** or **OrbStack** (macOS) running
2. **VS Code** with the "Dev Containers" extension (`ms-vscode-remote.remote-containers`)
3. A project repository

## Setup Flow

### Option A: Use the SAME init script

```bash
cd ~/my-project
bash same-devcontainer-init.sh --lang go --name "My Project"
code .
# Cmd+Shift+P > "Dev Containers: Reopen in Container"
```

The script auto-detects your language and generates all config files.

### Option B: Manual setup

Create `.devcontainer/` in your project root with three files:

1. `Dockerfile` - base image + system dependencies
2. `devcontainer.json` - VS Code config, extensions, mounts
3. `post-create.sh` - one-time setup (install tools, deps)

Then open in VS Code and use "Reopen in Container".

## Directory Structure

```
my-project/
  .devcontainer/
    Dockerfile            # Container image definition
    devcontainer.json     # VS Code + container config
    post-create.sh        # Post-create setup script
  .editorconfig           # Consistent formatting
  .gitattributes          # Line ending normalization
  .gitignore              # Security patterns
```

## First Time vs Subsequent Opens

- **First time**: "Reopen in Container" builds the Docker image (~2-3 min)
- **Subsequent**: "Reopen in Container" starts instantly (cached image)
- **After config changes**: Use "Rebuild and Reopen in Container" to pick up changes
