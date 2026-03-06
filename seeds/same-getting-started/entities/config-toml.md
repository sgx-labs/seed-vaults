---
title: "config.toml"
tags: [config, configuration, toml, settings, embedding, vault]
content_type: entity
domain: same-getting-started
---

# config.toml

## What It Is

SAME's configuration file, located at `.same/config.toml` in your vault directory. Created automatically by `same init`, or you can write it manually.

## Location

```
my-project/
  .same/
    config.toml    # <-- this file
    same.db        # SQLite database (auto-managed)
```

## Full Reference

```toml
# -- Vault Settings ----------------------------------------------------------
[vault]
  path = "."                          # Where your notes are (relative to config)
  handoff_dir = "sessions"            # Where handoffs are stored
  decision_log = "decisions/log.md"   # Where decisions are logged
  skip_dirs = ["_Raw", "_archive", "_PRIVATE"]  # Directories to skip

# -- Embedding Provider -------------------------------------------------------
[embedding]
  provider = "ollama"                 # "ollama", "openai", "openai-compatible", "none"
  model = "nomic-embed-text"          # Model name for your provider

# Provider-specific settings:

# Ollama (local, free)
[ollama]
  url = "http://localhost:11434"

# OpenAI
# [embedding]
#   provider = "openai"
#   model = "text-embedding-3-small"
#   api_key = ""                      # Or use SAME_EMBED_API_KEY env var

# OpenRouter
# [embedding]
#   provider = "openai-compatible"
#   model = "qwen/qwen3-embedding-8b"
#   base_url = "https://openrouter.ai/api/v1"
#   api_key = ""

# No embeddings (keyword search only)
# [embedding]
#   provider = "none"

# -- Memory Settings ----------------------------------------------------------
[memory]
  max_token_budget = 1200             # Max tokens returned per search
  max_results = 4                     # Max notes returned per search
  composite_threshold = 0.65          # Minimum similarity score (0.0 to 1.0)
```

## Key Settings Explained

### `vault.path`
Where SAME looks for markdown files. Usually `"."` (current directory).

### `vault.skip_dirs`
Directories SAME won't index. Always include `_archive` and any directories with sensitive data.

### `embedding.provider`
Your embedding provider. Options:
- `"ollama"` -- Local, free, requires Ollama installed
- `"openai"` -- OpenAI's API, requires API key
- `"openai-compatible"` -- Any OpenAI-compatible API (OpenRouter, etc.)
- `"none"` -- Keyword search only, no dependencies

### `memory.max_results`
How many notes to return per search. Lower values mean more focused results. Higher values mean broader coverage. Default: 4.

### `memory.composite_threshold`
Minimum similarity score for a note to be included in results. Lower values return more results (including less relevant ones). Higher values return fewer but more precise results. Default: 0.65.

## Configuration Priority

Settings are resolved in this order (highest wins):
1. CLI flags (`--vault`, `--model`, etc.)
2. Environment variables (`SAME_EMBED_API_KEY`, `VAULT_PATH`, etc.)
3. Config file (`.same/config.toml`)
4. Defaults

## Common Recipes

### Minimal (keyword search, no dependencies)
```toml
[vault]
  path = "."

[embedding]
  provider = "none"
```

### Local with Ollama
```toml
[vault]
  path = "."
  skip_dirs = ["_archive"]

[embedding]
  provider = "ollama"
  model = "nomic-embed-text"

[ollama]
  url = "http://localhost:11434"
```

## Related Notes

- Install SAME: `guides/install-same.md`
- Vault structure: `hub/vault-basics.md`
- MCP server: `entities/mcp-server.md`
