---
title: "Using Claude Code for Git Operations"
tags: [git, commits, pull-requests, branches, workflow, gh-cli, conventional-commits]
content_type: research
domain: engineering
---

# Using Claude Code for Git Operations

## Why Use Claude Code for Git

Claude Code has built-in tools for git operations. It reads diffs, writes commit messages, creates PRs, and manages branches. The advantage over doing it manually: Claude reads the actual code changes and writes messages that reflect what the code does, not what you think it does.

## Commits

### Let Claude Write Commit Messages

Claude reads the full diff and writes a message that describes the changes accurately:

```
You: "Commit the current changes."

Claude: [runs git diff, analyzes changes]
Claude: "Here's what I see:
  - Modified auth middleware to check token expiry
  - Added new test for expired token handling
  - Updated error message in login handler

  Proposed commit message:
  'Fix token expiry check in auth middleware

   The middleware was not validating token expiration timestamps,
   allowing expired tokens to pass authentication. Added expiry
   check and corresponding test coverage.'

  Shall I commit?"
```

### Stage Specific Files

Claude stages specific files rather than using `git add -A`, which prevents accidentally committing sensitive files:

```
You: "Commit only the changes in the auth package."

Claude: [stages auth/*.go files, excludes other changes]
```

You can also be explicit:

```
You: "Stage server/auth.go and server/auth_test.go, then commit."
```

### Review Before Committing

Always review the diff before approving a commit:

```
You: "Show me the full diff of what will be committed."

# After reviewing:
You: "Looks good, commit it."
```

## Pull Requests

### Create PRs with Summaries

Claude uses the `gh` CLI to create pull requests with structured descriptions:

```
You: "Create a PR for this branch."

Claude: [reads all commits on the branch, analyzes changes]
Claude: "Creating PR with:
  Title: Add rate limiting to API endpoints
  Body:
  ## Summary
  - Added rate limiting middleware using token bucket algorithm
  - Configured per-endpoint limits in config.yaml
  - Added rate limit headers to responses

  ## Test plan
  - [ ] Verify rate limit kicks in after threshold
  - [ ] Confirm rate limit headers are present
  - [ ] Test that admin endpoints have higher limits"
```

### Review Existing PRs

```
You: "Review PR #42 and summarize the changes."
You: "Check PR #42 for security issues."
```

Claude uses `gh pr view` and `gh pr diff` to read the PR content and provide feedback.

## Branch Management

### Create Feature Branches

```
You: "Create a branch for the rate-limiting feature."

Claude: git checkout -b feature/rate-limiting
```

### Switch and Compare Branches

```
You: "Show me what's different between this branch and main."

Claude: git diff main...HEAD
```

### Clean Up

```
You: "Which branches have been merged and can be deleted?"

Claude: [checks merged branches, lists them]
```

## Common Git Workflows

### Feature Branch Flow

```
You: "Create a branch for adding email notifications."
# ... do the work ...
You: "Commit everything we've done."
You: "Create a PR targeting main."
You: "Push it."
```

### Fix and Ship

```
You: "Fix the null pointer in order processing and commit it."
# Claude fixes, shows diff, commits with your approval
You: "Push to the current branch."
```

### Interactive History Review

```
You: "Show me the last 10 commits with their messages."
You: "What changed in commit abc1234?"
You: "Show the diff between yesterday's commit and now."
```

## Best Practices

### 1. Never Auto-Push

Always review before pushing. A commit is local and reversible. A push is shared and permanent (in practice).

```
# Good: separate steps with review
You: "Show the diff."
You: "Commit it."
You: "Push to origin."

# Bad: all at once with no review
You: "Commit and push everything."
```

### 2. Always Review the Diff

Even when Claude writes the commit message, read the diff. Claude might miss that you left a debug print statement or a TODO comment.

```
You: "Show me the diff before committing."
```

### 3. Stage Files Explicitly

Avoid `git add -A` in projects with sensitive files. Tell Claude which files or directories to include:

```
You: "Stage everything in src/ and tests/ but not config/."
```

### 4. Write Context in Commit Messages

Claude writes good technical messages by default, but you can guide the style:

```
You: "Commit this. The message should explain that we chose token
      bucket over sliding window because of memory constraints."
```

### 5. Use Conventional Commits If Your Project Uses Them

```
You: "Commit using conventional commit format."

Claude: "feat(auth): add token expiry validation

        Check token expiration timestamp in auth middleware.
        Previously expired tokens were accepted as valid."
```

### 6. Check Status Before Starting

```
You: "What's the current git status? Any uncommitted changes?"
```

This prevents starting new work on top of uncommitted changes from a previous session.

## Common Patterns

| Task | Prompt |
|------|--------|
| Commit staged changes | "Commit the staged changes" |
| Commit specific files | "Commit only the files in pkg/auth/" |
| Create PR | "Create a PR with a summary of all changes" |
| Review PR | "Review PR #15 for bugs and security issues" |
| Check what changed | "Show me what changed since the last commit" |
| Compare branches | "Diff this branch against main" |
| View recent history | "Show the last 5 commits with messages" |
| Create branch | "Create a branch called feature/notifications" |
| Cherry-pick | "Cherry-pick commit abc1234 onto this branch" |
| Revert | "Revert the last commit" |

## What Claude Cannot Do

- **Force push to protected branches** — Claude will warn you and refuse by default
- **Resolve complex merge conflicts** — Claude can help, but you should review the resolution
- **Push without permission** — Claude will always ask before pushing
- **Access private repos it's not authenticated for** — Use `/login` and `gh auth` first
