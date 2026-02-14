---
title: "Claude Code Vault Workflow with SAME"
tags: [vault-mastery, claude-code, workflow, agent, productivity]
content_type: research
domain: productivity
workstream: vault-mastery
---

# Claude Code Vault Workflow with SAME

SAME integrates with Claude Code through hooks that fire automatically during your conversation. You do not need to manage the vault manually — the agent handles search, context loading, and note creation. Your job is to have useful conversations and say yes when the agent offers to save something.

## Starting a Session

When you start a Claude Code session in a SAME-enabled vault, the agent automatically:

1. Calls `get_session_context` — loads pinned notes, the latest handoff, and recent decisions
2. Reads the CLAUDE.md — understands vault structure and conventions
3. Has your context before you type a word

You do not need to say "load my context" or "what was I working on." The hooks handle this. You will see a brief orientation message showing what was loaded.

If the last session ended abruptly (crash, closed terminal), session recovery detects the incomplete handoff and reconstructs your state. Nothing is lost.

## Searching Your Vault

The agent searches automatically when your message relates to something in the vault. Behind the scenes, SAME's `UserPromptSubmit` hook fires on every message, searching for relevant notes and injecting them into the conversation.

You can also search explicitly:
- "What do I know about spaced repetition?" — triggers `search_notes`
- "Find my notes on quarterly planning" — triggers `search_notes`
- "Search all my vaults for deadline" — triggers `search_across_vaults` (federated search)
- "Show me recent decisions" — triggers `recent_activity`

Natural language works. You do not need special syntax or commands.

## Adding Knowledge

The most valuable thing you can do is have substantive conversations with the agent and let it capture the results. When you explain something, make a decision, or work through a problem, the agent recognizes material worth preserving.

The pattern:
1. You discuss a topic with the agent
2. The agent says: "This seems worth saving as a note. Want me to create one?"
3. You say yes (or suggest changes)
4. The agent calls `save_note` with proper frontmatter and files it in the right directory

Every conversation is a deposit into your vault. Over time, this passive capture builds a knowledge base that reflects your actual thinking — not what you intended to write down, but what you actually said and decided.

## Logging Decisions

When you settle a question during a conversation, the agent offers to log it:

- "Let's go with weekly reviews on Friday mornings" → agent offers to call `save_decision`
- The decision captures: what was decided, why, what alternatives were considered
- Future searches for "weekly review" will surface this decision with full reasoning

This prevents re-litigation. Three months from now, when you wonder "why did I switch to Friday reviews?", the agent has the answer.

## Reviewing Vault Health

Ask the agent to audit your vault periodically:

- "What notes were added this week?" — `recent_activity` shows new and modified notes
- "Are any entity files stale?" — agent checks modification dates and flags old entities
- "What decisions have I made this month?" — filtered search on content type
- "Show me notes without frontmatter" — agent can scan for missing metadata
- "What topics are underrepresented in my vault?" — agent analyzes coverage gaps

A monthly vault review (10 minutes) keeps the knowledge base healthy. The agent does the scanning; you make the judgment calls.

## Maintenance Workflows

Common maintenance tasks you can ask the agent to handle:

**Update entity files:** "Update my projects entity — Project Alpha shipped last week, and I started Project Beta." The agent edits `entities/projects.md` with the new state.

**Archive stale notes:** "Move my old Q3 planning notes to archive." The agent moves files to `_archive/` and they leave the search index on next reindex.

**Reindex after changes:** If you manually edit notes outside Claude Code, tell the agent: "I edited some notes, please reindex." This triggers `reindex` to update embeddings.

**Fix frontmatter:** "Check my recent notes for missing content_type fields." The agent scans and offers to add metadata.

## Multi-Vault Workflows

If you have multiple vaults (work, personal, projects), SAME's federated search connects them:

- "Search across all my vaults for deadline" — `search_across_vaults` queries every registered vault
- "What decisions have I made in my work vault?" — filtered search on a specific vault
- Results include the source vault name, so you know where each note lives

Register vaults via the CLI: `same vault add work /path/to/work-vault`. The agent can then search any combination.

## Growing the Vault Over Time

The compound effect is real. Here is what it looks like:

**Week 1:** 30 seed notes. Agent knows your vault structure but not much about you.

**Month 1:** 50-70 notes. Agent knows your projects, recent decisions, and working patterns. Context surfacing starts being genuinely useful.

**Month 3:** 100-150 notes. Agent anticipates your needs. Searching feels like having a knowledgeable colleague. Decisions from two months ago surface when relevant.

**Month 6:** 200+ notes. The vault knows your work better than any single document could. New sessions start with rich context. The agent connects ideas across months of conversation.

The key: you do not need to "build" the vault deliberately. Just have useful conversations with the agent, say yes when it offers to save things, and log decisions when you make them. The vault grows as a byproduct of doing your actual work.

See: `research/vault-mastery/human-in-the-loop.md`, `research/vault-mastery/design-principles-applied.md`
