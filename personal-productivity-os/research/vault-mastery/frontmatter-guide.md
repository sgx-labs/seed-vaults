---
title: "YAML Frontmatter Guide for SAME"
tags: [vault-mastery, frontmatter, metadata, optimization, search]
content_type: research
domain: productivity
workstream: vault-mastery
---

# YAML Frontmatter Guide for SAME

Every markdown note in your vault can start with a YAML frontmatter block. SAME parses this metadata during indexing and uses it for filtering, ranking, and confidence scoring. Well-structured frontmatter makes your notes significantly easier to find.

## Required Fields

### title

The note's searchable title. SAME uses this for title-overlap ranking — the single strongest ranking signal after vector similarity.

```yaml
title: "Spaced Repetition for Long-Term Retention"
```

Rules: Be specific. Use the words someone would search for. Avoid generic titles.

### tags

A list of 3-5 keywords that describe the note's content. Tags create additional match surface for filtered searches (`search_notes_filtered`). They also influence content type inference if `content_type` is not set.

```yaml
tags: [learning, spaced-repetition, memory, study-techniques]
```

Rules: Mix one broad tag ("learning") with 2-3 specific tags ("spaced-repetition," "memory"). Use lowercase, hyphenated terms. Avoid overly generic tags like "notes" or "important."

### content_type

Determines the note's confidence baseline and decay rate. This directly affects ranking.

```yaml
content_type: research
```

The recognized types, with their confidence baselines and decay behavior:

| Type | Baseline | Decay | Use When |
|------|----------|-------|----------|
| `hub` | 0.85 | Never | Index/overview notes that link to other notes |
| `decision` | 0.90 | Never | Settled questions with reasoning |
| `research` | 0.70 | 90-day half-life | Analysis, findings, reference material |
| `project` | 0.65 | 90-day half-life | Project-specific notes and status |
| `handoff` | 0.60 | 30-day half-life | Session handoffs (agent-managed) |
| `progress` | 0.50 | 30-day half-life | Status updates, check-ins |
| `note` | 0.50 | 60-day half-life | Default for uncategorized notes |

Key insight: `decision` and `hub` notes never decay. A decision made six months ago ranks just as high as one made today. This is intentional — settled decisions should not fade.

### domain

A broad category for your note. Used for filtered searches when you want results from one area of your life.

```yaml
domain: productivity
```

Common domains: `productivity`, `engineering`, `health`, `finance`, `career`, `learning`, `projects`. Choose whatever matches how you think about your life's categories. Be consistent — pick 4-6 domains and use them everywhere.

## Optional Fields

### workstream

A more specific grouping within a domain. Useful for project-based filtering.

```yaml
workstream: vault-mastery
```

Examples: `vault-mastery`, `q1-planning`, `api-redesign`, `home-renovation`. Workstreams are temporary — they come and go with projects.

### pinned

When set to `true`, this note is loaded at the start of every agent session via `get_session_context`. Use sparingly for notes that should always be in context.

```yaml
pinned: true
```

Good candidates for pinning: current quarter's goals, active project priorities, personal operating principles. Bad candidates: research notes, old decisions, anything that does not need daily visibility.

### review_by

A date string indicating when this note should be reviewed. Adds a small confidence boost (0.05) to signal the note is actively maintained.

```yaml
review_by: "2026-03-15"
```

## Good vs. Bad Frontmatter

### Bad

```yaml
---
title: "Notes"
tags: [stuff]
---
```

Problems: Generic title (no ranking signal). One vague tag. No content type (defaults to "note" with fastest decay). No domain (cannot filter).

### Good

```yaml
---
title: "Weekly Review Framework for Knowledge Workers"
tags: [reviews, weekly-review, productivity-systems, reflection]
content_type: research
domain: productivity
workstream: reviews
---
```

Why this works: Specific title matches queries like "weekly review" and "knowledge worker review." Four tags with a mix of broad and specific. Content type is explicit. Domain enables filtering. Workstream groups it with related review notes.

## Frontmatter and Content Type Inference

If you omit `content_type`, SAME infers it from your file path and tags:
- Path contains "decision" → `decision`
- Path contains "research" → `research`
- Path contains "hub" or "index" → `hub`
- Path contains "session" or "handoff" → `handoff`
- Tag includes "decision" → `decision`
- Otherwise → `note`

Explicit is better than inferred. Set `content_type` on every note.

## Tag Strategy

The best tag sets follow this pattern:

1. **One domain tag** — the broad area (`productivity`, `engineering`)
2. **One method/framework tag** — the approach (`gtd`, `okr`, `spaced-repetition`)
3. **Two specific tags** — the particular aspect (`task-initiation`, `weekly-review`)
4. **One meta tag** (optional) — content about content (`vault-mastery`, `process`)

This gives you broad searches that find the note (domain), filtered searches that narrow to the method (framework), and specific searches that pinpoint the topic (specific tags).

See: `research/vault-mastery/optimizing-notes-for-search.md`, `research/vault-mastery/note-architecture.md`
