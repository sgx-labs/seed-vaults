---
title: "How Do I Document My API or CLI?"
tags: [api, cli, documentation, reference, developer-experience]
content_type: research
domain: engineering
---

# How Do I Document My API or CLI?

## The Question

How do you write API and CLI documentation that developers can actually use without reading the source code?

## CLI Documentation

### Command Reference Format

For each command, document:

```markdown
## your-tool search

Search for notes matching a query.

### Usage

your-tool search [query] [flags]

### Flags

| Flag | Short | Default | Description |
|------|-------|---------|-------------|
| --limit | -l | 10 | Maximum results to return |
| --format | -f | text | Output format (text, json, table) |
| --verbose | -v | false | Show detailed match information |

### Examples

# Basic search
your-tool search "authentication patterns"

# JSON output for scripting
your-tool search "auth" --format json --limit 5

# Verbose output with scores
your-tool search "auth" -v
```

### CLI Documentation Principles

1. **Every flag needs a default value** — users should know what happens without flags
2. **Every command needs an example** — show the command AND the expected output
3. **Group related commands** — use subcommand groups (e.g., `your-tool vault list`)
4. **Help text IS documentation** — make `--help` output clear and complete

## API Documentation (Libraries)

### Function Reference Format

For each public function, document:

```markdown
## searchNotes(query, options?)

Search the knowledge base for matching notes.

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| query | string | Yes | Natural language search query |
| options.limit | number | No | Max results (default: 10) |
| options.threshold | number | No | Min relevance score 0-1 (default: 0.5) |

### Returns

`Promise<SearchResult[]>` — Array of matching notes sorted by relevance.

### Example

const results = await searchNotes("authentication", { limit: 5 });
for (const result of results) {
  console.log(result.title, result.score);
}
```

### API Documentation Principles

1. **Document every public function** — If it is exported, it needs docs
2. **Show the types** — Parameter types, return types, error types
3. **Include error cases** — What exceptions can be thrown and why
4. **Working examples** — Every example should run if copy-pasted

## Auto-Generated Docs

Many tools can generate API docs from code comments:
- **TypeDoc** — TypeScript
- **JSDoc** — JavaScript
- **GoDoc** — Go (automatically from code comments)
- **Sphinx** — Python (with docstrings)
- **RustDoc** — Rust (built into cargo)

Auto-generated docs are a good baseline. Supplement with hand-written guides for complex workflows.

## Related

- `hub/docs.md` — Documentation strategy overview
- `research/docs/documentation-tools.md` — Tools for building doc sites
