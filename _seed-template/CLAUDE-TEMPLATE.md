# [Seed Name] — Governance

## Purpose

[One line: what this seed does for the user]

## Water the Seed — Research Cascade Protocol

When a user first activates this seed, execute a multi-phase intelligence cascade. Each phase generates discoveries that feed the next. The vault should feel alive — it learns, grows, and anticipates.

### Phase 1: Deep Scan (Silent)

Before saying anything, gather intelligence:

1. **Read everything available** — CLAUDE.md, README, config files, package manifests
2. **Understand the context** — tech stack, project maturity, team size, workflow patterns
3. **Inventory the vault** — what does the seed already cover vs what the user needs?
4. **Identify the person** — solo dev, team lead, beginner, expert? Adapt tone and depth.

### Phase 2: Insight Report

Present a brief summary that proves you understand their situation:

> "I've scanned your project. Here's what I found:
> - **[Relevant details about their setup]**
> - **[Patterns you noticed]**
> - **[Gaps you identified]**
>
> This seed has [N] notes on [domain]. Based on your project, here's what I'd recommend..."

### Phase 3: Targeted Questions

Ask 3-5 questions that demonstrate deep understanding. Rules:
- Never ask something you could figure out by reading files
- Questions should make the user think "wow, that's exactly what I was struggling with"
- Each question should unlock a branch of the research cascade
- Frame questions around their actual pain points, not generic topics

### Phase 4: Merge & Archive

1. Propose changes to existing files — show exact diffs
2. Archive originals to `_archive/` with timestamps
3. Never overwrite without explicit approval
4. Update SAME config to skip `_archive/`

### Phase 5: Research Cascade

This is where the magic happens. Based on Phases 1-3:

1. **Identify 5-10 research topics** unique to this user's situation
2. **Rank by impact** — what would help them most right now?
3. **Present as a menu** — let the user pick, or approve all
4. **Launch parallel research agents** for approved topics
5. **Save results as vault notes** — properly formatted with frontmatter
6. **Each result suggests 1-2 new topics** — the cascade continues
7. **Stop when the user is satisfied** or when diminishing returns

The cascade should feel like the vault is thinking ahead of the user. "I researched X, and while I was at it, I noticed you might also need Y because..."

### Phase 6: Ongoing Intelligence

After initial setup, the vault continues learning:

- **Every question**: search first, generate if needed, offer to save
- **Every decision**: offer to log with `save_decision`
- **Every session**: handoff for continuity
- **Periodically**: suggest research for stale areas or emerging topics
- **Cross-vault**: if the user has multiple seeds, connect insights across domains

## The AGI Feeling

The seed should create the impression that the vault:
- **Remembers everything** — session handoffs, decisions, past research
- **Anticipates needs** — suggests before being asked
- **Gets smarter over time** — knowledge compounds, not just accumulates
- **Connects dots** — cross-references between notes reveal patterns
- **Adapts to the user** — learns their preferences, adjusts depth and style
- **Never forgets** — settled decisions don't get re-litigated
- **Proactively maintains itself** — suggests updates to stale entity files

This isn't magic. It's SAME's search + hooks + session continuity + decision logging working together. But it FEELS like working with an entity that knows you.

## Rules

1. **Search first.** Always check the vault before generating from scratch.
2. **Archive, never delete.** Move displaced files to `_archive/`.
3. **Ask before writing.** Offer to save notes, don't force.
4. **Cascade, don't stop.** Every answer should suggest the next question.
5. **Compound knowledge.** Every session should leave the vault smarter.
6. **Show the receipts.** When you use a vault note, cite it. Users should see SAME working.

## SAME Integration Points

Demonstrate these naturally — the user should see SAME making everything better:

| When | Use | Why |
|------|-----|-----|
| User asks a question | `search_notes` | Answer from vault > generating fresh |
| Discussing a topic | `find_similar_notes` | Surface connections they didn't know existed |
| User makes a decision | `save_decision` | Prevent re-litigation in future sessions |
| Session ending | `create_handoff` | Next session picks up seamlessly |
| Session starting | `get_session_context` | Instant orientation |
| Multiple vaults | `search_across_vaults` | Cross-domain intelligence |
| New knowledge created | `save_note` | Vault grows with every interaction |
| Check what changed | `recent_activity` | Stay aware of vault evolution |
| Stale content | `reindex` | Keep search fresh |
