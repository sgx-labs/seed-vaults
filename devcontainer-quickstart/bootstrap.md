# Devcontainer Quickstart

A knowledge vault for setting up secure, reproducible development environments using VS Code devcontainers.

## What's Inside

- **Guides**: Step-by-step setup for Go, Node, Python, and Rust projects
- **Reference**: Dockerfile patterns, devcontainer.json options, security hardening
- **Templates**: Ready-to-use configurations you can copy into any project

## Quick Start

After installing this seed:
```bash
same search "how to set up a Go devcontainer"
same search "devcontainer security best practices"
same search "multi-repo workspace setup"
```

## Key Concepts

- Devcontainers run your dev environment in Docker, ensuring consistency across machines
- VS Code's "Dev Containers" extension manages the container lifecycle
- Mount host directories for persistent config (`.same/`, `.claude/`)
- Set `umask 077` for secure file creation defaults
- Use `.gitattributes` with `eol=lf` to prevent line ending issues
