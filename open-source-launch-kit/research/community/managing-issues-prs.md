---
title: "How Do I Manage Issues and PRs From Contributors?"
tags: [issues, pull-requests, triage, management, contributors]
content_type: research
domain: engineering
---

# How Do I Manage Issues and PRs From Contributors?

## The Question

As your project grows, issues and PRs start piling up. How do you stay on top of them without it becoming a full-time job?

## Triage System

### Label Strategy

Create a clear labeling system from the start:

**Type labels:**
- `bug` — Something is broken
- `enhancement` — Feature request
- `documentation` — Docs need updating
- `question` — Support question (consider redirecting to Discussions)

**Priority labels:**
- `critical` — Broken for everyone, security issue
- `important` — Affects many users, should fix soon
- `nice-to-have` — Would improve things but not urgent

**Contributor labels:**
- `good first issue` — GitHub surfaces these for new contributors
- `help wanted` — Also surfaced by GitHub for contributor matching

**Status labels:**
- `needs-triage` — Not yet reviewed
- `needs-info` — Waiting for reporter to provide details
- `wontfix` — Intentionally not fixing (explain why)

### Triage Workflow

1. New issue arrives -> add `needs-triage` label
2. Read it within 24-48 hours
3. Ask clarifying questions if needed (`needs-info`)
4. Categorize: is this a bug, feature, question, or out of scope?
5. Add type and priority labels
6. Respond with initial assessment

## Issue Templates

Use GitHub issue templates to collect structured information:

**Bug Report Template:**
```markdown
### What happened?

### What did you expect?

### Steps to reproduce

### Environment
- OS:
- Version:
- Language/runtime version:
```

**Feature Request Template:**
```markdown
### Problem
What problem does this solve?

### Proposed Solution
What would you like to happen?

### Alternatives Considered
What else have you tried?
```

## PR Management

### Response Time
- Acknowledge within 24 hours (even if you cannot review yet)
- Review within 1 week for routine PRs
- Complex PRs: set expectations — "This will take me a few days to review properly."

### Stale PRs and Issues
- Enable GitHub's stale bot (or use actions/stale)
- Mark inactive issues as stale after 60 days
- Close after 90 days with a friendly message
- This is not rude — it is necessary for project health

### Template for Closing Stale Issues
```
This issue has been inactive for 90 days. Closing for now to keep the
issue tracker manageable. If this is still relevant, please reopen with
any new information. Thank you for reporting!
```

## Scaling Tips

- Use GitHub Projects to track work across issues and PRs
- Batch triage sessions weekly instead of checking constantly
- Delegate: promote active contributors to triage helpers
- Pin a "known issues" issue for common problems

## Related

- `research/community/first-contributor-pr.md` — Handling first-time contributors
- `hub/community.md` — Community building overview
- `entities/github.md` — GitHub features for community management
