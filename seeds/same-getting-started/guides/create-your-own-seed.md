---
title: "Create Your Own Seed"
tags: [seedvault, create, template, sharing, community, authoring]
content_type: guide
domain: same-getting-started
---

# Create Your Own SeedVault

A SeedVault is a collection of markdown notes on a specific topic, packaged so anyone can install it and get expert-level knowledge in their vault. Here's how to build one.

## Step 1: Plan Your Seed

Before writing, answer these questions:

1. **What topic does it cover?** (e.g., "Kubernetes operations", "React testing patterns")
2. **Who is it for?** (e.g., "developers new to K8s", "mid-level React devs")
3. **What questions should it answer?** List 10-15 specific questions.
4. **How many notes?** Target 15-50 for a focused seed, 50-150 for a comprehensive one.

## Step 2: Create the Structure

```bash
mkdir -p my-seed/{hub,research,entities,decisions,guides,sessions,_archive,_Raw}
```

Required files:
```
my-seed/
  README.md              # What the seed is, how to install
  CLAUDE.md              # Agent governance rules
  SEED-SPEC.md           # Metadata and planning doc
  LICENSE                # CC BY-SA 4.0 recommended
  bootstrap.md           # Pinned onboarding note
  config.toml.example    # Provider-agnostic config
  hub/                   # 5-7 core concept notes
  research/              # 10-20 detailed deep dives
  entities/              # 10-20 quick reference cards
  decisions/             # Pre-populated decisions
  guides/                # Step-by-step how-tos
```

## Step 3: Write Hub Notes First

Hub notes are the entry points. Each covers a broad topic area:

```markdown
---
title: "Kubernetes Basics"
tags: [kubernetes, basics, overview, containers]
content_type: hub
domain: kubernetes-ops
---

# Kubernetes Basics

Plain English explanation of what K8s is...
```

Rules for hub notes:
- One per major topic area (5-7 total)
- Plain English, no jargon without explanation
- Link to research and entity notes for deeper dives
- Under 200 lines

## Step 4: Write Research Notes

Research notes answer specific questions in depth:

```markdown
---
title: "How Pod Scheduling Works"
tags: [kubernetes, scheduling, pods, nodes]
content_type: research
domain: kubernetes-ops
---

# How Pod Scheduling Works

Detailed explanation...
```

Rules for research notes:
- One question per note
- Include commands, examples, and real scenarios
- Under 200 lines

## Step 5: Write Entity Notes

Entity notes are quick references for specific tools, concepts, or services:

```markdown
---
title: "kubectl"
tags: [kubernetes, kubectl, cli, reference]
content_type: entity
domain: kubernetes-ops
---

# kubectl

What it is, key commands, common patterns...
```

Rules for entity notes:
- 20-40 lines each
- Factual, not opinionated
- Include the most common commands or settings

## Step 6: Write CLAUDE.md

Your CLAUDE.md tells AI agents how to use the seed. Include:

1. **Purpose** -- What the seed is for
2. **Research Cascade** -- How to navigate hub -> research -> entities
3. **Water the Seed protocol** -- How to onboard new users
4. **Rules** -- Search first, archive never delete, etc.
5. **SAME integration points** -- When to use which SAME tool

Use the template from `_seed-template/CLAUDE-TEMPLATE.md` as a starting point.

## Step 7: Write bootstrap.md

The bootstrap note is pinned and loaded every session. It should:

- Explain the Water the Seed onboarding process
- List where to find things (table of hub notes)
- List the key questions the seed answers
- Describe the directory structure

## Step 8: Test Your Seed

```bash
cd my-seed
same init
same reindex
```

Verify with test searches:

```bash
same search "your key question 1"
same search "your key question 2"
same search "your key question 3"
```

Every key question should return relevant results.

## Step 9: Quality Checklist

Before sharing, verify:
- [ ] Every note under 200 lines
- [ ] YAML frontmatter on all notes
- [ ] bootstrap.md with `pinned: true`
- [ ] CLAUDE.md with Research Cascade and SAME integration
- [ ] config.toml.example (provider-agnostic)
- [ ] No PII, no API keys, no secrets
- [ ] `_archive/` in skip_dirs
- [ ] `same reindex` produces 0 errors
- [ ] Key questions are answerable by searching

## Step 10: Share It

Push to GitHub and submit to the SeedVaults repository, or share however you prefer.

## Related Notes

- SeedVaults deep dive: `research/seed-vaults-deep-dive.md`
- Vault structure: `hub/vault-basics.md`
- CLAUDE.md reference: `entities/claude-md.md`
