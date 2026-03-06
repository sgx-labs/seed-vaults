---
title: "Onboarding Documentation"
tags: [onboarding, new-hire, getting-started, developer-experience, documentation]
content_type: hub
domain: technical-writing
---

# Onboarding Documentation

## The Test

A good onboarding doc passes the "day one test": a new team member can go from zero to a working development environment, run tests, and make a small change -- all on their first day, without asking anyone for help.

If they have to Slack someone to ask "how do I run this?", the onboarding doc failed.

## What to Include

### Environment Setup (30 minutes or less)

```markdown
## Setup

### Prerequisites
- macOS 13+ or Ubuntu 22.04+
- Go 1.22+ (`brew install go` or see https://go.dev/dl)
- Docker Desktop (`brew install --cask docker`)
- PostgreSQL 15+ (`brew install postgresql@15`)

### Clone and Run

git clone git@github.com:your-org/your-project.git
cd your-project
make setup          # installs deps, creates .env, seeds database
make test           # runs the test suite (should see all green)
make dev            # starts the dev server at http://localhost:8080

### Verify It Works
Open http://localhost:8080/health — you should see {"status": "ok"}.
```

### Architecture Overview (one page)

Don't write a 50-page system design doc. Write one page with:
- What the system does (3 sentences)
- A simple diagram showing the main components
- Where the code for each component lives
- How data flows through the system

### Key Workflows

Document the 3-5 things a new developer does most often:

1. How to add a new API endpoint
2. How to write and run tests
3. How to create a database migration
4. How to deploy to staging
5. How to debug a failing CI build

### Where to Find Things

```markdown
## Project Map

src/
  api/          # HTTP handlers and middleware
  service/      # Business logic (no HTTP concerns)
  store/        # Database queries and models
  config/       # Configuration loading
docs/
  decisions/    # Architecture Decision Records
  runbooks/     # Operational procedures
scripts/        # Build and deployment scripts
```

### Common Gotchas

Every project has them. Document the ones that trip up every new developer:

```markdown
## Gotchas

- The test database is separate from dev. Run `make test-db-reset`
  if tests fail with "relation does not exist."
- Environment variables are loaded from `.env` in dev but from
  the secret manager in production. Never hardcode config values.
- The linter is strict about unused imports. Run `make lint` before
  pushing to avoid CI failures.
```

## What to Skip

- **Company history.** Nobody reads it on day one. Put it in the wiki.
- **Exhaustive architecture docs.** They'll explore the codebase naturally. Give them the map, not the territory.
- **Process documents.** Link to them, don't inline them. "See CONTRIBUTING.md for PR guidelines."
- **Everything at once.** Week 1 needs are different from month 1 needs. Layer the information.

## Keeping It Current

Onboarding docs rot faster than any other documentation. Prevention:

- **Every new hire updates the doc.** Make it their first PR. They're the best judge of what's missing.
- **Automate what you can.** `make setup` is more reliable than 15 manual steps.
- **Date it.** "Last verified: 2025-03-01" lets people know if it might be stale.
- **Test it quarterly.** Spin up a clean environment and follow the doc.

## See Also

- `hub/writing-great-readmes.md` -- The README as the first onboarding touchpoint
- `hub/internal-wiki-best-practices.md` -- Where to put the stuff that doesn't fit in the repo
- `hub/runbooks-and-playbooks.md` -- Operational onboarding for on-call engineers
- `guides/starting-documentation-from-zero.md` -- Building onboarding docs for an undocumented project
