# Seed Spec Template

## Metadata
- **Name**: [seed-name]
- **Type**: Knowledge | Framework | Hybrid
- **Audience**: [who is this for]
- **Note count target**: [50-200]
- **Agent build time**: [estimated hours]

## Purpose
[One paragraph: what question does this seed answer for its audience?]

## Hub Categories
[5-7 top-level topics. Each becomes a hub/ note.]

1. hub/topic-one.md — [what it covers]
2. hub/topic-two.md — [what it covers]
...

## Key Questions (agent research targets)
[10-20 specific questions the seed should answer. Each becomes 1-3 research notes.]

1. [Question]
2. [Question]
...

## Entities
[Key domain concepts that need entity files (20-40 lines each)]

1. entities/thing-one.md — [what it tracks]
2. entities/thing-two.md — [what it tracks]
...

## Decisions to Pre-Populate
[Architectural decisions that should ship with the seed]

1. [Decision and rationale]
...

## Water the Seed Protocol

Every seed must include smart onboarding in its bootstrap.md and CLAUDE.md:

### 1. Scan & Merge
- Agent reads existing CLAUDE.md before suggesting changes
- Proposes merged version — shows diff, waits for approval
- Archives originals to `_archive/` before writing changes
- Never overwrites user work

### 2. Gap Analysis
- After indexing, compare seed coverage against user's actual needs
- Search vault for each key question — identify what's covered, what's missing
- Factor in user's tech stack, workflow, and team setup

### 3. Research Recommendations
- Present numbered list of recommended research topics based on gaps
- Offer to spin up parallel research agents for approved topics
- New research gets saved as notes in the user's vault

### 4. SAME Showcase Behaviors
- Use `same search` to find answers before generating from scratch
- Use `same save_decision` when the user makes a project decision
- Use `same create_handoff` at session end
- Use `same find_similar_notes` to cross-reference related topics
- Use `same search_across_vaults` when the user has multiple seeds
- Surface these capabilities naturally — don't lecture, demonstrate

## CLAUDE.md Governance Template

- Safety rule 1
- Safety rule 2
...

## Config Notes
[Any special SAME config for this seed]

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
