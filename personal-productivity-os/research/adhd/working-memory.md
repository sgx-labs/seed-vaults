---
title: "Working Memory: Why SAME Changes Everything"
tags: [adhd, working-memory, external-brain, context, productivity, same]
content_type: research
domain: productivity
workstream: adhd
---

# Working Memory: Why SAME Changes Everything

## The ADHD Working Memory Problem

Working memory is the brain's scratchpad — the ability to hold information in mind while using it. It is what lets you follow multi-step instructions, remember what you were about to say, hold context during a complex task, and recall a decision you made yesterday about how to approach a problem.

ADHD significantly impairs working memory. Research shows that working memory deficits affect ADHD brains broadly, not just in specific task types. This means:

- You walk into a room and forget why
- Someone gives you three instructions and you remember one
- You make a decision, then re-make it a week later because you forgot the reasoning
- You lose track of where you are in a multi-step process
- Important context evaporates between work sessions

## Why Traditional Note-Taking Fails

The standard advice is "write everything down." This helps, but it has a fatal flaw for ADHD brains: **you have to remember to check your notes.**

The system fails at the point of retrieval, not capture. You might write excellent notes, build a beautiful second brain, organize everything perfectly — and then never look at it again because checking your notes requires the same working memory and task initiation that you do not have.

This is the graveyard of productivity systems: Notion databases with 500 pages nobody reads, to-do apps with 200 unchecked items, journals that get filled for two weeks and then abandoned. The capture happened. The retrieval never does.

## The Push vs. Pull Problem

Traditional tools are **pull-based**: you have to go to them, search them, read them, figure out what is relevant. Every step requires executive function.

| Pull-Based (Traditional) | Push-Based (SAME) |
|--------------------------|-------------------|
| You open your notes | Notes are surfaced to you |
| You search for context | Context is loaded automatically |
| You remember to check | The hook fires every session |
| You decide what is relevant | The agent ranks by relevance |
| You review your decisions | Decisions appear in orientation |

SAME is **push-based**. Through Claude Code hooks, context is delivered to you at the start of every session. You do not have to remember to look — the system finds you.

## What Working Memory Failure Looks Like at Work

- **Decision re-litigation** — "Did we decide to use PostgreSQL or MySQL?" You decided PostgreSQL three weeks ago with clear reasoning. But without working memory of that decision, the team debates it again.
- **Context loss between sessions** — You spend 20 minutes at the start of every work session trying to remember where you were. What was I doing? What was the next step? What did I try that did not work?
- **Dropped threads** — You told a colleague you would send them something. It evaporated from memory within the hour.
- **Repeated research** — You looked up how to configure that setting last month. You cannot remember the answer. You look it up again.

Each of these has a cost in time, energy, credibility, and confidence. Over years, the accumulated cost is staggering.

## The External Brain That Actually Works

An effective external brain for ADHD needs to be:

1. **Automatic** — Capture happens without you deciding to capture
2. **Proactive** — Retrieval happens without you deciding to retrieve
3. **Contextual** — What surfaces is relevant to what you are doing now
4. **Persistent** — Nothing is lost between sessions, between days, between months
5. **Low-friction** — No complex filing, tagging, or organizational overhead

This is exactly what SAME provides.

## How SAME Helps

This is SAME's core value proposition for ADHD brains:

- **Automatic session context** — `get_session_context` fires at the start of every Claude Code session. It loads your last handoff (where you left off), pinned notes (persistent priorities), and recent decisions. This is 80% of the working memory problem solved in one hook.
- **Decision persistence** — `save_decision` captures decisions with full reasoning. Weeks later, `search_notes` can find them. No more re-litigation.
- **Session handoffs** — `create_handoff` at the end of a session captures what was done, what is pending, and blockers. Next session, it all comes back. The gap between sessions is bridged automatically.
- **Semantic search** — You do not need to remember where you filed something. Describe what you need in natural language and the agent finds it. "What was that decision about the database?" works.
- **Context without effort** — You never have to organize, file, tag, or maintain the system. You just work. The agent handles persistence.
- **Multi-session continuity** — A project that spans 50 sessions has a continuous thread of context. Each session picks up where the last one ended. Your working memory for the project is effectively unlimited.

The difference between SAME and a note-taking app is the difference between a search engine and a library card catalog. The card catalog requires you to know what you are looking for and where to find it. The search engine just needs a question.

For ADHD brains, that difference is everything.

See: `research/adhd/executive-function.md`, `research/adhd/adhd-friendly-systems.md`
