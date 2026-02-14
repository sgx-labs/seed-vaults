---
title: "Automating Repetitive Tasks with Claude Code"
tags: [automation, hooks, mcp, slash-commands, workflow, advanced, permissions, lifecycle]
content_type: research
domain: engineering
---

# Automating Repetitive Tasks with Claude Code

## The Question

How do you build repeatable automation around Claude Code so that common tasks happen automatically or with a single command?

## The Automation Stack

Claude Code has four automation surfaces. Each handles a different layer:

| Layer | What It Automates |
|-------|------------------|
| **Hooks** | Session lifecycle -- context loading, handoffs, guardrails |
| **CLAUDE.md** | Agent behavior -- coding conventions, project rules, constraints |
| **MCP tools** | External capabilities -- knowledge bases, APIs, databases |
| **Slash commands** | User shortcuts -- common prompts, recurring workflows |

The most powerful automations combine multiple layers.

## Hook-Based Automation

Hooks run at specific points in the session lifecycle. They are the backbone of automation in Claude Code.

### SessionStart: Automatic Context Loading

Load project context every time a session begins, with no manual prompting:

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

What this does:
- Loads recent decisions and handoff notes
- Surfaces pinned notes and session context
- Provides orientation so the agent starts informed

Without this hook, you would need to manually say "load my project context" at the start of every session.

### Stop: Automatic Handoff

Save session state when the conversation ends:

```json
{
  "hooks": {
    "Stop": [
      {
        "type": "command",
        "command": "same hook stop"
      }
    ]
  }
}
```

What this does:
- Captures what was accomplished
- Notes pending work and blockers
- Writes a handoff note the next session can read

This creates continuity across sessions without you ever typing "save a handoff note."

### PreToolUse: Guardrails

Block dangerous operations automatically:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "same hook pre-tool-use"
      }
    ]
  }
}
```

Example guardrails you can implement:
- Block `git push --force` to main
- Prevent deletion of migration files
- Warn before editing generated code
- Enforce that tests run before commits

### Combining Hooks: Full Lifecycle

```json
{
  "hooks": {
    "SessionStart": [
      { "type": "command", "command": "same hook session-start" }
    ],
    "UserPromptSubmit": [
      { "type": "command", "command": "same hook prompt-submit" }
    ],
    "PreToolUse": [
      { "type": "command", "command": "same hook pre-tool-use" }
    ],
    "Stop": [
      { "type": "command", "command": "same hook stop" }
    ]
  }
}
```

This is a complete lifecycle automation. Context loads at start, relevant notes surface with each prompt, dangerous operations are blocked, and state is saved at end. All of it happens without manual intervention.

## CLAUDE.md Rules for Consistency

`CLAUDE.md` is not just documentation -- it is an instruction set that shapes agent behavior every session.

### Enforce Coding Standards

```markdown
# CLAUDE.md

## Rules
- All new functions must have JSDoc comments.
- Use named exports, never default exports.
- Error messages must include the function name: `functionName: description`.
- Test files must be named *.test.ts, not *.spec.ts.
```

Claude follows these rules automatically. No need to remind it each session.

### Enforce Process

```markdown
## Process
- Always run `npm test` after modifying any file in src/.
- After creating a new API endpoint, update the OpenAPI spec in docs/api.yaml.
- Never modify files in the generated/ directory.
```

### Environment-Specific Rules

```markdown
## Environment
- Database migrations: `npx prisma migrate dev`
- Seed data: `npx prisma db seed`
- Local server: `npm run dev` (port 3000)
- Tests require REDIS_URL=redis://localhost:6379
```

## Permission Pre-Approval

For commands Claude runs frequently, pre-approve them to avoid repeated permission prompts:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm test)",
      "Bash(npm run lint)",
      "Bash(go test ./...)",
      "Bash(make build)",
      "Bash(git status)",
      "Bash(git diff)"
    ]
  }
}
```

This eliminates the "allow this command?" prompt for safe, frequently used commands. Claude can run tests, lint, and build without interrupting you.

