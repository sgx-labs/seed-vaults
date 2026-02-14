---
title: "Note Architecture for Maximum SAME Effectiveness"
tags: [vault-mastery, architecture, vault-structure, organization]
content_type: research
domain: productivity
workstream: vault-mastery
---

# Note Architecture for Maximum SAME Effectiveness

A vault is not a folder of files. It is a knowledge architecture. How you structure notes determines how well the agent can find, connect, and surface your thinking. This guide covers the structural patterns that make SAME most effective.

## The Hub-and-Spoke Model

Every vault should have hub notes that serve as indexes for a topic area, with research notes radiating outward as detailed spokes.

**Hub notes** (`hub/` directory):
- One per major topic area: `hub/goals.md`, `hub/energy.md`, `hub/systems.md`
- Content type: `hub` (highest confidence baseline at 0.85, never decays)
- Contains brief summaries and links to research notes
- The agent finds these first when a query is broad

**Research notes** (`research/` directory):
- One question or concept per note
- Content type: `research` (0.70 baseline, 90-day decay)
- Contains the depth — evidence, analysis, practical steps
- The agent finds these when a query is specific

Example: `hub/energy.md` links to `research/energy/deep-work-flow.md`, `research/energy/context-switching-cost.md`, and `research/energy/burnout-prevention.md`. A search for "energy" finds the hub. A search for "cost of context switching" finds the specific research note.

## One Question Per Research Note

This is the single most impactful structural rule. Each research note should answer one clear question:

- "How does context switching affect productivity?" → `context-switching-cost.md`
- "What is the two-minute rule and when does it work?" → `two-minute-rule.md`
- "How do I prevent burnout without reducing output?" → `burnout-prevention.md`

Why: embedding models produce a single vector per chunk. One-topic notes produce sharp vectors that match precisely. Multi-topic notes produce blurry vectors that match weakly. See `research/vault-mastery/embedding-science.md` for the details.

## Entity Files for Things That Change

Entity files in `entities/` track things with current state: tools you use, projects you are running, people you work with. They are living documents — the agent updates them as things change.

```
entities/
  me.md          — Your current role, preferences, working style
  work.md        — Current job, team, responsibilities
  projects.md    — Active projects and their status
```

Entity files should be 20-40 lines. They answer: "What is the current state of X?" Keep them lean. When an entity becomes complex, split off research notes for the detailed aspects.

## Decision Log for Settled Questions

Decisions are first-class in SAME. They get the highest confidence baseline (0.90) and never decay. A decision logged six months ago ranks as high as one logged today.

Use `decisions/log.md` as the central decision record, and individual files in `decisions/` for decisions that need detailed reasoning.

Every decision should capture:
- **What** was decided
- **Why** (the reasoning)
- **What was rejected** (alternatives considered)
- **When** it was decided

This prevents re-litigation. When someone (including future you) asks "why do we do it this way?", the agent surfaces the decision with full context.

## Templates for Recurring Processes

Templates in `templates/` standardize recurring activities:

```
templates/
  weekly-review.md      — Weekly review checklist
  monthly-review.md     — Monthly review framework
  project-kickoff.md    — New project setup checklist
  decision-template.md  — Decision documentation format
  daily-capture.md      — Daily capture prompts
```

Templates are not indexed for search (the `skip_dirs` config typically excludes them). They are structural — the agent uses them when creating new notes of that type.

## Archive, Never Delete

When a note is no longer relevant, move it to `_archive/`. Do not delete it. Archived notes leave the search index (SAME skips `_archive/` by default) but remain on disk for reference.

This matters because:
- You might need the information later
- The agent can be told to search archives explicitly if needed
- Deletion is irreversible; archiving is not

## Session Handoffs

The `sessions/` directory is agent-managed. SAME writes session handoffs here automatically. Each handoff captures what was accomplished, what is pending, and any blockers.

Do not manually edit session handoffs. The agent reads them via `get_session_context` at the start of each session. They are the mechanism for continuity between conversations.

## Skip Directories for Noise

Configure SAME to skip directories that contain noise:

```toml
[vault]
  skip_dirs = ["_Raw", "_PRIVATE", "_archive", "templates", "node_modules"]
```

- `_Raw/` — Full-length documents, exports, data dumps. Keep the originals here; create trimmed research notes for the concepts you want searchable.
- `_PRIVATE/` — Sensitive content excluded from search and vault feeds by hardcoded security rules.
- `_archive/` — Displaced notes that should not clutter search results.

## Cross-References Between Notes

Link related notes using relative paths at the bottom of each note:

```markdown
See: `research/energy/context-switching-cost.md`, `research/systems/time-blocking-vs-flexible.md`
```

These links serve two purposes:
1. **Human navigation** — you can follow the trail when reading
2. **Agent context** — when the agent surfaces a note, the cross-references hint at what to search next

## Directory Naming Conventions

Use lowercase, hyphenated directory names that match the terms you search for:

```
research/
  adhd/              — not "attention-topics" or "brain-stuff"
  energy/            — not "focus-and-energy" or "wellness"
  goals/             — not "objectives" or "targets"
  systems/           — not "frameworks" or "methodologies"
  vault-mastery/     — not "meta" or "about-this-vault"
```

SAME includes path components in title-overlap scoring. `research/adhd/task-initiation.md` matches a search for "adhd task initiation" on path alone. Descriptive paths earn free ranking signal.

See: `research/vault-mastery/optimizing-notes-for-search.md`, `research/vault-mastery/frontmatter-guide.md`
