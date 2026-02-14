# Contributing to Seed Vaults

We welcome contributions! Whether you're fixing a typo, adding notes to an existing seed, or proposing an entirely new seed, this guide will help you get started.

## Improving Existing Seeds

Good first contributions:

- **Fix factual errors** — correct outdated information or wrong references
- **Add missing notes** — fill gaps in coverage (e.g. a security seed missing a common vulnerability)
- **Improve tags** — add `content_type`, `domain`, or topic tags to improve search relevance
- **Better frontmatter** — ensure YAML frontmatter is consistent with the seed's conventions

For small fixes (typos, broken links), open a PR directly. For larger changes (restructuring categories, adding 10+ notes), open an issue first so we can discuss scope.

## Proposing a New Seed

1. **Open an issue** with a draft `SEED-SPEC.md` using the [`_seed-template/`](./_seed-template/) as your starting point
2. Include: domain, target audience, 5-7 hub categories, estimated note count
3. We'll discuss scope, overlap with existing seeds, and quality expectations
4. Once approved, build the seed and open a PR

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

- All contributions are licensed under [CC BY 4.0](./LICENSE)
- By submitting a PR, you agree to license your contribution under CC BY 4.0
- **AI-generated content must be disclosed** — if notes were generated or substantially written by an AI, say so in the PR description. This is standard practice, not a disqualifier
- See [DISCLAIMER.md](./DISCLAIMER.md) for the project's content notice

## Questions?

Open an issue or start a discussion. We're happy to help you build something useful.
