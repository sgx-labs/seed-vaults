---
title: "Building a SAME SeedVault"
tags: [guide, seedvault, same, meta, step-by-step, flywheel, documentation]
content_type: guide
domain: technical-writing
---

# Building a SAME SeedVault

## Prerequisites

- SAME CLI installed (`same --version`)
- A topic you know well enough to write 40-60 notes on
- Understanding of the seed structure (see `research/seedvault-creation-guide.md` for the full explanation)

## Step 1: Plan Your Seed (30 minutes)

Create the SEED-SPEC.md first. Answer:

- **Who is the audience?** Be specific. "Backend developers building REST APIs" not "developers."
- **What are the 10-15 core topics?** These become hub notes.
- **What questions should the seed answer?** List 10-15 key questions.
- **What reference material is needed?** These become entity notes.

Write the SEED-SPEC.md with these answers. See `entities/common-documentation-templates.md`.

## Step 2: Create the Directory Structure (5 minutes)

```bash
mkdir -p my-seed/{hub,research,entities,guides,decisions,sessions,_archive}
```

## Step 3: Write the CLAUDE.md (1 hour)

This is the most important file. It tells the AI agent how to use the seed. Include:

1. **Purpose** -- one paragraph describing the seed
2. **Research Cascade Protocol** -- six phases (scan, report, questions, merge, cascade, ongoing)
3. **Common Search Triggers** -- table mapping user questions to vault notes
4. **Rules** -- 8-10 rules for how the agent interacts with the vault
5. **SAME Showcase Behaviors** -- examples of `search`, `save_decision`, `create_handoff`

Model it closely on the api-design-patterns seed's CLAUDE.md.

## Step 4: Write Hub Notes First (2-3 hours)

Write your 10-15 hub notes. Each hub note:

- Covers one topic in 80-150 lines
- Starts with a clear overview
- Includes practical examples (before/after when applicable)
- Has a "See Also" section linking to 3-5 related notes
- Uses YAML frontmatter with `content_type: hub`

Write hubs before research notes. Hubs define the structure; research fills in the details.

## Step 5: Write Research Notes (2-3 hours)

Write 10-15 research notes that go deeper on topics introduced in hub notes:

- Each stays under 200 lines
- Includes implementation details, tradeoffs, and specifics
- Links back to the parent hub note
- Uses YAML frontmatter with `content_type: research`

## Step 6: Write Entity Notes (1-2 hours)

Write 8-10 reference notes:

- Organized for lookup (tables, lists, code blocks)
- Factual, not opinionated
- Pin frequently-used entities (`pinned: true`)
- Uses YAML frontmatter with `content_type: entity`

## Step 7: Write Guide Notes (1-2 hours)

Write 5-8 step-by-step guides:

- Assume a specific starting point
- Number all steps
- Show expected output
- Include time estimates

## Step 8: Create Supporting Files (30 minutes)

- **bootstrap.md** -- pinned onboarding note with search table and key questions
- **README.md** -- public description with install instructions
- **config.toml.example** -- SAME configuration template
- **LICENSE** -- CC BY-SA 4.0 recommended
- **decisions/log.md** -- empty decision log with template

## Step 9: Cross-Reference Everything (1 hour)

Go through every note and verify:
- Every "See Also" section has 3-5 links
- Links use relative paths
- Hub notes link to their research notes and vice versa
- Entity notes are referenced from relevant hubs
- Guides link to the reference material they assume

## Step 10: Quality Check (30 minutes)

Run through the SEED-SPEC.md quality checklist:
- [ ] Every note under 200 lines
- [ ] YAML frontmatter on all notes
- [ ] Consistent tags and content_type fields
- [ ] No broken internal links
- [ ] bootstrap.md is pinned
- [ ] CLAUDE.md has Research Cascade Protocol

## Total Time

8-12 hours for a 40-60 note seed. Split across 2-3 sessions.

## See Also

- `research/seedvault-creation-guide.md` -- Full explanation (the "why" behind each step)
- `hub/diataxis-framework.md` -- How Diataxis maps to seed directories
- `entities/common-documentation-templates.md` -- Templates for all file types
- `hub/writing-style-guide.md` -- Quality standards for note content
