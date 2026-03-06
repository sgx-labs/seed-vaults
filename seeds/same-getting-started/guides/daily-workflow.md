---
title: "Daily Workflow"
tags: [workflow, daily, routine, habits, productivity, practical]
content_type: guide
domain: same-getting-started
---

# Daily Workflow with SAME

This is what a realistic day looks like when you're using SAME with Claude Code (or any MCP-compatible AI tool).

## Morning: Start a Session

Open your terminal and start Claude Code in your project:

```bash
cd ~/my-project
claude
```

What happens automatically:
1. SAME loads your pinned notes (project overview, coding standards)
2. SAME loads the latest session handoff from yesterday
3. SAME shows recent git activity
4. Your AI knows what you were working on and what's next

You don't need to re-explain anything. Just say:

> "Let's continue where we left off."

Your AI reads the handoff and picks up the task.

## Mid-Morning: Work on Features

As you work, SAME is active in the background:

**When you ask a question:**
> "How does our auth middleware work?"

Your AI searches the vault first, finds your notes on auth, and answers with citations. No hallucination.

**When you make a decision:**
> "Let's use Redis for caching instead of Memcached."

Your AI logs the decision:
```
save_decision:
  title: "Use Redis for caching"
  rationale: "Better data structure support, team familiarity"
  alternatives: "Memcached (simpler but fewer data types)"
```

That decision is now permanent. No future session will suggest Memcached.

**When you discover something useful:**
> "This approach to error handling works really well. Save it as a note."

Your AI saves it to the vault:
```
save_note:
  path: "research/error-handling-pattern.md"
  content: "..."
```

## Lunch Break: Context Preserved

Close your terminal. Go eat. Come back. Start a new session.

SAME loads the handoff from this morning. Your AI knows you were working on the caching integration with Redis. It picks up right where you stopped.

## Afternoon: Research and Exploration

Need to explore a new topic? Use your vault as a starting point:

> "What do we know about rate limiting?"

SAME searches across all your vaults -- project notes, installed SeedVaults, everything. If you have a `claude-code-power-user` seed installed, it might return notes about rate limiting patterns you didn't know were there.

If the vault doesn't have what you need, your AI researches the topic and offers to save the findings:

> "I didn't find notes on rate limiting in your vault. Here's what I found... Want me to save this as a research note?"

Your vault grows with every session.

## End of Day: Create a Handoff

Before closing, your AI creates a handoff:

> "Create a handoff for today's session."

```
create_handoff:
  summary: "Implemented Redis caching layer"
  completed:
    - "Set up Redis connection pool"
    - "Added cache middleware to API"
    - "Wrote 5 integration tests (all passing)"
  in_progress:
    - "Cache invalidation on user profile updates"
  next_steps:
    - "Finish cache invalidation logic"
    - "Add cache hit/miss metrics"
    - "Update deployment config for Redis"
```

Tomorrow morning, your AI reads this and knows exactly what to do.

## Weekly: Review and Maintain

Once a week, spend 10 minutes reviewing your vault:

```bash
same status           # What's in the vault?
same search "stale"   # Any notes that need updating?
```

Look at your decision log. Are there decisions that need revisiting? Update entity files that have changed. The vault should reflect reality.

## The Compound Effect

After a week: Your AI remembers 5 sessions of context.
After a month: Your AI has deep knowledge of your project's history, decisions, and patterns.
After three months: Your vault is a comprehensive knowledge base that no team wiki could match.

Every note saved, every decision logged, every handoff created -- it all compounds. The AI that works with you in month three is dramatically more effective than the one in week one.

## Related Notes

- Session memory: `hub/session-memory.md`
- Connect to Claude Code: `guides/connect-to-claude-code.md`
- Agent handoffs: `research/agent-handoffs-explained.md`
