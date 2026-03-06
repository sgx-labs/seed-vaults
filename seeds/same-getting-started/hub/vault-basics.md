---
title: "Vault Basics"
tags: [vault, structure, directory, markdown, notes, organization]
content_type: hub
domain: same-getting-started
---

# Vault Basics

## What is a Vault?

A vault is just a folder of markdown files that SAME indexes and makes searchable. That's it. No special format required, no proprietary file types. If you can write a `.md` file, you can use SAME.

Your vault lives alongside your project. When you run `same init` in a project folder, SAME creates a `.same/` directory with its config and database. Your markdown files stay exactly where they are.

## Directory Structure

A typical vault looks like this:

```
my-project/
  .same/              # SAME's config and database (auto-created)
    config.toml       # Your settings
    same.db           # SQLite database with embeddings
  hub/                # Category overviews -- start here for any topic
  research/           # Detailed guides and deep dives
  entities/           # Quick references for tools, concepts, services
  decisions/          # Architectural decision records
  sessions/           # Session handoffs for continuity
  _archive/           # Archived originals (skipped by indexer)
  _Raw/               # Unprocessed source material (skipped by indexer)
  CLAUDE.md           # Instructions for AI agents
  bootstrap.md        # Pinned onboarding note
```

You don't need all of these. Even a single markdown file is a valid vault.

## How Notes Work

Every note is a markdown file. SAME reads the content, generates an embedding (a numerical representation of meaning), and stores it in SQLite. When you or your AI searches, SAME finds the notes whose embeddings are closest to your query.

### Frontmatter (Optional but Recommended)

Add YAML frontmatter to help SAME organize and filter your notes:

```markdown
---
title: "Authentication Strategy"
tags: [auth, security, jwt, decisions]
content_type: research
domain: backend
---

# Authentication Strategy

We chose JWT with refresh tokens because...
```

The frontmatter fields:
- **title** -- Human-readable title
- **tags** -- Keywords for filtering searches
- **content_type** -- hub, research, entity, decision, guide
- **domain** -- Topic area (backend, frontend, devops, etc.)

### Pinned Notes

Some notes should always be loaded at session start. Mark them with `pinned: true` in frontmatter:

```yaml
---
title: "Project Overview"
pinned: true
---
```

Pinned notes get included in every session's context via `get_session_context`.

## Special Directories

| Directory | Indexed? | Purpose |
|-----------|:--------:|---------|
| `_archive/` | No | Old versions of files, safely stored |
| `_Raw/` | No | Unprocessed source material |
| `_PRIVATE/` | No | Sensitive files (API keys, credentials) |
| `sessions/` | Yes | Session handoff notes |
| `decisions/` | Yes | Decision records |

Configure skip directories in `.same/config.toml`:

```toml
[vault]
  skip_dirs = ["_Raw", "_archive", "_PRIVATE"]
```

## Working with Your Vault

```bash
# See what SAME is tracking
same status

# Search your notes
same search "authentication decision"

# Ask a question and get cited answers
same ask "what did we decide about the database?"

# Rebuild the index after adding new notes
same reindex
```

## Next Steps

- Create your first vault: `hub/your-first-vault.md`
- Learn about searching: `hub/search-and-discovery.md`
- Understand session memory: `hub/session-memory.md`
