---
title: "Built-in Slash Commands Reference"
tags: [slash-commands, cli, reference, productivity, compact, review, model, skills]
content_type: research
domain: engineering
---

# Built-in Slash Commands Reference

## What Are Slash Commands?

Slash commands are shortcuts you type directly into the Claude Code prompt. They start with `/` and perform common actions without needing a full natural language prompt. They are faster and more reliable than typing out instructions.

## Built-in Commands

### Session Management

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `/clear` | Clears the conversation history and starts fresh | When context is polluted or you want a clean slate |
| `/compact` | Summarizes the conversation to free context space | Between tasks, when the context window is getting full |
| `/cost` | Shows token usage and estimated cost for the session | Periodically, to track spend and build cost intuition |
| `/status` | Shows current session info (model, project, permissions) | When you need to confirm your current configuration |

### Setup and Configuration

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `/init` | Creates a CLAUDE.md file for the current project | First time setting up a project with Claude Code |
| `/model` | Shows or changes the active model (Opus, Sonnet, Haiku) | When you want to switch models mid-session |
| `/permissions` | Shows or modifies tool permissions | When you want to allow/deny specific tools |
| `/memory` | Shows or edits CLAUDE.md memory files | When you want to view or update project instructions |

### Authentication

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `/login` | Authenticates with Anthropic or your API provider | First time setup, or when your session expires |
| `/logout` | Clears authentication credentials | When switching accounts or for security |

### Development Tools

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `/review` | Reviews code changes (staged, unstaged, or specific commits) | Before committing, during code review |
| `/doctor` | Diagnoses common issues (config, permissions, connectivity) | When something is not working as expected |

### Interface

| Command | What It Does | When to Use |
|---------|-------------|-------------|
| `/help` | Shows available commands and usage info | When you forget what's available |
| `/vim` | Toggles vim keybindings for the input area | If you prefer vim-style editing |

## Command Details

### /compact — Context Window Management

The most important command for long sessions. When your conversation gets long, Claude's context window fills up. `/compact` summarizes everything so far into a condensed form, freeing space for new work.

```
# After finishing a feature, before starting a bug fix:
/compact

# With a custom focus for the summary:
/compact focus on the authentication changes we made
```

When to use:
- After completing a task and before starting a new one
- When Claude starts forgetting earlier context
- When you see compaction warnings

### /review — Code Review

Reviews your current changes against the working tree or specific commits.

```
# Review all uncommitted changes:
/review

# Claude will look at git diff, analyze changes, and provide feedback
# on bugs, security issues, style, and improvement opportunities.
```

This is a shortcut for asking Claude to review your changes manually. It automatically pulls the right diffs and presents structured feedback.

### /model — Switch Models Mid-Session

```
/model

# Shows current model and lets you switch:
# - Opus 4.6: Complex tasks, multi-file architecture
# - Sonnet 4.5: Daily coding, balanced
# - Haiku 4.5: Quick lookups, simple tasks
```

Switching models mid-session keeps the conversation context. You can start with Sonnet for exploration, then switch to Opus for implementation.

### /init — Project Setup

```
/init

# Creates a CLAUDE.md with:
# - Project description
# - Build/test commands
# - Code style preferences
# - Key architecture notes
```

Run this once per project. The generated CLAUDE.md is a starting point — edit it to add your specific rules and preferences. See `entities/claude-md.md` for best practices.

### /doctor — Diagnostics

```
/doctor

# Checks:
# - Authentication status
# - Model availability
# - Tool permissions
# - MCP server connections
# - Common configuration issues
```

Use this when Claude is not behaving as expected. It catches configuration problems that are hard to spot manually.

## Custom Slash Commands (Skills)

Beyond built-in commands, you can create custom slash commands using the skills system. Skills are project-specific or user-specific commands that map to predefined prompts or workflows.

### How Skills Work

Skills are defined as markdown files in your project's `.claude/commands/` directory (project-level) or `~/.claude/commands/` (user-level).

### Creating a Custom Command

Create a file at `.claude/commands/test-coverage.md`:

```markdown
Run the test suite and report coverage for the changed files.
Focus on:
1. Which functions lack test coverage
2. Edge cases that should be tested
3. Suggest specific test cases to add

Use `go test -cover ./...` or the project's test command.
```

Now you can use it:

```
/test-coverage
```

### Example Custom Commands

| Command file | Purpose |
|-------------|---------|
| `deploy-check.md` | Pre-deployment checklist |
| `security-scan.md` | Review for common vulnerabilities |
| `api-docs.md` | Generate/update API documentation |
| `migration-check.md` | Verify database migrations |
| `perf-review.md` | Analyze for performance issues |

### Parameterized Commands

Custom commands can accept arguments using `$ARGUMENTS`:

`.claude/commands/review-file.md`:
```markdown
Review the file at $ARGUMENTS for:
1. Bugs and logic errors
2. Security vulnerabilities
3. Performance issues
4. Code style consistency
```

Usage:
```
/review-file src/auth/middleware.go
```

## Tips

- **Use `/compact` proactively,** not just when you hit limits. Clean context produces better results.
- **Use `/cost` to build intuition.** After a few sessions, you will know which tasks are expensive and which are cheap.
- **Create custom commands for repeated workflows.** If you do the same review pattern every PR, make it a command.
- **`/review` before every commit.** It takes seconds and catches real bugs.
- **`/doctor` is your first debugging step.** When something feels off, run it before investigating manually.
