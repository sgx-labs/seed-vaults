---
title: "Internal Wiki Best Practices"
tags: [wiki, knowledge-base, internal-docs, organization, wiki-rot, documentation]
content_type: hub
domain: technical-writing
---

# Internal Wiki Best Practices

## The Wiki Problem

Every team starts a wiki with good intentions. Within a year, it becomes a graveyard of outdated pages that nobody trusts, nobody maintains, and everybody complains about. This is preventable.

## What Belongs in a Wiki

Wikis are best for content that:
- **Changes slowly** -- team processes, product context, how-we-work guides
- **Doesn't belong in the repo** -- meeting notes, team agreements, vendor comparisons
- **Needs collaborative editing** -- shared documents that multiple people maintain
- **Is discovered by browsing** -- organizational knowledge people stumble upon

What does NOT belong in a wiki:
- **Code documentation** -- this belongs in the repo (see `research/documentation-as-code.md`)
- **API references** -- generate from code or OpenAPI specs
- **Runbooks** -- these belong near the code they operate on
- **ADRs** -- these belong in the repo for version control

## Structure That Scales

```
Home
├── Team
│   ├── Who's who (roles, areas of ownership)
│   ├── How we work (rituals, communication norms)
│   └── On-call rotation and escalation
├── Product
│   ├── Product overview and goals
│   ├── User personas
│   └── Feature specs (linked, not embedded)
├── Engineering
│   ├── Architecture overview (links to repo ADRs)
│   ├── Vendor and tool decisions
│   └── Environment guide (staging, production)
└── Onboarding
    ├── First week checklist
    ├── Key contacts
    └── Links to repo-based setup docs
```

**Key principle:** The wiki is a table of contents that links to the right place. Technical docs link to the repo. Product docs link to the spec tool. Meeting notes live in one place.

## Preventing Wiki Rot

Wiki rot happens when pages accumulate without ownership. Fight it with:

### Ownership
Every page has an owner. Display it prominently. The owner is responsible for accuracy, not necessarily for writing -- they just ensure someone reviews it periodically.

### Expiration Dates
Add a "Last reviewed" date to every page. Set up automated reminders:
- Process docs: review every 6 months
- Architecture overviews: review every 3 months
- Onboarding docs: review after every new hire

### Pruning
Schedule a quarterly "wiki gardening" session:
1. Archive pages not updated in 12+ months
2. Merge duplicate pages
3. Fix broken links
4. Delete pages with zero views in the last 6 months

### Entry Points
Nobody uses a wiki with 200 pages and no map. Maintain:
- A clear home page with the top 10 most-used links
- A "start here" page for new hires
- Consistent naming conventions so pages are findable by search

## Common Wiki Mistakes

- **No structure.** A flat list of 200 pages. Use categories and a clear hierarchy.
- **No ownership.** Everyone's responsibility is no one's responsibility.
- **Duplicating repo docs.** Architecture docs in both the repo and the wiki. Pick one source of truth and link.
- **Meeting notes as knowledge.** Meeting notes are ephemeral. Extract decisions and action items into proper pages.
- **Perfection paralysis.** A rough page that exists is better than a perfect page that doesn't. Write something, improve it later.

## See Also

- `hub/onboarding-documentation.md` -- Wiki as part of the onboarding experience
- `hub/architecture-decision-records.md` -- Keep ADRs in the repo, not the wiki
- `research/documentation-as-code.md` -- The case for docs in the repo
- `research/documentation-debt.md` -- Identifying and paying down wiki rot
