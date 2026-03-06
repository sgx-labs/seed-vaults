---
title: "Devcontainer Quickstart Seed Specification"
content_type: hub
tags: [seed-spec, devcontainer, docker, vscode, developer-environment]
domain: devcontainer-quickstart
---

# Devcontainer Quickstart Seed

## Metadata
- **Name**: devcontainer-quickstart
- **Type**: Knowledge
- **Audience**: Developers setting up containerized workspaces
- **Note count target**: 30-50
- **Status**: Work in Progress

## Purpose

Help developers set up secure, reproducible dev environments using VS Code devcontainers. Covers language-specific configurations, security hardening, multi-repo workspaces, and troubleshooting.

## Hub Categories

1. guides/getting-started.md — First-time devcontainer setup
2. guides/language-*.md — Language-specific configurations
3. reference/devcontainer-json-options.md — Configuration reference
4. reference/security-hardening.md — Security best practices

## Key Questions

1. How do I set up a devcontainer for the first time?
2. What are the best practices for Go/Node/Python/Rust devcontainers?
3. How do I configure multi-repo workspaces?
4. What security hardening should I apply to devcontainers?
5. How do I troubleshoot common devcontainer issues?
6. What VS Code extensions should I include?
7. What Dockerfile patterns work best for dev environments?

## Quality Checklist
- [x] bootstrap.md with orientation
- [x] Guides for major languages
- [x] Reference documentation
- [ ] YAML frontmatter on all notes
- [ ] Entity files for tools and platforms
- [ ] Decision log
- [ ] Session handoff support
- [ ] 30+ notes covering full scope