### What to Pre-Approve

- Test runners
- Linters and formatters
- Build commands
- Read-only git commands (status, diff, log)
- Project-specific safe scripts

### What NOT to Pre-Approve

- `git push` (you want to review before pushing)
- `rm` commands (destructive)
- Database migrations (need review)
- Anything that calls external APIs with side effects

## Custom Slash Commands

Create shortcuts for prompts you use repeatedly. Define them in your project or user settings:

```json
{
  "commands": {
    "/test": "Run the full test suite and report any failures.",
    "/lint-fix": "Run the linter and fix all auto-fixable issues.",
    "/review": "Review the staged git changes for bugs, security issues, and style problems.",
    "/pr": "Create a pull request with a summary of all changes on this branch."
  }
}
```

### Usage

```
You: /test
→ Claude runs the test suite and reports results

You: /review
→ Claude reviews staged changes and gives feedback

You: /pr
→ Claude creates a PR with auto-generated description
```

## Practical Automation Recipes

### Recipe 1: Automated Commit Messages

```
You: "Stage the changes and generate a commit message."
```

With CLAUDE.md rules:

```markdown
## Commit Messages
- Use conventional commits: feat:, fix:, refactor:, test:, docs:
- First line under 72 characters
- Include the "why" not just the "what"
```

Claude reads the diff, understands the change, and writes a message following your conventions.

### Recipe 2: Automated PR Descriptions

```
You: /pr
```

Claude:
1. Runs `git log main..HEAD` to see all commits
2. Runs `git diff main...HEAD` to see all changes
3. Writes a structured PR description with summary, changes, and test plan
4. Creates the PR via `gh pr create`

### Recipe 3: Test-Driven Feedback Loop

```markdown
# CLAUDE.md
## Rules
- After every code change, run the relevant test file.
- If a test fails, fix the code before moving on.
- Never leave a session with failing tests.
```

This turns Claude into a TDD partner. Every edit triggers a test run. Failures get fixed immediately.

### Recipe 4: Context-Aware Development

Combine SAME hooks with MCP tools:

```
SessionStart hook:
  → SAME loads last session's handoff note
  → Agent sees: "Last session: implemented user auth. Next: add rate limiting."

UserPromptSubmit hook:
  → SAME searches notes for relevant context
  → Agent sees: related decisions and architecture notes

You: "Add rate limiting to the API endpoints."
  → Agent already knows the auth middleware structure from the handoff
  → Agent has the architecture context from SAME's search
  → Implementation is immediately on target
```

### Recipe 5: Automated Documentation Updates

```markdown
# CLAUDE.md
## Rules
- When adding a new API endpoint, update docs/api.yaml with the endpoint spec.
- When adding a new CLI command, update the help text and the man page.
- When changing environment variables, update .env.example.
```

Documentation stays in sync because Claude updates it as part of every change.

## Building Your Personal Automation Stack

Start with these three layers and expand:

### Layer 1: Hooks (Session Lifecycle)

```
same init
```

This sets up SessionStart and Stop hooks. You get automatic context loading and handoff saving from day one.

### Layer 2: CLAUDE.md (Project Rules)

Write your project's architecture overview, coding conventions, and process rules. Claude follows them every session.

### Layer 3: MCP Tools (External Context)

```bash
same init --mcp-only   # if using an IDE
same init              # if using CLI
```

SAME provides search, decision logging, and session context as MCP tools. Your agent can query the knowledge base mid-conversation.

### Layer 4: Permission Pre-Approval + Slash Commands

Once you know which commands Claude runs frequently, pre-approve them. Once you know which prompts you type frequently, make them slash commands.

## The Payoff

A fully automated Claude Code setup means:

- Sessions start with full context (hooks)
- The agent follows your conventions (CLAUDE.md)
- Relevant knowledge surfaces automatically (MCP)
- Common tasks are one command (slash commands)
- Safe operations run without prompts (permissions)
- Session state persists across conversations (handoffs)

You spend less time managing the agent and more time on the actual work.
