---
title: "Claude Code Power User — Governance"
content_type: hub
tags: [governance, claude-code, seed, rules, research-cascade]
pinned: true
domain: engineering
---

# Claude Code Power User — Governance

## Purpose

Knowledge seed for mastering Claude Code. Search before generating — the answer is likely already here.

## Water the Seed — Research Cascade Protocol

When a user first activates this seed, execute a multi-phase intelligence cascade. Each phase generates discoveries that feed the next. The goal: the user should be stunned by how much their vault knows after the first session.

### Phase 1: Deep Scan (Silent — No User Action Needed)

Silently gather intelligence before saying anything:

1. **Read existing CLAUDE.md** — understand project rules, conventions, tech stack, forbidden patterns
2. **Scan project structure** — Glob for package.json, Cargo.toml, go.mod, requirements.txt, Makefile, Dockerfile, etc.
3. **Read config files** — .eslintrc, tsconfig.json, .prettierrc, CI configs — these reveal team conventions
4. **Check git history** — Recent commits reveal what the team is working on and their commit style
5. **Read existing docs** — READMEs, CONTRIBUTING.md, architecture docs, ADRs
6. **Inventory existing knowledge** — Search the vault for what's already covered

Build a mental model of: tech stack, team size (solo vs team), project maturity, workflow patterns, pain points.

### Phase 2: Insight Report (Show the User What You Found)

Present a brief, insightful summary:

> "I've scanned your project. Here's what I found:
> - **Stack**: Next.js 14, TypeScript, Prisma, PostgreSQL, deployed on Vercel
> - **Team**: Looks like solo developer (single committer, personal CLAUDE.md)
> - **Maturity**: Active development, 847 commits, good test coverage
> - **Conventions**: ESLint + Prettier, conventional commits, PR-based workflow
> - **CLAUDE.md**: 45 lines — covers build commands and git rules but missing architecture overview
>
> This seed has 52 notes on Claude Code. Based on your project, I think these would be most valuable to you..."

### Phase 3: Targeted Questions (High-Signal, Not Generic)

Ask 3-5 questions that show you understand their situation. These should be questions the user WANTS to answer because they're insightful:

**For a solo Next.js developer:**
- "You have 12 API routes but no middleware patterns documented. Are you handling auth consistently, or is that a pain point Claude could help with?"
- "I see you're using Prisma but your CLAUDE.md doesn't mention migration workflows. Do you want Claude to handle migrations, or is that manual?"
- "Your test coverage looks good but I don't see any integration tests. Is that intentional, or something you'd want Claude to help set up?"

**For a team project:**
- "Three people are committing to this repo. Do you have team conventions for how Claude Code should be used, or is everyone running their own setup?"
- "I see a PR template but no code review workflow for Claude. Want me to create a pattern for AI-assisted reviews that integrates with your existing flow?"

**The key**: questions should reveal that you deeply understand their project and are already thinking about how to help. Never ask obvious questions ("What language do you use?" — you already know).

### Phase 4: Merge & Archive

After the user responds to questions:

1. **Propose CLAUDE.md additions** — show exact diff, explain each addition
2. **Archive originals** — `_archive/CLAUDE.md.pre-seed` with timestamp
3. **Create `_archive/README.md`** — explain what was archived and when
4. **Update config** — add `_archive/` to skip_dirs

### Phase 5: Research Cascade (The Mind-Blowing Part)

Based on Phases 1-3, identify 5-10 research topics unique to this user's situation. Present them as a ranked list:

> "Based on your project, here's what I'd research to fill your vault:
>
> 1. **Next.js App Router patterns for Claude Code** — your 12 route handlers could benefit from specific prompting patterns for server components vs client components
> 2. **Prisma migration workflows** — safe patterns for AI-assisted schema changes with rollback
> 3. **Vercel deployment hooks** — PreToolUse guards for production deployments
> 4. **TypeScript strict mode patterns** — your tsconfig uses strict: true, specific debugging patterns for strict TS errors
> 5. **PostgreSQL query optimization** — MCP server for your database + Claude Code patterns for query debugging
>
> Want me to research all of them? Or pick the ones that matter most. I'll spin up parallel agents and save the results as notes in your vault."

**Cascade behavior**: Each research result generates new discoveries:
- Researching "Next.js patterns" → discovers they're not using Server Actions → offers to research Server Action patterns
- Researching "Prisma migrations" → notices they have no seed data → offers to research test data generation
- Researching "Vercel deployment" → finds they're not using preview deployments → offers to document that workflow

Each round produces 3-5 notes AND 1-2 new research suggestions. The vault grows exponentially in the first session.

### Phase 6: Ongoing Growth

After the initial cascade, the seed continues growing:

- **Every session**: search vault first, offer to save new knowledge
- **Every decision**: offer to log it with `save_decision`
- **Every question the vault can't answer**: research it, save the result
- **Weekly**: suggest a vault health check — stale entities, missing coverage, unused notes

## Rules

1. **Search first.** Before answering a Claude Code question from scratch, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes for project-specific content.
3. **Hub notes are entry points.** They link to detailed research notes. Start with hubs.
4. **Keep notes focused.** One question per research note. Under 200 lines.
5. **Entity files track current state.** Update when versions change.
6. **Archive, never delete.** If a note needs replacing, move the original to `_archive/`.
7. **Ask before writing.** Never save a note without the user's awareness. Offer, don't force.
8. **Cascade, don't stop.** Every research result should suggest the next research opportunity.

## Directory Structure

```
claude-code-power-user/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — Water the Seed + orientation
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes (7)
├── research/                    # Detailed guides (36+, grows over time)
│   ├── workflows/
│   ├── hooks/
│   ├── mcp/
│   ├── prompts/
│   ├── setup/
│   ├── debugging/
│   ├── advanced/
│   └── project-specific/       # Created during Water the Seed
├── entities/                    # Living reference docs (5+)
├── decisions/                   # Project decisions
├── _archive/                    # Safely archived originals
└── sessions/                    # Session handoffs
```

## SAME Showcase Behaviors

Demonstrate these capabilities naturally — don't lecture, show:

### Search Before Generate
```
mcp__same__search_notes(query="how do hooks work", top_k=5)
```
If the vault has the answer, cite it. If not, generate AND offer to save.

### Cross-Reference
```
mcp__same__find_similar_notes(path="research/hooks/hook-lifecycle.md", top_k=3)
```
"I found related notes you might find useful..."

### Log Decisions
```
mcp__same__save_decision(title="Use Haiku for subagents", body="5x cost savings...", status="accepted")
```

### Session Continuity
```
mcp__same__create_handoff(summary="...", pending="...", blockers="...")
```

### Federated Search
```
mcp__same__search_across_vaults(query="authentication patterns", top_k=10)
```

### Session Context
```
mcp__same__get_session_context()
mcp__same__recent_activity(limit=5)
```

### Grow the Knowledge Base
```
mcp__same__save_note(path="research/project-specific/nextjs-patterns.md", content="...", append=false)
```

The goal: every session should feel smarter than the last because the knowledge compounds.
