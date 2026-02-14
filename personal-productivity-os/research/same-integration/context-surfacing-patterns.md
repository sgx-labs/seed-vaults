---
title: "Context Surfacing Patterns"
tags: [same, integration, hooks, context, search, working-memory]
content_type: research
domain: productivity
workstream: same-integration
---

# Context Surfacing Patterns

## The Core Mechanism

SAME's `UserPromptSubmit` hook fires every time you send a message. It takes your input, searches your vault using `search_notes`, and injects the most relevant notes into the agent's context window. This happens before the agent responds.

You do not search. You do not remember which notes exist. You do not navigate folders. You just talk, and the right context appears.

This is the killer feature for productivity.

## Why This Changes Everything

### The Discipline Problem
Every second-brain tool requires you to remember your notes exist and go look for them. This requires exactly the kind of executive function that people who need second-brain tools tend to lack. It is a cruel irony: the tool designed to compensate for poor memory requires good memory to use.

SAME eliminates this entirely. The hook does the remembering. You mention a topic, and relevant notes surface automatically. Zero discipline required.

### The Context Reconstruction Problem
Knowledge workers spend an estimated 20-30% of their time searching for information they already have. Not creating new knowledge — reconstructing context they have lost. "What did we decide about the API?" "Where did I put those meeting notes?" "What was the status of Project Alpha?"

With context surfacing, you never ask these questions. You mention "Project Alpha" in conversation, and the agent already has the status, decisions, and blockers loaded. The conversation starts at full speed.

### The Connection Problem
You have a note about a design pattern and a separate note about a recurring bug. You never connect them because they live in different folders and your search terms never overlap. The insights that live between notes stay invisible.

SAME's semantic search connects notes by meaning, not just keywords. Mention the bug, and the design pattern note surfaces because they are semantically related. The agent makes connections you would never make manually.

## Patterns for Optimizing Context Surfacing

### Use Consistent Project Names
If you call it "Project Alpha" in one note and "the alpha project" in another and "that client thing" in a third, search quality degrades. Pick one name per project and use it everywhere. The agent will find it more reliably.

### Write Notes With Future Retrieval in Mind
When creating a note, ask: "What would I be thinking about when I need this again?" Include those terms. A decision about using a specific technology should mention the problem it solves, not just the technology name.

### Keep Notes Focused
One note, one topic. A note about "Q3 planning, also some thoughts on hiring, plus a reminder about the dentist" will surface for all three queries but will be relevant to none of them. Split it into three notes.

### Tag Meaningfully
Tags improve search precision. Use tags that represent categories you actually think in:
- Project names: `project-alpha`, `website-redesign`
- Domains: `engineering`, `marketing`, `personal`
- Types: `decision`, `blocker`, `idea`, `pattern`

### Update Entity Files Regularly
Entity files in `entities/` are high-value search targets. When the agent searches for a person, project, or tool, the entity file should contain the most current information. Stale entity files surface stale context.

### Use the Decision Log
Logged decisions via `save_decision` are some of the most valuable items the hook surfaces. When you are discussing a topic where a past decision is relevant, the agent brings it forward automatically. This prevents re-litigation and saves hours.

## What Gets Surfaced

The hook returns the top results ranked by semantic similarity and a composite score. What surfaces depends on:

1. **Semantic relevance** — How closely the note's content matches your message
2. **Recency** — More recent notes get a slight boost
3. **Note quality** — Focused, well-tagged notes rank higher than sprawling ones

## What Does NOT Get Surfaced

- Notes in `_Raw/` or `_PRIVATE/` directories (excluded by design)
- Template files (excluded from indexing in config)
- Notes with very low relevance scores (below the composite threshold)

## The Feedback Loop

Context surfacing creates a virtuous cycle:

1. You write notes (or the agent writes them for you via handoffs and decisions)
2. Notes get indexed automatically
3. Future sessions surface those notes when relevant
4. You get better outcomes because you have full context
5. Better outcomes motivate more note-taking

After 50 sessions, the vault is dense with relevant context. Every conversation is richer because every past conversation contributed to the vault. This is the compounding effect described in `research/same-integration/compounding-knowledge.md`.

## Common Anti-Patterns

### Writing for the File System Instead of for Search
Do not organize notes into deep folder hierarchies hoping to "find them later by browsing." Nobody browses. Organize for search: clear titles, good tags, focused content.

### Hoarding Raw Material
A 50-page PDF dumped into the vault will not surface well. Extract the key insights into a focused note. The raw material can live in `_Raw/` for reference.

### Ignoring What Surfaces
When the agent surfaces a note, read it. It surfaced for a reason. If the note is stale or wrong, update or delete it. Ignoring surfaced context defeats the purpose.

## See Also

- `research/same-integration/same-as-executive-function.md` — Context surfacing as working memory
- `research/same-integration/vault-as-second-brain.md` — Active vs. passive systems
- `research/systems/quick-capture.md` — Getting information into the vault
