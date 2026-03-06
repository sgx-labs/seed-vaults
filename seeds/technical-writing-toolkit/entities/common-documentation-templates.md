---
title: "Common Documentation Templates"
tags: [templates, readme, adr, rfc, runbook, changelog, reference, entity]
content_type: entity
pinned: true
domain: technical-writing
---

# Common Documentation Templates

## README Template

```markdown
# Project Name

One-line description of what this project does.

## Install

[Exact install command]

## Quick Start

[3-5 lines showing the most common use case]

## Usage

[2-3 more examples for common scenarios]

## Configuration

[Key config options, if any]

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

[License name and link]
```

## ADR Template

```markdown
# ADR-NNN: [Decision Title]

**Status:** Proposed | Accepted | Rejected | Superseded by ADR-NNN
**Date:** YYYY-MM-DD
**Deciders:** [Names or team]

## Context

[What is the situation that requires a decision?]

## Decision

[What was decided.]

## Alternatives Considered

**[Alternative 1]** — [Why it was rejected.]

**[Alternative 2]** — [Why it was rejected.]

## Consequences

[What are the positive and negative effects of this decision?]
```

## RFC Template

```markdown
# RFC: [Proposal Title]

**Author:** [Name]
**Date:** YYYY-MM-DD
**Status:** Draft | Under Review | Accepted | Rejected
**Reviewers:** [Names]

## Problem

[What problem are we solving? Why now?]

## Proposal

[What do you propose? High-level description.]

## Design

[Detailed design: API changes, data models, diagrams.]

## Alternatives Considered

[What else was evaluated and why it was rejected?]

## Risks and Mitigations

[What could go wrong? How will we handle it?]

## Rollout Plan

[How will this be deployed? What's the rollback plan?]

## Open Questions

[What hasn't been decided yet?]
```

## Runbook Template

```markdown
# [Task Name]

**When to use:** [Trigger condition]
**Who can run:** [Required access/role]
**Duration:** [Expected time]
**Risk:** [Low | Medium | High — and why]

## Prerequisites

- [Required access]
- [Required tools]

## Steps

1. [Exact command or action]
   Expected output: [what you should see]

2. [Next step]

## If It Doesn't Work

- [Symptom]: [What to do]
- [Symptom]: [What to do]
- Escalate to: [Team or person]

## Post-Action

- [Notification or logging steps]
```

## Changelog Template

```markdown
# Changelog

All notable changes documented here.
Format: [Keep a Changelog](https://keepachangelog.com).
Versioning: [Semantic Versioning](https://semver.org).

## [Unreleased]

## [X.Y.Z] - YYYY-MM-DD

### Added
- [New feature description]

### Changed
- [Behavior change description]

### Fixed
- [Bug fix description]

### Removed
- [Removed feature description]
```

## CONTRIBUTING Template

```markdown
# Contributing

## Quick Start

1. Fork the repo
2. Create a branch: `git checkout -b my-feature`
3. Make changes and add tests
4. Run `make test` and `make lint`
5. Submit a pull request

## Guidelines

- [Code style rules]
- [Test requirements]
- [Documentation requirements]

## Pull Request Process

- [How PRs are reviewed]
- [What approvals are needed]
```

## See Also

- `hub/writing-great-readmes.md` -- README guidance
- `hub/architecture-decision-records.md` -- ADR guidance
- `hub/rfcs-and-design-docs.md` -- RFC guidance
- `hub/runbooks-and-playbooks.md` -- Runbook guidance
- `hub/changelogs.md` -- Changelog guidance
