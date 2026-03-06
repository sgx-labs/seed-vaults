---
title: "SeedVault Creation Guide"
tags: [seedvault, same, meta, creation, flywheel, knowledge-base, documentation]
content_type: research
domain: technical-writing
---

# SeedVault Creation Guide

## The Flywheel

This note teaches you how to create SAME SeedVaults -- which is exactly how this seed was created. Understanding seed creation makes you better at technical writing. Being better at technical writing makes you create better seeds. The flywheel spins.

## What Is a SeedVault?

A SeedVault is a curated knowledge base designed for SAME (Stateless Agent Memory Engine). It's a directory of Markdown files with YAML frontmatter, organized into categories, that an AI agent can search, reference, and grow over time.

Seeds are:
- **Pre-built knowledge** that agents use to answer questions
- **Living documents** that grow with every session
- **Portable** -- move them between projects, share them with teams
- **Self-documenting** -- they teach the agent how to use them

## Anatomy of a SeedVault

```
my-seed/
  README.md              # Public-facing description
  CLAUDE.md              # Agent governance and Research Cascade Protocol
  SEED-SPEC.md           # Planning doc: structure, targets, checklist
  bootstrap.md           # Pinned onboarding note (read every session)
  config.toml.example    # SAME configuration template
  LICENSE                # CC BY-SA 4.0 recommended
  hub/                   # Core topic overviews (10-15 notes)
  research/              # Deep dives (10-15 notes)
  entities/              # Quick reference (8-10 notes)
  guides/                # Step-by-step how-tos (5-8 notes)
  decisions/             # Decision templates and log
  sessions/              # Session handoffs
  _archive/              # Safely archived originals
```

## Step-by-Step Creation

### 1. Choose Your Domain

Pick a topic you know well or want to learn deeply. Good seed topics:
- Have 30-60 notes worth of content
- Cover a domain with clear subtopics
- Are useful across multiple projects
- Don't become obsolete quickly

### 2. Write the SEED-SPEC.md First

Plan before you write. The seed spec defines:
- **Audience** -- who will use this seed?
- **Hub categories** -- what are the 10-15 core topics?
- **Key questions** -- what questions should the seed answer?
- **Entities** -- what reference material is needed?
- **Quality checklist** -- how will you know it's done?

### 3. Create the CLAUDE.md

This is the most important file. It tells the agent:
- How to use the seed (Research Cascade Protocol)
- What to search for and where
- Rules for interacting with the knowledge base
- How to demonstrate SAME capabilities

Model it after the api-design-patterns seed's CLAUDE.md.

### 4. Write Hub Notes First

Hubs are entry points. They:
- Cover one topic in 100-150 lines
- Link to related research, entities, and guides
- Include a "See Also" section
- Use practical examples, not theory

### 5. Write Research Notes

Deep dives that support hub notes. They:
- Go deeper into implementation, tradeoffs, and specifics
- Stay under 200 lines
- Include before/after examples where applicable
- Link back to their parent hub and related notes

### 6. Write Entity Notes

Quick reference material. They:
- Are organized for lookup, not reading
- Use tables, lists, and code blocks heavily
- Stay factual -- no opinions or recommendations
- Get pinned if they're used frequently

### 7. Write Guide Notes

Step-by-step walkthroughs. They:
- Assume a specific starting point
- Have numbered steps
- Show expected output at each step
- Link to reference material for details

### 8. Create the Bootstrap and README

- **bootstrap.md** -- the first note the agent reads. Orientation, search table, key questions.
- **README.md** -- public-facing description with install instructions.

## Quality Standards

Every note should:
- Have YAML frontmatter (title, tags, content_type, domain)
- Stay under 200 lines
- Cross-reference related notes with relative paths
- Practice the writing principles it teaches (for writing-related seeds)
- Include a "See Also" section linking to 3-5 related notes

The seed overall should:
- Have 40-60 notes
- Cover the domain comprehensively enough that an agent can answer most questions
- Demonstrate SAME capabilities naturally in the CLAUDE.md
- Pass the quality checklist in SEED-SPEC.md

## Common Seed Mistakes

- **Too many notes.** 100 shallow notes are worse than 50 deep ones.
- **No cross-references.** Notes that don't link to each other create dead ends.
- **Missing CLAUDE.md Research Cascade.** Without it, the agent doesn't know how to use the seed.
- **Inconsistent frontmatter.** Every note needs the same fields for indexing.
- **Theory without examples.** Show, don't tell.

## See Also

- `guides/building-a-seedvault.md` -- Condensed step-by-step version
- `hub/diataxis-framework.md` -- How Diataxis maps to seed directories
- `hub/writing-style-guide.md` -- Writing quality for seed notes
- `entities/common-documentation-templates.md` -- Templates for seed files
- `research/documentation-as-code.md` -- Seeds as docs-as-code
