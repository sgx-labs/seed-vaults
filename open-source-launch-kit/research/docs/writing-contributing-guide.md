---
title: "How Do I Write a Good CONTRIBUTING.md?"
tags: [contributing, documentation, contributors, onboarding, dev-setup, good-first-issue]
content_type: research
domain: engineering
---

# How Do I Write a Good CONTRIBUTING.md?

## The Question

Your CONTRIBUTING.md is the first thing a potential contributor reads. How do you write one that turns "I might contribute" into "I just opened a PR"?

## The Goal

A contributor should be able to go from "I found this project" to "I have a working dev environment and know what to work on" in under 15 minutes.

## Essential Sections

### 1. Welcome Message (2-3 Sentences)

Start warm. People need to feel welcome before they read instructions.

```markdown
# Contributing to [Project Name]

Thank you for wanting to contribute! Every contribution matters —
bug reports, documentation fixes, and code changes all help make
this project better.
```

### 2. Quick Dev Setup (The Most Important Section)

List the exact commands to get a working development environment:

```markdown
## Development Setup

git clone https://github.com/yourorg/your-project
cd your-project
npm install
npm test  # Verify everything works
```

If setup requires more than 5 commands, simplify your setup process. Contributors bounce at complexity.

### 3. What to Work On

Point newcomers to the right issues:

```markdown
## Where to Start

- Look for issues labeled `good first issue` — these are specifically
  chosen for newcomers
- Issues labeled `help wanted` are ready for external contributors
- Not sure where to start? Open a discussion and ask!
```

### 4. How to Submit Changes

Step-by-step process:

```markdown
## Submitting Changes

1. Fork the repo and create a branch from `main`
2. Make your changes
3. Add tests for new functionality
4. Run the test suite: `npm test`
5. Push your branch and open a pull request
```

### 5. Code Conventions

Save contributors the guesswork:

```markdown
## Code Style

- We use [linter/formatter] — run `npm run lint` before committing
- Variable names: camelCase
- Functions: descriptive verb-noun (e.g., processEvent, validateInput)
- Tests: describe blocks with "should..." test names
```

### 6. What NOT to Do

Prevent wasted effort:

```markdown
## Before You Start

- For large changes, open an issue first to discuss the approach
- Do not change coding style across the project in a single PR
- Do not add dependencies without discussing in an issue first
```

## Advanced Sections (Add as Project Grows)

- **Architecture overview** — Where things live and why
- **Testing strategy** — How to write tests that match your patterns
- **Release process** — For maintainers, how releases work
- **Communication channels** — Where to ask questions

## Making It Discoverable

GitHub automatically surfaces CONTRIBUTING.md:
- When someone opens a new issue, GitHub links to it
- When someone opens a new PR, GitHub links to it
- It appears in the "Community" section of your repo

## Common Mistakes

1. **Too long** — More than 2 printed pages and nobody reads it
2. **No dev setup instructions** — The biggest friction point
3. **Outdated commands** — Test your contributing guide quarterly
4. **No "good first issue" labels** — Contributors have nothing to start with
5. **No code style guide** — Contributors guess wrong, you reject their PR, they leave

## Related

- `templates/contributing-template.md` — Fill-in template
- `research/community/first-contributor-pr.md` — Handling first PRs
- `hub/community.md` — Community building overview
