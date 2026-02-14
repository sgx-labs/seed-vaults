---
title: "First Project Setup — Step by Step"
tags: [getting-started, setup, first-project, tutorial, beginner]
content_type: research
domain: engineering
---

# First Project Setup — Step by Step

## Before You Start

You need:
- An Anthropic API key (or a Claude Max subscription)
- Node.js 18+ installed
- A project you want to work on (existing codebase or new project)

## Step 1: Install Claude Code

```bash
npm install -g @anthropic-ai/claude-code
```

Verify the installation:

```bash
claude --version
```

## Step 2: Open Your Project

Navigate to your project directory in the terminal:

```bash
cd /path/to/your/project
```

Claude Code works from the terminal, not from a browser. It reads your filesystem, runs commands, and edits files directly.

## Step 3: Start Claude Code

```bash
claude
```

On first run, Claude will:
1. Ask you to authenticate (API key or OAuth)
2. Read any existing CLAUDE.md in the directory
3. Present you with a prompt

You are now in an interactive session. Type naturally.

## Step 4: Create Your CLAUDE.md

This is the most important setup step. Ask Claude to help:

```
You: "Look at this project and create a CLAUDE.md file with build commands,
      architecture overview, and key conventions."
```

Claude will explore your codebase and draft a CLAUDE.md. Review it carefully and ask for edits:

```
You: "Add a rule that we never push directly to main. Also add that tests
      must pass before committing."
```

Or write it yourself. A minimal CLAUDE.md:

```markdown
# My Project

## Build & Test
- `npm install` — install dependencies
- `npm run dev` — start dev server
- `npm test` — run tests
- `npm run build` — production build

## Architecture
- src/ — application source code
- tests/ — test files
- public/ — static assets

## Rules
- Always run tests before committing
- Never push directly to main
```

Save the file and commit it to git. Claude reads it automatically at the start of every session.

## Step 5: Set Up Permissions

Create `.claude/settings.json` to pre-approve your common commands:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test *)",
      "Bash(npm run *)",
      "Bash(git status *)",
      "Bash(git diff *)",
      "Bash(git log *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)"
    ]
  }
}
```

Without this, Claude asks for approval on every command. Pre-approving your build and test commands makes the workflow much faster.

Commit `.claude/settings.json` to git (it is safe to share).

## Step 6: Install Hooks (Optional)

Hooks run automatically at specific points in Claude's lifecycle. The most common setup uses SAME for session memory:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "same hook session-start"
      }
    ]
  }
}
```

Add hooks to `.claude/settings.json` alongside your permissions.

You can also write custom hook scripts. Any executable that reads from stdin and writes to stdout works.

## Step 7: Configure MCP Servers (Optional)

MCP servers extend Claude's capabilities. Add them to `.claude/settings.json`:

```json
{
  "mcpServers": {
    "same": {
      "type": "stdio",
      "command": "same",
      "args": ["mcp"],
      "env": {}
    }
  }
}
```

Popular MCP servers to consider:
- **SAME** -- knowledge base and session memory
- **GitHub** -- issue and PR management from within Claude
- **Filesystem** -- extended file operations
- **Puppeteer** -- browser automation and testing

Install MCP servers separately (each has its own install process), then reference them in settings.

## Step 8: Try Your First Tasks

Start with low-risk tasks to build confidence:

### Explore the Codebase

```
You: "Explain the architecture of this project. What are the main
      components and how do they interact?"
```

### Run Tests

```
You: "Run the test suite and summarize the results."
```

### Fix a Simple Bug

```
You: "There's a bug where the login form doesn't show an error message
      when the password is wrong. The handler is in src/auth/login.ts.
      Fix it and add a test."
```

### Review Code

```
You: "Review the last 3 commits for potential issues."
```

### Write a New Feature (Plan First)

```
You: "I need to add rate limiting to the API endpoints. Use plan mode
      to design the approach before implementing."
```

## Tips for Your First Day

### Start Small

Do not begin with "rewrite the entire authentication system." Start with a bug fix, a test, or a code review. Build trust in the tool incrementally.

### Read the Diffs

Claude edits files directly. Always review what it changed before committing. Use:

```
You: "Show me the diff of everything you changed."
```

### Use Plan Mode for Big Tasks

For anything touching more than two files, ask Claude to plan first:

```
You: "Plan how you would implement feature X. Don't write code yet."
```

Review the plan. Adjust. Then let Claude implement.

### Let Claude Run Tests

After any change, ask Claude to verify:

```
You: "Run the tests to make sure nothing broke."
```

### Use /compact When Switching Tasks

If you are switching from one task to another in the same session:

```
/compact
You: "Now let's work on the API rate limiting."
```

This summarizes the prior conversation and frees up context window space.

### Check Token Usage

```
/cost
```

This shows how many tokens you have used. It builds intuition about what costs more (large file reads, many tool calls) and what costs less (targeted prompts, focused tasks).

## What Your Project Should Look Like After Setup

```
your-project/
  CLAUDE.md                      # Project instructions (committed)
  .claude/
    settings.json                # Permissions and hooks (committed)
    settings.local.json          # Personal overrides (gitignored)
  .gitignore                     # Includes .claude/settings.local.json
  src/
  tests/
  ...
```

## Next Steps

- Read `research/setup/claude-md-guide.md` for advanced CLAUDE.md patterns
- Read `research/setup/permissions-settings.md` for the full permission system
- Read `research/workflows/daily-workflow.md` for structuring your day
- Read `hub/prompt-patterns.md` for effective prompt techniques

## Quick Reference Card

| Task | Command / Prompt |
|------|-----------------|
| Start Claude | `claude` |
| Create CLAUDE.md | "Create a CLAUDE.md for this project" |
| Plan before coding | "Use plan mode to design X" |
| Run tests | "Run the test suite" |
| Review changes | "Show me the diff" |
| Switch tasks | `/compact` then new prompt |
| Check cost | `/cost` |
| Exit | `/exit` or Ctrl+C |
