---
title: "Federated Life — Multiple Vaults, One Mind"
tags: [same, integration, federated-search, vaults, domains, organization]
content_type: research
domain: productivity
workstream: same-integration
---

# Federated Life — Multiple Vaults, One Mind

## The Problem With Silos

You have a work life and a personal life. You have side projects and learning goals. You have a household to manage and a career to build. Each domain has its own context, decisions, and momentum.

Most people keep these in completely separate systems — or worse, in one giant system where everything bleeds together. Neither approach works.

Separate systems mean no connections. You cannot search across domains. The insight from your learning vault that would solve your work problem stays invisible. The personal deadline that conflicts with your work sprint never surfaces.

One giant system means no boundaries. Work stress leaks into personal notes. A search for "deadline" returns 200 results across every domain. Context switching between domains requires mental effort to filter out the noise.

## The Federated Model

SAME solves this with multiple vaults and federated search.

### One Vault Per Life Domain

```
~/.config/same/vaults.json

work          → ~/vaults/work
personal      → ~/vaults/personal
side-project  → ~/vaults/side-project
learning      → ~/vaults/learning
```

Each vault is independent. Its own notes, its own decisions, its own session handoffs. When you are working in your work vault, you see work context. Clean boundaries. No bleed.

### Federated Search Connects Them

When you need to see across domains, `search_across_vaults` searches every registered vault simultaneously and merges results by relevance score.

```
search_across_vaults(query="deadline this month", top_k=10)
```

This returns deadlines from your work vault, your personal vault, and your side project — ranked by relevance, annotated with which vault each result came from. One search, complete picture.

### Domain-Specific Agents

Each vault can have its own `CLAUDE.md` with domain-specific governance. Your work vault agent knows your role, your team, your tools. Your personal vault agent knows your goals, your routines, your energy patterns. The agent adapts its behavior to the domain you are in.

## How This Maps to Cognition

The brain does not actually silo information. Ideas from one domain inform another. A management technique you learn in a course applies to your team at work. A debugging strategy from your side project applies to your day job.

But the brain is bad at making these cross-domain connections spontaneously. They happen by accident — in the shower, on a walk, at 2am. Federated search makes them happen on demand.

This is associative memory without the randomness.

## Practical Vault Architecture

### The Work Vault
- Projects, decisions, meeting notes
- Team member entities
- Tool and process documentation
- Work-specific goals and OKRs

### The Personal Vault
- Life goals, habits, health tracking
- Personal projects (home renovation, travel planning)
- Financial decisions
- Relationship and social commitments

### The Learning Vault
- Course notes, book summaries
- Skill development tracking
- Ideas and insights from reading
- Connections to apply in other domains

### The Project Vault (Per Major Project)
- Deep context for one specific initiative
- Architecture decisions, research, timelines
- Can be archived when the project completes

## Cross-Cutting Searches

The real power emerges with queries that span domains:

| Query | What Surfaces |
|-------|--------------|
| "deadline March" | Work sprints + personal tax deadline + side project launch |
| "presentation skills" | Learning vault notes + work presentation feedback |
| "burnout" | Personal energy patterns + work load analysis |
| "budget" | Personal finances + work project budget + side project costs |

These connections happen automatically. No manual linking. No remembering which vault has what.

## Privacy Boundaries

Federated search respects vault boundaries in one critical way: each vault is a separate database. Notes from your personal vault are never stored in your work vault's database. The search happens at query time, and results are merged in memory.

If you share your work vault with a team (future SAME feature), your personal vault remains completely private. The architecture enforces this structurally, not through access controls that can be misconfigured.

## Getting Started

1. Start with one vault (this one — your personal productivity OS)
2. When you start a new domain, create a new vault: `same vault add work ~/vaults/work`
3. Use `same vault default` to switch your active vault for day-to-day work
4. Use `same search --all` or `search_across_vaults` when you need the full picture

Do not over-architect. Two vaults (work + personal) covers most people. Add more only when a domain has enough distinct context to justify separation.

## See Also

- `research/same-integration/same-as-executive-function.md` — Federated search as associative memory
- `research/same-integration/compounding-knowledge.md` — Each vault compounds independently
- `research/systems/organize-not-sort.md` — Why structure matters
