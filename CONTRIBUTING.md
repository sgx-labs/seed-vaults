# Contributing to Seed Vaults

We welcome contributions! Whether you're fixing a typo, adding notes to an existing seed, or proposing an entirely new seed, this guide will help you get started.

## Improving Existing Seeds

Good first contributions:

- **Fix factual errors** — correct outdated information or wrong references
- **Add missing notes** — fill gaps in coverage (e.g. a security seed missing a common vulnerability)
- **Improve tags** — add `content_type`, `domain`, or topic tags to improve search relevance
- **Better frontmatter** — ensure YAML frontmatter is consistent with the seed's conventions

For small fixes (typos, broken links), open a PR directly. For larger changes (restructuring categories, adding 10+ notes), open an issue first so we can discuss scope.

## Contributing a New Seed

We actively encourage community seed contributions. The best seeds come from real expertise — if you know a topic well enough to help others, you have a seed worth sharing.

**Start small.** Even 10 well-written notes on a focused topic is valuable. You can always add more later.

### Getting started

1. **Copy the template** — use [`_seed-template/`](./_seed-template/) as your starting point
2. **Fill in `SEED-SPEC.md`** — define your domain, audience, and 5-7 hub categories
3. **Write your notes** — one concept per file, under 200 lines each
4. **Add a `CLAUDE.md`** — teach the agent how to use your domain knowledge
5. **Test locally** (see below)
6. **Open a PR** with your seed

For a detailed walkthrough of the entire process, see the [Building a SAME SeedVault](./seeds/technical-writing-toolkit/research/seedvault-creation-guide.md) guide.

You don't need to open an issue first for new seeds. Just submit a PR. If we have questions about scope or overlap, we'll discuss in the review.

### How to test your seed locally

Before submitting, verify your seed works with SAME:

```bash
# Navigate to your seed directory
cd your-seed-name/

# Initialize and index
same init
same reindex

# Test that notes surface for relevant queries
same search "your primary use case"
same search "a specific concept from your notes"
same search "something a user would actually ask"
```

Good signs:
- Relevant notes appear in the top 3 results
- Tags and frontmatter are parsed correctly
- No indexing errors

### What makes a good seed PR

Here are patterns we see in strong contributions:

- **Focused scope** — "Python testing patterns" is better than "everything about Python"
- **Real expertise** — notes that reflect hands-on experience, not just documentation summaries
- **Good test queries** — PR description includes 2-3 queries that demonstrate the seed works
- **Consistent structure** — follows the template layout (hub/, research/, entities/)
- **Complete frontmatter** — every note has title, content_type, domain, and tags

### Examples of contribution ideas

- A DevOps engineer shares their Terraform module patterns (20 notes)
- A data scientist contributes pandas/numpy recipes they use daily (15 notes)
- A designer adds accessibility audit checklists (12 notes)
- A hobbyist shares their sourdough baking knowledge (10 notes)

### Proposing a seed via issue (optional)

If you want feedback before building, you can open an issue with a draft `SEED-SPEC.md`:

1. Include: domain, target audience, 5-7 hub categories, estimated note count
2. We'll discuss scope, overlap with existing seeds, and quality expectations
3. Once we align, build the seed and open a PR

Use the template structure — it ensures your seed works with `same seed install` out of the box.

## Quality Standards

Every note must meet these requirements:

- [ ] **YAML frontmatter** — `title`, `content_type`, `domain`, and `tags` at minimum
- [ ] **Under 200 lines** — notes are for retrieval, not documentation. Split long content
- [ ] **No PII** — no real names, emails, API keys, or identifying information
- [ ] **One concept per file** — a note should answer one question well
- [ ] **Test with SAME** — run `same reindex && same search "your test query"` to verify notes surface correctly

### Content-type tags

Use standard content types so SAME can weight results appropriately:

| Tag | Use for |
|-----|---------|
| `decision` | Architectural decisions, trade-off analyses |
| `entity` | Living reference docs (tools, platforms, standards) |
| `hub` | Category index notes linking to related research |
| `research` | Deep-dive reference notes on a single concept |
| `template` | Fill-in templates and checklists |
| `guide` | Step-by-step how-to notes |

### Disclaimers

Seeds covering health, medical, financial, or legal topics **must** include per-note disclaimers. Example:

```yaml
---
title: Beginner Strength Training Program
content_type: guide
domain: fitness
tags: [strength-training, beginner, programming]
disclaimer: >
  This content is AI-generated and for informational purposes only.
  Consult a qualified professional before starting any exercise program.
---
```

## PR Process

1. **Fork** this repository
2. **Branch** from `main` — use a descriptive name (e.g. `add-kubernetes-notes`, `new-seed-data-engineering`)
3. **One seed per PR** — don't mix changes across multiple seeds
4. Include in your PR description:
   - Note count (new/modified/deleted)
   - 2-3 test queries that demonstrate the notes work with SAME search
   - Any disclaimers needed for the domain
5. **Submit** — we'll review within a few days

### What we look for in review

- Notes follow the quality standards above
- Frontmatter is complete and consistent
- Content is accurate and useful for AI retrieval
- No overlap or contradiction with existing notes in the same seed
- `SEED-SPEC.md` is updated if the seed's scope changed

## Legal

- All contributions are licensed under [CC BY-SA 4.0](./LICENSE)
- By submitting a PR, you agree to license your contribution under CC BY-SA 4.0
- **AI-generated content must be disclosed** — if notes were generated or substantially written by an AI, say so in the PR description. This is standard practice, not a disqualifier
- See [DISCLAIMER.md](./DISCLAIMER.md) for the project's content notice

## Questions?

Open an issue or start a discussion. We're happy to help you build something useful.
