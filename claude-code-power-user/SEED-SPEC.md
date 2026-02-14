---
title: "Claude Code Power User Seed — Specification"
content_type: template
tags: [seed-spec, claude-code, knowledge-seed, hub-categories, entity-definitions]
domain: engineering
---

# Claude Code Power User Seed

## Metadata
- **Name**: claude-code-power-user
- **Type**: Knowledge
- **Audience**: Developers using Claude Code (or wanting to start). Vibe coders, AI-assisted devs, anyone who wants to get more out of their AI coding workflow.
- **Note count target**: 60-80
- **Agent build time**: 2-3 hours

## Purpose
Make any developer immediately productive with Claude Code. Covers workflows, hooks, MCP servers, keyboard shortcuts, prompt patterns, project setup, and common pitfalls. The seed every SAME user should start with.

## Hub Categories

1. hub/getting-started.md — Installation, first project setup, key concepts
2. hub/workflows.md — Daily workflows, when to use what, efficiency patterns
3. hub/hooks.md — Hook system, lifecycle, custom hooks, examples
4. hub/mcp-servers.md — MCP ecosystem, popular servers, configuration
5. hub/prompt-patterns.md — How to talk to Claude Code effectively
6. hub/project-setup.md — CLAUDE.md, .claude/ directory, settings, permissions
7. hub/troubleshooting.md — Common errors, debugging, performance

## Key Questions

1. How do I set up a new project with Claude Code?
2. What goes in CLAUDE.md and why does it matter?
3. How do hooks work (SessionStart, Stop, UserPromptSubmit, PreToolUse, PostToolUse)?
4. What are the best MCP servers to install?
5. How do I write effective prompts for coding tasks vs research vs debugging?
6. What are slash commands and how do I create custom ones?
7. How do I manage permissions and approve/deny tool use?
8. What keyboard shortcuts speed up my workflow?
9. How do I use Claude Code with git effectively (commits, PRs, branches)?
10. How do I handle large codebases (context management, file references)?
11. What are common mistakes that waste tokens?
12. How do I use plan mode vs direct execution?
13. How do I set up multi-file editing workflows?
14. What's the difference between Opus, Sonnet, and Haiku for different tasks?
15. How do I use Claude Code for debugging (error messages, stack traces)?
16. How do I integrate with VS Code, Cursor, or other editors?
17. How do I use background tasks and parallel agents?
18. What are the best practices for CLAUDE.md governance files?
19. How do I use Claude Code for code review?
20. How do I batch operations to reduce token usage?

## Entities

1. entities/claude-code.md — Current version, key capabilities, model options
2. entities/mcp-protocol.md — What MCP is, how it works, stdio vs SSE
3. entities/hooks-system.md — Available hook points, execution model
4. entities/claude-md.md — Purpose, structure, best practices
5. entities/context-window.md — Token limits, compaction, how to manage

## Decisions to Pre-Populate

1. AD-001: Use CLAUDE.md for project governance — keeps instructions version-controlled and shareable
2. AD-002: Hook-based automation over manual workflows — SessionStart for orientation, Stop for handoffs
3. AD-003: MCP for search, hooks for automation — different tools for different jobs
4. AD-004: Batch prompts over rapid-fire — fewer, more detailed prompts save tokens and get better results

## CLAUDE.md Governance

- This is a knowledge seed — browse and search, don't modify research notes
- Use SAME search to find answers before asking Claude to generate from scratch
- When you learn something new about Claude Code, suggest adding it to the seed
- hub/ notes are entry points, research/ notes have detail

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "welcome"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  max_token_budget = 1200
  max_results = 4
  composite_threshold = 0.65
```

## Why This Seed First

- **Our exact audience** — everyone using SAME is using Claude Code (or similar)
- **Best demo of SAME** — "search for how hooks work" and get a perfect answer instantly
- **Zero domain expertise needed** — we know Claude Code deeply
- **Cross-promotes SAME** — the seed teaches Claude Code AND demonstrates SAME working
- **Free forever** — pure adoption play
