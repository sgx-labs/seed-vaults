---
title: "The Diataxis Framework"
tags: [diataxis, tutorial, how-to, reference, explanation, documentation-types]
content_type: hub
domain: technical-writing
---

# The Diataxis Framework

## The Four Modes of Documentation

Diataxis (from the Greek "dia taxis" -- "across arrangement") identifies four distinct types of documentation. Each serves a different need. Most documentation problems come from mixing them.

|  | **Learning** | **Working** |
|---|---|---|
| **Practical** | Tutorial | How-to Guide |
| **Theoretical** | Explanation | Reference |

### Tutorial

**Purpose:** Teach a beginner by doing.

**Structure:** A guided lesson that takes the reader through a series of steps to complete a meaningful project. The reader learns by doing, not by reading.

**Example:** "Build a REST API in 30 minutes" -- walks through creating endpoints, connecting a database, and testing with curl.

**Characteristics:**
- The author decides the path. The reader follows.
- Every step produces a visible result.
- Minimal explanation -- just enough to keep moving.
- Repeatable: following the steps always works.

**Common mistake:** Stopping to explain theory in the middle of a tutorial. Save that for Explanation docs.

### How-to Guide

**Purpose:** Help someone accomplish a specific task.

**Structure:** A series of steps that solve a problem the reader already has. Unlike tutorials, guides assume the reader has context and just needs the recipe.

**Example:** "How to add rate limiting to your API" -- assumes you have a working API and need to add this specific feature.

**Characteristics:**
- The reader comes with a goal. The guide helps them achieve it.
- Focused on a single task.
- Assumes existing knowledge.
- Flexible -- the reader adapts steps to their situation.

**Common mistake:** Writing a tutorial when you mean a guide. If the reader already knows what they want to do, skip the hand-holding.

### Reference

**Purpose:** Describe the machinery. Facts, not opinions.

**Structure:** Comprehensive, accurate, and organized for lookup. API references, configuration options, CLI flags, status codes.

**Example:** The `entities/` directory in this seed -- Markdown syntax, Mermaid diagrams, template collections.

**Characteristics:**
- Organized by structure (alphabetical, by category), not by use case.
- Complete -- covers everything, not just common cases.
- Consistent format -- every entry looks the same.
- Terse -- no stories, no persuasion.

**Common mistake:** Putting tutorials inside reference docs. "Here's how to use this function..." belongs in a guide, not a reference.

### Explanation

**Purpose:** Help someone understand *why*.

**Structure:** Discursive prose that explores a topic from multiple angles. Context, history, design decisions, tradeoffs.

**Example:** "Why we chose REST over GraphQL" -- discusses tradeoffs, team context, and decision rationale.

**Characteristics:**
- No steps to follow.
- Multiple perspectives considered.
- Can be read at any time -- not tied to a task.
- Answers "why?" and "what about...?"

**Common mistake:** Mixing explanation with reference. Configuration docs shouldn't explain the philosophy of configuration.

## How to Apply This

When you sit down to write documentation, ask: **What mode am I in?**

- "I want to teach a beginner" -> Write a **tutorial**
- "I want to help someone do X" -> Write a **how-to guide**
- "I want to document what exists" -> Write a **reference**
- "I want to explain why" -> Write an **explanation**

Then stay in that mode. Don't switch mid-document. If you find yourself explaining theory in a tutorial, split it out into a separate explanation document and link to it.

## Mapping to This Seed

| Diataxis Mode | Seed Directory | Example |
|--------------|---------------|---------|
| Tutorial | `guides/` | `guides/starting-documentation-from-zero.md` |
| How-to Guide | `guides/` | `guides/writing-your-first-adr.md` |
| Reference | `entities/` | `entities/markdown-syntax-reference.md` |
| Explanation | `hub/`, `research/` | `hub/code-comments-philosophy.md` |

## See Also

- `entities/diataxis-framework-reference.md` -- Quick reference card
- `hub/writing-style-guide.md` -- Style principles that apply to all four modes
- `research/writing-for-different-audiences.md` -- Adapting content for skill levels
- `hub/api-documentation.md` -- API docs as reference documentation
