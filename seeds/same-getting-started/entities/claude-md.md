---
title: "CLAUDE.md"
tags: [claude-md, governance, agent-instructions, configuration, rules]
content_type: entity
domain: same-getting-started
---

# CLAUDE.md

## What It Is

A `CLAUDE.md` file is a set of instructions that AI agents read at the start of every session. It tells the AI how to behave, what tools to use, what rules to follow, and how to interact with the user.

Originally designed for Claude Code, the pattern has been adopted broadly. SAME uses `CLAUDE.md` files in two ways:

1. **Project-level** -- In your project root, controlling how AI agents work on your code
2. **Seed-level** -- In each SeedVault, controlling how the AI uses that seed's knowledge

## Why It Matters

Without a CLAUDE.md, your AI agent starts every session with default behavior. It doesn't know your preferences, your project's conventions, or your workflow. With a CLAUDE.md, the AI:

- Searches your vault before generating answers
- Follows your coding standards
- Uses SAME's tools (search, decisions, handoffs) automatically
- Respects your project's architecture decisions

## What to Include

A good CLAUDE.md covers:

### Rules and Boundaries
```markdown
## Rules
- Always search the vault before generating answers
- Never overwrite files without asking
- Log decisions using `save_decision`
- Create handoffs at session end
```

### Project Context
```markdown
## Project
- Language: Go
- Database: PostgreSQL
- Deploy target: Fly.io
- Test framework: Go standard testing
```

### Workflow Preferences
```markdown
## Workflow
- Run tests before committing
- Use conventional commit messages
- Keep functions under 50 lines
```

### SAME Integration
```markdown
## SAME
- Use `search_notes` before answering knowledge questions
- Use `save_decision` when the user makes a project choice
- Use `create_handoff` at session end
- Use `find_similar_notes` to discover related context
```

## CLAUDE.md in Seeds

Every SeedVault includes a CLAUDE.md that tells the AI how to use that seed's knowledge. The seed's CLAUDE.md includes:

- The Research Cascade protocol (how to onboard)
- Search-first rules (always check the vault)
- Integration points (when to use which SAME tool)

When you install a seed, SAME's merge protocol ensures your existing CLAUDE.md isn't overwritten -- it archives the original and proposes a merged version.

## Multiple CLAUDE.md Files

You can have CLAUDE.md files at different levels:
- `~/CLAUDE.md` -- Global defaults
- `~/my-project/CLAUDE.md` -- Project-specific rules
- `~/my-project/.same/seeds/seed-name/CLAUDE.md` -- Seed-specific rules

Claude Code reads all of them, with more specific files taking precedence.

## Related Notes

- Configuration reference: `entities/config-toml.md`
- Session memory: `hub/session-memory.md`
- SeedVaults: `research/seed-vaults-deep-dive.md`
