---
title: "Using Claude Code for Code Review"
tags: [code-review, security, quality, workflow, review]
content_type: research
domain: engineering
---

# Using Claude Code for Code Review

## Why Claude Code for Reviews

Claude reads the full diff, understands the codebase context, and checks for patterns that are easy to miss during manual review: unchecked errors, SQL injection vectors, race conditions, off-by-one errors. It is not a replacement for human review, but it catches things faster and more consistently.

## Quick Start: The /review Command

The fastest way to review your changes:

```
/review
```

This reviews all uncommitted changes (staged and unstaged) and provides structured feedback. Use it before every commit.

## Review Patterns

### Review the Last Commit

```
You: "Review the last commit for bugs and code quality issues."
```

Claude runs `git diff HEAD~1..HEAD`, reads the changed files, and provides feedback.

### Review a Specific PR

```
You: "Review PR #42."
```

Claude uses `gh pr diff 42` to get the changes and analyzes them in context.

### Review Multiple Commits

```
You: "Review all commits on this branch since it diverged from main."
```

Claude runs `git diff main...HEAD` and reviews the cumulative changes.

### Review a Specific File

```
You: "Review server/handlers/payment.go for security issues."
```

This reads the entire file, not just recent changes. Useful for auditing existing code.

## Focused Reviews

### Security Review

```
You: "Review these changes with a security focus. Check for:
      - SQL injection
      - XSS vulnerabilities
      - Authentication/authorization gaps
      - Sensitive data exposure
      - Input validation issues"
```

Example output Claude might produce:

```
Security findings:

1. HIGH: SQL injection in search.go line 34
   `db.Query("SELECT * FROM users WHERE name = '" + name + "'")`
   Fix: Use parameterized query: `db.Query("SELECT * FROM users WHERE name = ?", name)`

2. MEDIUM: Missing authorization check in handlers/admin.go line 78
   The DeleteUser handler checks authentication but not admin role.
   Fix: Add `requireRole(r, "admin")` before processing.

3. LOW: Error message in auth.go line 92 leaks internal path
   `fmt.Errorf("failed to read /etc/app/secrets.json: %w", err)`
   Fix: Use a generic message for client-facing errors.
```

### Performance Review

```
You: "Review the database queries in this PR for performance issues.
      Check for N+1 queries, missing indexes, and unbounded result sets."
```

### Error Handling Review

```
You: "Review these changes for error handling. Are all errors checked?
      Are error messages descriptive? Do we log enough context?"
```

### Test Coverage Review

```
You: "Review this PR. Are there adequate tests? What edge cases
      are missing?"
```

### API Contract Review

```
You: "Review the API changes in this PR. Are they backward compatible?
      Do they follow our existing patterns?"
```

## Prompt Patterns for Better Reviews

### Be Specific About What to Look For

```
# Too vague:
You: "Review this code."

# Better:
You: "Review the error handling in the new payment processing flow.
      Check that all Stripe API errors are handled and that we never
      expose raw Stripe error messages to the client."
```

### Provide Context About What Changed and Why

```
You: "Review this refactoring. We moved from sync to async processing
      for order notifications. Focus on whether the error handling
      still works correctly in the async context."
```

### Ask for Severity Ratings

```
You: "Review these changes and rate each finding as HIGH, MEDIUM, or LOW
      severity. Only flag HIGH issues as blockers."
```

### Ask for Specific Fixes

```
You: "Review this code. For each issue you find, show the fix inline —
      don't just describe the problem."
```

## Review Checklists

### Pre-Commit Checklist

Run before every commit:

```
You: "Before I commit, check:
      1. Are there any debug/print statements left in?
      2. Are there any TODO comments that should be addressed?
      3. Are all new functions tested?
      4. Are error messages user-friendly?
      5. Is there any commented-out code that should be removed?"
```

### Pre-Merge Checklist

Run before merging a PR:

```
You: "Pre-merge review:
      1. Does the PR description match what the code does?
      2. Are there breaking changes that need documentation?
      3. Are database migrations reversible?
      4. Are new environment variables documented?
      5. Do all tests pass?"
```

## Reviewing Others' Code

Claude can review code you did not write, which is useful for team PR reviews:

```
You: "Review PR #42 from the team. I need to approve or request
      changes. Focus on:
      - Does the implementation match the ticket requirements?
      - Are there architectural concerns?
      - Code quality and test coverage"
```

For leaving review comments directly:

```
You: "Based on your review of PR #42, draft review comments I can
      post on GitHub. Format each as the file, line number, and
      comment text."
```

## Tips

1. **Run `/review` before every commit.** Make it a habit. It takes seconds and catches real bugs.

2. **Layer your reviews.** First pass: correctness. Second pass: security. Third pass: performance. Trying to catch everything in one prompt produces shallow results.

3. **Review your own code through Claude's eyes.** Ask Claude to explain what a function does. If its explanation does not match your intent, the code is unclear.

4. **Use reviews to learn.** When Claude flags something, ask "why is this a problem?" to understand the underlying principle.

5. **Do not blindly accept review suggestions.** Claude sometimes flags things that are intentional design choices. You know your codebase's constraints better than Claude does.

6. **Review tests too.** Tests can have bugs. Ask Claude to review test logic, not just production code.
