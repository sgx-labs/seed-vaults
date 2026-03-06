---
title: "Install SAME"
tags: [install, setup, getting-started, brew, npm, binary, go]
content_type: guide
domain: same-getting-started
---

# Install SAME

## Recommended: Install Script

Works on macOS and Linux. Downloads the latest binary for your platform:

```bash
curl -fsSL https://statelessagent.com/install.sh | bash
```

Verify:

```bash
same --version
```

## npm (All Platforms)

If you have Node.js installed:

```bash
npm install -g @sgx-labs/same
```

This works on macOS, Linux, and Windows.

## Windows PowerShell

```powershell
irm https://statelessagent.com/install.ps1 | iex
```

## Build from Source

Requires Go 1.25+ and CGO_ENABLED=1:

```bash
git clone --depth 1 https://github.com/sgx-labs/statelessagent.git
cd statelessagent
make install
```

The binary is installed to `$GOPATH/bin` or `/usr/local/bin`.

## Docker

```bash
git clone --depth 1 https://github.com/sgx-labs/statelessagent.git
cd statelessagent
docker build -t same .
```

## After Installing

### 1. Initialize Your First Vault

```bash
cd ~/my-project    # or any folder with markdown notes
same init
```

`same init` walks you through setup:
- Vault location
- Embedding provider (Ollama, OpenAI, or none)
- Hook installation for your AI tool

### 2. (Optional) Set Up Ollama for Semantic Search

SAME works without Ollama using keyword search. But for semantic search (understanding meaning, not just words), install Ollama:

```bash
# macOS
brew install ollama

# Linux
curl -fsSL https://ollama.com/install.sh | sh

# Pull the default embedding model
ollama pull nomic-embed-text
```

Then re-run `same init` or edit `.same/config.toml` to set the provider to "ollama".

### 3. Try the Demo

```bash
same demo
```

This creates a temporary vault with sample notes and walks you through search, ask, and other features. Great for seeing SAME in action before setting up your own vault.

### 4. Run Diagnostics

```bash
same doctor
```

This runs 19 checks to verify your installation is working correctly. If anything is wrong, it tells you exactly what to fix.

## Updating SAME

```bash
same update
```

Or reinstall with the same method you used originally.

## Troubleshooting

**"Command not found"** -- Make sure the binary is in your PATH. Try `which same` or check `$GOPATH/bin`.

**"No vault found"** -- Run `same init` from inside your notes folder, or set `VAULT_PATH=/path/to/notes`.

**"Ollama not responding"** -- SAME falls back to keyword search automatically. If you want semantic search, make sure Ollama is running: `ollama serve`.

**Database issues** -- Run `same repair` to back up and rebuild the database.

## Next Steps

- Create your first vault: `hub/your-first-vault.md`
- Connect to Claude Code: `guides/connect-to-claude-code.md`
- Understand the configuration: `entities/config-toml.md`
