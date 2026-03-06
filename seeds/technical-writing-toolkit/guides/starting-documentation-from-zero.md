---
title: "Starting Documentation from Zero"
tags: [guide, getting-started, zero-to-docs, beginner, documentation]
content_type: guide
domain: technical-writing
---

# Starting Documentation from Zero

## The Situation

Your project has no documentation. Maybe a placeholder README. Where do you start?

The answer: don't try to document everything. Document the one thing people need most, then expand.

## Step 1: Write the README (30 minutes)

Open your editor and answer three questions:

1. **What is this?** One sentence.
2. **How do I install it?** The exact commands.
3. **How do I use it?** One example of the most common use case.

```markdown
# myproject

CLI tool for converting log files to structured JSON.

## Install

go install github.com/yourorg/myproject@latest

## Usage

myproject parse access.log
# outputs structured JSON to stdout

myproject parse access.log --output results.json
# writes to file
```

That's your README. Commit it. You now have documentation.

## Step 2: Make Setup Reproducible (1 hour)

Can someone clone your repo and run the project? If the setup requires more than 3 commands, write a setup script or Makefile:

```makefile
.PHONY: setup
setup:
	cp .env.example .env
	go mod download
	docker compose up -d
	go run ./cmd/migrate up
	@echo "Ready. Run 'make dev' to start."
```

Then document it:

```markdown
## Development Setup

git clone git@github.com:yourorg/myproject.git
cd myproject
make setup
make dev
```

## Step 3: Document the Top 3 Questions (1 hour)

Ask your team: "What do people ask you most about this project?" Document those three things. Common candidates:

- How to add a new feature (endpoint, command, handler)
- How to run tests
- How to deploy

Add these to a `docs/` directory or expand your README.

## Step 4: Add a CONTRIBUTING Guide (30 minutes)

If anyone besides you will contribute:

```markdown
# Contributing

1. Fork and clone
2. Create a branch: git checkout -b my-feature
3. Make changes and add tests
4. Run: make test && make lint
5. Submit a pull request
```

## Step 5: Start Recording Decisions (Ongoing)

The next time your team makes a technical decision, write an ADR. See `guides/writing-your-first-adr.md`. Even one ADR is better than zero.

## What NOT to Do

- **Don't write a 50-page architecture doc.** You'll never finish it. Start small.
- **Don't aim for perfection.** A rough README that exists is infinitely better than a polished doc you'll write "someday."
- **Don't document internal implementation details first.** Start with what users (including your future self) need to know.
- **Don't write documentation nobody asked for.** Start with the questions people actually have.

## The First Week

| Day | Task | Time |
|-----|------|------|
| 1 | Write README (what, install, example) | 30 min |
| 2 | Make setup reproducible | 1 hour |
| 3 | Document top 3 questions | 1 hour |
| 4 | Add CONTRIBUTING.md | 30 min |
| 5 | Write first ADR (retroactive) | 30 min |

Five days, 3.5 hours total. You now have functional documentation.

## See Also

- `hub/writing-great-readmes.md` -- Expanding your README
- `hub/architecture-decision-records.md` -- Starting ADRs
- `entities/documentation-maturity-model.md` -- Where you are and where to go
- `research/documentation-debt.md` -- You're paying down debt already
- `hub/onboarding-documentation.md` -- Next level after the basics
