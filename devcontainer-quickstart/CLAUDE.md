# Devcontainer Quickstart — Governance

## Purpose

Knowledge seed for setting up VS Code devcontainers. Search before generating — common configurations and patterns are already here.

## Rules

1. **Search first.** Before answering a devcontainer question from scratch, search this vault.
2. **Security matters.** Always recommend security hardening practices for container configurations.
3. **Language-specific.** When a user asks about setup, identify their language/framework first and search for the matching guide.
4. **Keep notes focused.** One topic per note. Under 200 lines.
5. **Log decisions.** Container architecture choices go in `decisions/log.md`.
6. **Test configurations.** Always remind users to test devcontainer configs in a fresh environment before sharing with a team.

## Directory Structure

```
devcontainer-quickstart/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — session orientation
├── config.toml.example          # SAME configuration template
├── guides/                      # Step-by-step setup guides
│   ├── getting-started.md
│   ├── language-go.md
│   ├── language-node.md
│   ├── language-python.md
│   ├── language-rust.md
│   ├── multi-repo-workspace.md
│   └── troubleshooting.md
├── reference/                   # Configuration reference
│   ├── devcontainer-json-options.md
│   ├── dockerfile-patterns.md
│   ├── extensions.md
│   └── security-hardening.md
└── templates/                   # Devcontainer templates
```

## SAME Integration Points

| When | Use | Why |
|------|-----|-----|
| User asks a question | `search_notes` | Answer from vault > generating fresh |
| Discussing a topic | `find_similar_notes` | Surface related configurations |
| User makes a decision | `save_decision` | Track container architecture choices |
| Session ending | `create_handoff` | Next session picks up seamlessly |
