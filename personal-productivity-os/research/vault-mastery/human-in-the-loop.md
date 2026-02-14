---
title: "Human in the Loop: Staying in Control of Your Vault"
tags: [vault-mastery, human-in-the-loop, control, agent, curation]
content_type: research
domain: productivity
workstream: vault-mastery
---

# Human in the Loop: Staying in Control of Your Vault

The agent grows your vault, but you own it. SAME is designed so the agent assists and suggests while you make every final call. Understanding this dynamic helps you get the most value without losing control of your knowledge base.

## The Agent Offers, Never Forces

Every vault modification is a suggestion. When the agent wants to save a note, it asks first. When it wants to log a decision, it describes what it will write and waits for your approval. When it wants to update an entity file, it shows you the proposed changes.

You can:
- Say yes — the agent writes the note as proposed
- Modify — "Save it, but change the title to X" or "Add a tag for Y"
- Decline — "Do not save that, it is not worth keeping"

This is not a formality. Your judgment about what is worth keeping shapes the vault's quality over time. A vault where you approve everything becomes noisy. A vault where you curate thoughtfully becomes sharp.

## Review What Was Added

Periodically check what the agent has written. Two tools make this easy:

**Recent activity:** Ask the agent "What notes were added this week?" or use `same search` from the command line. The `recent_activity` tool shows recently modified notes sorted by date.

**Direct reading:** Agent-created notes are just markdown files. Open them in any text editor. Check that the content is accurate, the frontmatter is correct, and the note is filed in the right directory.

Recommended cadence: a quick scan once a week during your weekly review. Five minutes to skim new notes and confirm they reflect your actual thinking.

## Edit Agent-Created Notes

Agent-generated notes are starting points, not sacred texts. Edit them freely:

- Fix inaccuracies — the agent sometimes misunderstands nuance
- Add context — you know things the agent does not
- Refine the title — make it more searchable
- Adjust tags — add domain-specific terms the agent missed
- Restructure — split a note that covers too much, merge notes that overlap

After editing, the next reindex picks up your changes. The embedding updates to reflect the improved content, and future searches find the note more accurately.

## Archive What Does Not Work

Some notes will not prove useful. That is expected — not everything said in a conversation is worth preserving. When you find a note that adds noise without value:

1. Move it to `_archive/` — the directory is excluded from search by default
2. The next reindex removes it from the search index
3. The file still exists on disk if you ever need it

Do not delete notes. Archiving is reversible; deletion is not. A note that seems useless today might be relevant in three months when you return to a topic.

## Shape the Vault Over Time

Your vault is a living system. It should evolve as your work and interests change:

**Promote useful patterns:** When you notice the agent consistently saving a certain type of note that you find valuable, encourage more of it. "I like how you captured that decision. Do that for all architecture choices."

**Discourage noise:** When the agent saves notes you consistently ignore or archive, tell it. "Stop saving meeting summaries — I do not refer back to them." The agent adjusts.

**Reorganize as categories emerge:** If you accumulate 15 notes in a single directory, that is a signal to split into subcategories. Create subdirectories and move notes. Reindex. The agent adapts to the new structure.

**Retire stale workstreams:** When a project ends, archive its notes or update entity files to reflect the new state. The vault should represent current reality, not a museum of past projects.

## Weekly Vault Review

A 10-minute weekly review keeps the vault healthy. Ask the agent:

1. "Show me what was added this week" — scan new notes for accuracy
2. "Are any entity files outdated?" — update entities that have changed
3. "What decisions were logged?" — confirm the reasoning is captured correctly
4. "Any notes I should archive?" — remove noise before it accumulates

This review is the curation step. The agent handles volume; you handle quality. Together, the vault stays useful.

## The Feedback Loop

SAME includes a feedback mechanism. When the agent surfaces a note during a conversation, you can signal whether it was helpful. Useful notes get a small confidence boost via the feedback tool. Over time, frequently useful notes rise in the rankings, and rarely useful notes fall.

This is a light touch — you do not need to rate every note. But when the agent surfaces something particularly helpful or particularly irrelevant, mentioning it calibrates the system.

## The Vault Reflects Your Thinking

After months of use, your vault becomes a map of how you think. Not a perfect map — maps never are — but a useful one. It reflects your decisions, your research, your priorities, and your patterns.

The agent amplifies this. It surfaces connections you forgot, recalls decisions you made months ago, and identifies patterns across hundreds of notes. But the raw material is yours: your conversations, your judgments, your curation.

Stay in the loop. The agent is a powerful collaborator, but the vault is your knowledge base. The best results come from active partnership — the agent proposes, you refine, and the vault improves session by session.

See: `research/vault-mastery/claude-code-vault-workflow.md`, `research/vault-mastery/design-principles-applied.md`
