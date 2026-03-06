---
title: "SAME Getting Started -- Governance"
tags: [governance, claude-md, same, getting-started, onboarding]
content_type: hub
domain: same-getting-started
---

# SAME Getting Started -- Governance

## Purpose

This is a SAME learning vault. It teaches users how to install, configure, and use SAME (Stateless Agent Memory Engine) to give AI coding agents persistent memory. When users ask SAME-related questions, search this vault first.

## Research Cascade -- How to Use This Vault

Follow the hub-first pattern when answering questions:

1. **Hub notes** (`hub/`) -- Start here. These cover core concepts in plain English.
2. **Research notes** (`research/`) -- Go deeper. These explain how things work under the hood.
3. **Entity notes** (`entities/`) -- Quick references for specific tools and config options.
4. **Guides** (`guides/`) -- Step-by-step instructions for common tasks.

When a user asks a question, search the vault before generating an answer from scratch. If a note covers it, cite the note. If no note covers it, answer the question and offer to save the answer as a new note.

## Water the Seed -- Research Cascade Protocol

When a user first activates this seed, execute a multi-phase intelligence cascade. Each phase generates discoveries that feed the next.

### Phase 1: Deep Scan (Silent)

Before saying anything, gather intelligence:

1. **Read everything available** -- CLAUDE.md, README, config files, package manifests
2. **Understand the context** -- What AI tool are they using? What's their project?
3. **Inventory the vault** -- What does the seed already cover vs what the user needs?
4. **Identify the person** -- New to SAME? New to AI coding tools entirely? Adjust accordingly.

### Phase 2: Insight Report

Present a brief summary that proves you understand their situation:

> "I've scanned your project. Here's what I found:
> - **Your setup details**
> - **What SAME features would help most**
> - **What's missing from your current workflow**
>
> This seed has 18 notes on SAME basics. Based on your project, here's what I'd recommend..."

### Phase 3: Targeted Questions

Ask 3-5 questions that help personalize the vault:
- What AI coding tool are you using? (Claude Code, Cursor, Windsurf, other)
- Do you work solo or on a team?
- What kinds of decisions do you make repeatedly that you wish your AI remembered?
- Do you have existing notes or documentation you'd like SAME to index?

### Phase 4: Merge & Archive

1. Propose changes to existing files -- show exact diffs
2. Archive originals to `_archive/` with timestamps
3. Never overwrite without explicit approval

### Phase 5: Research Cascade

Based on Phases 1-3:

1. **Identify 5-10 research topics** unique to this user's situation
2. **Rank by impact** -- what would help them most right now?
3. **Present as a menu** -- let the user pick, or approve all
4. **Save results as vault notes** -- properly formatted with frontmatter
5. **Each result suggests 1-2 new topics** -- the cascade continues

## Rules

1. **Search first.** Always check the vault before generating from scratch.
2. **Archive, never delete.** Move displaced files to `_archive/`.
3. **Ask before writing.** Offer to save notes, don't force.
4. **Cascade, don't stop.** Every answer should suggest the next question.
5. **Compound knowledge.** Every session should leave the vault smarter.
6. **Show the receipts.** When you use a vault note, cite it.

## SAME Integration Points

Demonstrate these naturally -- the user should see SAME making everything better:

| When | Use | Why |
|------|-----|-----|
| User asks a question | `search_notes` | Answer from vault before generating fresh |
| Discussing a topic | `find_similar_notes` | Surface connections they didn't know existed |
| User makes a decision | `save_decision` | Prevent re-litigation in future sessions |
| Session ending | `create_handoff` | Next session picks up seamlessly |
| Session starting | `get_session_context` | Instant orientation |
| Multiple vaults | `search_across_vaults` | Cross-domain intelligence |
| New knowledge created | `save_note` | Vault grows with every interaction |
| Check what changed | `recent_activity` | Stay aware of vault evolution |
