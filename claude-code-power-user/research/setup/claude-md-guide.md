---
title: "Writing Effective CLAUDE.md Files"
tags: [claude-md, project-setup, configuration, governance, best-practices, nested, architecture]
content_type: research
domain: engineering
---

# Writing Effective CLAUDE.md Files

## What CLAUDE.md Does

CLAUDE.md is the single most impactful file in your project for Claude Code. Claude reads it at the start of every session and treats its contents as instructions. A well-written CLAUDE.md turns Claude from a generic assistant into a project-aware collaborator that knows your build system, conventions, and rules.

## How Claude Processes It

1. When you start a session, Claude reads `CLAUDE.md` from the working directory
2. It also reads any `CLAUDE.md` files in parent directories up to the repo root
3. Instructions are treated as persistent rules for the entire session
4. If you edit CLAUDE.md mid-session, Claude picks up the changes on the next prompt

## What to Include

### Build, Test, and Lint Commands

This is the highest-value section. Claude cannot guess your commands.

```markdown
## Build & Test
- `make build` — compile the binary (requires CGO_ENABLED=1)
- `make test` — run all tests
- `npm run lint` — check for style issues
- `go vet ./...` — static analysis
```

### Architecture Overview

Give Claude a map of the codebase so it knows where to look.

```markdown
## Architecture
- `cmd/` — CLI entry points (one file per command)
- `internal/store/` — Database layer (SQLite)
- `internal/api/` — HTTP handlers
- `internal/config/` — Configuration loading
- `web/` — React frontend (Vite + TypeScript)
```

### Conventions and Patterns

Tell Claude how your team writes code.

```markdown
## Conventions
- Error handling: Always wrap errors with `fmt.Errorf("context: %w", err)`
- Logging: Use `slog` structured logging, never `fmt.Println`
- Tests: Table-driven tests with `t.Run()` subtests
- Naming: Handlers are `HandleVerb` (HandleCreate, HandleDelete)
```

### Forbidden Actions

Put "never do" rules near the top. Claude follows explicit prohibitions reliably.

```markdown
## Rules
- NEVER commit directly to main — always use feature branches
- NEVER run `rm -rf` on any directory
- NEVER auto-push — always ask before pushing
- Do NOT modify generated files in `gen/`
- Do NOT add dependencies without approval
```

### Git Workflow

```markdown
## Git
- Commit messages: conventional commits (feat:, fix:, docs:)
- Branch naming: feature/short-description or fix/short-description
- Always run tests before committing
- Squash merge to main
```

## Structure: What Makes a CLAUDE.md Scannable

Use headers, bullet points, and tables. Claude parses markdown structure well.

| Element | Use For |
|---------|---------|
| `##` Headers | Major sections (Build, Architecture, Rules) |
| Bullet points | Lists of commands, conventions, file paths |
| Code blocks | Exact commands, file examples |
| Tables | Quick reference (command cheat sheets) |
| Bold text | Emphasis on critical rules |

Keep the root CLAUDE.md under 200 lines. If it grows beyond that, move details into subdirectory CLAUDE.md files.

## Example: Complete CLAUDE.md

```markdown
# My Web App

## Build & Test
- `npm run dev` — start dev server (port 3000)
- `npm test` — run Jest tests
- `npm run build` — production build
- `npm run db:migrate` — run pending migrations

## Architecture
- src/routes/ — Express route handlers
- src/services/ — Business logic layer
- src/models/ — Sequelize models
- src/middleware/ — Auth, logging, rate limiting
- tests/ — Jest tests, mirrors src/ structure

## Conventions
- TypeScript strict mode everywhere
- Use Zod for request validation
- Services return `Result<T, Error>`, never throw
- All endpoints need integration tests

## Rules
- NEVER commit .env files
- NEVER use `any` type — use `unknown` and narrow
- Always run `npm test` before committing
- Ask before adding new npm dependencies

## Git
- Conventional commits (feat:, fix:, chore:)
- PR required for main, squash merge
- Keep PRs under 400 lines when possible
```

## Common Mistakes

### Too Long

A 500-line CLAUDE.md dilutes important instructions. Claude prioritizes what it reads, and burying critical rules in a wall of text means they get less weight. Keep it under 200 lines.

### Too Vague

```markdown
# Bad
- Follow best practices
- Write good code
- Be careful with the database

# Good
- Use parameterized queries for all SQL — never string concatenation
- All database operations go through the Repository pattern in src/repos/
- Run `npm run db:check` after any migration changes
```

### Including Secrets

CLAUDE.md is checked into git. Never put API keys, passwords, database URLs, or tokens in it. If Claude needs to know about environment variables, reference the variable name, not the value.

```markdown
# Bad
DATABASE_URL=postgres://admin:secretpass@prod-db:5432/myapp

# Good
- Database: Set DATABASE_URL in .env (see .env.example)
```

### Duplicating What Code Shows

Do not paste entire files into CLAUDE.md. Claude can read your code. Focus on things that are NOT obvious from the code: conventions, why decisions were made, what tools to run.

## Advanced: Nested CLAUDE.md Files

You can place CLAUDE.md files in subdirectories. They add context when Claude is working in that subtree.

```
project/
  CLAUDE.md              # Project-wide rules
  frontend/
    CLAUDE.md            # React-specific conventions
  backend/
    CLAUDE.md            # Go-specific patterns
  infrastructure/
    CLAUDE.md            # Terraform rules, never auto-apply
```

Example subdirectory CLAUDE.md:

```markdown
# Frontend (React)

## Commands (run from project root)
- `npm run dev` — start Vite dev server
- `npm run test:frontend` — run Vitest

## Conventions
- Functional components only, no class components
- Use React Query for server state, Zustand for client state
- CSS Modules for styling (component.module.css)
- Lazy-load routes with React.lazy()

## File Pattern
- components/FeatureName/FeatureName.tsx — component
- components/FeatureName/FeatureName.test.tsx — tests
- components/FeatureName/FeatureName.module.css — styles
```

Subdirectory CLAUDE.md files are additive. They do not replace the root file. Claude merges them together.

## Global CLAUDE.md

Place a CLAUDE.md at `~/.claude/CLAUDE.md` for instructions that apply to every project on your machine. Good for personal preferences:

```markdown
# Global Preferences
- Use vim keybindings references when explaining editor shortcuts
- Prefer concise explanations over verbose ones
- When committing, always show the diff first
- Default to TypeScript for new JavaScript files
```

## When to Update CLAUDE.md

- After adding a new build command or script
- When the team adopts a new convention
- After debugging an issue caused by Claude not knowing a rule
- When onboarding a new team member (if they need it, Claude does too)

## Quick Checklist

- [ ] Build, test, and lint commands documented
- [ ] Architecture overview with directory purposes
- [ ] Key conventions listed (naming, error handling, patterns)
- [ ] "Never do" rules near the top
- [ ] Git workflow described
- [ ] No secrets or PII
- [ ] Under 200 lines
- [ ] Checked into git
