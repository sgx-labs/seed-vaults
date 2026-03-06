---
title: "Technical Writing Toolkit — Seed Spec"
tags: [seed-spec, planning, structure, quality-checklist, technical-writing]
content_type: hub
domain: technical-writing
---

# Technical Writing Toolkit — Seed Spec

## Metadata
- **Name**: technical-writing-toolkit
- **Type**: Knowledge
- **Audience**: Engineers writing documentation, technical writers, SAME seed creators
- **Note count target**: 45-55
- **Agent build time**: 4-6 hours

## Purpose

Comprehensive reference for anyone writing technical documentation. Covers every major documentation type (READMEs, ADRs, RFCs, runbooks, changelogs), writing craft (style, clarity, structure), tooling (linters, generators, static sites), and meta-skills (measuring quality, paying down doc debt). Self-referential: this seed teaches the exact skills needed to create more seeds, creating a flywheel.

## Hub Categories

1. hub/writing-great-readmes.md — Structure, what to include, what to skip, before/after examples
2. hub/api-documentation.md — OpenAPI, examples-first approach, versioning docs
3. hub/architecture-decision-records.md — ADR format, when to write, finding old decisions
4. hub/runbooks-and-playbooks.md — Structure for incident response, on-call guides
5. hub/changelogs.md — Keep a Changelog, semantic versioning tie-in
6. hub/rfcs-and-design-docs.md — Template, review process, decision recording
7. hub/code-comments-philosophy.md — When to comment, when not to, self-documenting code myth
8. hub/error-messages.md — User-facing, developer-facing, log messages
9. hub/onboarding-documentation.md — New hire docs, project-specific guides
10. hub/internal-wiki-best-practices.md — Structure, ownership, preventing wiki rot
11. hub/writing-style-guide.md — Concise, active voice, scannable
12. hub/diataxis-framework.md — Tutorial vs guide vs reference vs explanation

## Key Questions (agent research targets)

1. Should documentation live in the repo or in a separate wiki?
2. What documentation tool should I use for my project?
3. How do I get my team to actually write documentation?
4. How do I write for an international audience?
5. What diagrams should I include and what tool should I use?
6. How do I make my docs searchable and findable?
7. How do I measure whether my documentation is working?
8. Can AI help me write and maintain documentation?
9. How do I deal with documentation that's out of date?
10. How do I write for beginners and experts in the same docs?
11. What makes open source documentation effective?
12. How do I create a SAME SeedVault?
13. What Markdown features are portable across platforms?
14. How do I start documenting a project that has zero docs?
15. How do I review someone else's documentation?

## Entities

1. entities/diataxis-framework-reference.md — Four documentation modes mapped
2. entities/markdown-syntax-reference.md — Including GFM extensions
3. entities/mermaid-diagram-reference.md — Syntax quick reference
4. entities/common-documentation-templates.md — README, ADR, RFC, runbook, changelog
5. entities/style-checklist.md — Pre-publish quality gate
6. entities/accessibility-in-documentation.md — Inclusive docs practices
7. entities/documentation-maturity-model.md — Levels 0-4 with criteria
8. entities/writing-tools-reference.md — Vale, markdownlint, alex, write-good

## Decisions to Pre-Populate

1. decisions/log.md — Decision log with template
2. decisions/documentation-tool-choice.md — Criteria and tradeoff template
3. decisions/style-guide-adoption.md — Adopting a style guide for your team

## Water the Seed Protocol

### 1. Scan & Merge
- Agent reads existing CLAUDE.md before suggesting changes
- Proposes merged version -- shows diff, waits for approval
- Archives originals to `_archive/` before writing changes
- Never overwrites user work

### 2. Gap Analysis
- After indexing, compare seed coverage against user's actual documentation
- Search vault for each key question -- identify what's covered, what's missing
- Factor in user's project type, team size, and documentation maturity

### 3. Research Recommendations
- Present numbered list of recommended research topics based on gaps
- Offer to spin up parallel research agents for approved topics
- New research gets saved as notes in the user's vault

### 4. SAME Showcase Behaviors
- Use `same search` to find answers before generating from scratch
- Use `same save_decision` when the user makes a documentation decision
- Use `same create_handoff` at session end
- Use `same find_similar_notes` to cross-reference related patterns
- Use `same search_across_vaults` when the user has multiple seeds
- Surface these capabilities naturally -- don't lecture, demonstrate

## Config Notes

```toml
[vault]
  path = "."
  skip_dirs = ["_Raw", "_archive"]
  handoff_dir = "sessions"
  decision_log = "decisions/log.md"

[memory]
  composite_threshold = 0.65
  max_results = 4
```

## Quality Checklist
- [ ] Every note under 200 lines
- [ ] YAML frontmatter on all notes (title, tags, content_type, domain)
- [ ] bootstrap.md with pinned: true and "Water the Seed" section
- [ ] CLAUDE.md with merge protocol and SAME showcase behaviors
- [ ] config.toml.example (provider-agnostic)
- [ ] No PII, no API keys, no secrets
- [ ] `_archive/` in skip_dirs
- [ ] `same reindex` produces 0 errors
- [ ] `same search` returns relevant results for 5 test queries
- [ ] Key questions are answerable by searching the vault
- [ ] SAME tools are demonstrated naturally in CLAUDE.md examples
- [ ] Every note practices the writing principles it teaches
- [ ] Before/after examples in style and craft notes
- [ ] Cross-references between related notes
- [ ] SeedVault creation guide closes the flywheel loop
