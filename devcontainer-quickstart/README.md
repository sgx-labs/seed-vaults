# Devcontainer Quickstart — SAME Seed Vault

> **Status: Work in Progress** — This seed is functional but still being expanded.

Set up secure, reproducible dev environments with VS Code devcontainers.

## What's Inside

- 12 notes covering getting started, language-specific configs, multi-repo workspaces, and security hardening
- Guides for Go, Node, Python, and Rust devcontainer setups
- Reference docs for devcontainer.json options, Dockerfile patterns, and extensions
- Troubleshooting guide for common issues

## Who It's For

Developers setting up containerized workspaces with VS Code devcontainers, from first-time setup to advanced multi-repo configurations.

## Prerequisites

- [SAME](https://github.com/sgx-labs/statelessagent) installed
- An embedding provider configured (Ollama, OpenRouter, or any OpenAI-compatible API)

## Install

```bash
same seed install devcontainer-quickstart
```

Or clone manually:

```bash
git clone https://github.com/sgx-labs/seed-vaults.git
cd seed-vaults/devcontainer-quickstart
same init
same reindex
```

## Getting Started

1. Run `same init` to configure your embedding provider
2. Run `same reindex` to index all seed notes
3. Start a conversation with your AI agent — the bootstrap note will guide first-session onboarding
4. Ask about your specific language or framework setup

## Directory Structure

```
devcontainer-quickstart/
  bootstrap.md          # Pinned onboarding note
  guides/               # Step-by-step setup guides
  reference/            # Configuration reference docs
  templates/            # Devcontainer templates
```

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
