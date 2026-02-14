---
title: "How Do I Set Up GitHub Issue Templates?"
tags: [github, issues, templates, contributor-experience, triage]
content_type: research
domain: engineering
---

# How Do I Set Up GitHub Issue Templates?

## The Question

Half your issues are missing reproduction steps. A quarter are feature requests filed as bugs. How do you get structured, useful issue reports from users?

## YAML-Based Issue Forms (Recommended)

GitHub supports structured issue forms with dropdowns, checkboxes, and required fields. Create these in `.github/ISSUE_TEMPLATE/`.

### Bug Report Form

Create `.github/ISSUE_TEMPLATE/bug_report.yml`:

```yaml
name: Bug Report
description: Report a bug or unexpected behavior
title: "[Bug]: "
labels: ["bug", "needs-triage"]
body:
  - type: markdown
    attributes:
      value: |
        Thanks for reporting! Please fill out the details below.

  - type: textarea
    id: what-happened
    attributes:
      label: What happened?
      description: Describe the bug clearly.
      placeholder: "When I run `your-tool search`, it crashes with..."
    validations:
      required: true

  - type: textarea
    id: expected
    attributes:
      label: What did you expect to happen?
    validations:
      required: true

  - type: textarea
    id: reproduce
    attributes:
      label: Steps to reproduce
      description: Minimal steps to reproduce the issue.
      placeholder: |
        1. Install v1.2.3
        2. Run `your-tool init`
        3. Run `your-tool search "test"`
        4. See error
    validations:
      required: true

  - type: input
    id: version
    attributes:
      label: Version
      description: "Run `your-tool --version`"
      placeholder: "v1.2.3"
    validations:
      required: true

  - type: dropdown
    id: os
    attributes:
      label: Operating System
      options:
        - macOS (Apple Silicon)
        - macOS (Intel)
        - Linux
        - Windows
        - Other
    validations:
      required: true

  - type: textarea
    id: logs
    attributes:
      label: Relevant log output
      description: Paste any error messages or logs.
      render: shell
```

### Feature Request Form

Create `.github/ISSUE_TEMPLATE/feature_request.yml`:

```yaml
name: Feature Request
description: Suggest an idea or improvement
title: "[Feature]: "
labels: ["enhancement"]
body:
  - type: textarea
    id: problem
    attributes:
      label: What problem does this solve?
      description: Describe the problem or need.
    validations:
      required: true

  - type: textarea
    id: solution
    attributes:
      label: Proposed solution
      description: How would you like it to work?
    validations:
      required: true

  - type: textarea
    id: alternatives
    attributes:
      label: Alternatives considered
      description: What else have you tried or considered?
```

### Config File (Optional)

Create `.github/ISSUE_TEMPLATE/config.yml` to add external links:

```yaml
blank_issues_enabled: false
contact_links:
  - name: Questions and Support
    url: https://github.com/yourorg/your-project/discussions
    about: Please ask questions in Discussions, not Issues.
```

Setting `blank_issues_enabled: false` forces users to use your templates.

## PR Template

Create `.github/PULL_REQUEST_TEMPLATE.md`:

```markdown
## What does this PR do?

<!-- Brief description of the changes -->

## Related issue

<!-- Link to the issue this addresses: Fixes #123 -->

## Checklist

- [ ] Tests added/updated
- [ ] Documentation updated (if applicable)
- [ ] Changelog entry added (if user-facing change)
```

## Related

- `research/community/managing-issues-prs.md` — Issue management at scale
- `research/community/first-contributor-pr.md` — Handling contributor PRs
- `entities/github.md` — GitHub features overview
