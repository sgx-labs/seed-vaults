---
title: "SAME Design Principles Applied to Your Vault"
tags: [vault-mastery, design-principles, philosophy, same]
content_type: research
domain: productivity
workstream: vault-mastery
---

# SAME Design Principles Applied to Your Vault

SAME is built on eight design principles. Understanding them changes how you use the system — from treating it as a search tool to treating it as a thinking partner that gets smarter over time.

## 1. Ship Seeds, Not Products

*Your vault is never "done" — it grows with every session.*

A seed vault ships with structure and a few starter notes. It is intentionally incomplete. The value comes from what you add over time — your projects, your decisions, your patterns.

What this means for you: do not try to build the perfect vault upfront. Start with the seed structure, have conversations with the agent, and let the vault grow organically. A 30-note vault after day one is more useful than a 200-note vault you spent a weekend writing but never use.

## 2. Teach, Don't Tell

*The agent adapts — detailed for new topics, brief for familiar ones.*

SAME tracks what you have discussed before. When a topic is new to the vault, the agent provides more context and explanation. When a topic has extensive notes, the agent surfaces the key points without re-explaining the basics.

What this means for you: your vault trains the agent's communication style. The more notes on a topic, the more efficiently the agent discusses it with you. Early conversations are longer and more exploratory. Later conversations are tighter and more productive.

## 3. Serve Two Customers

*Notes should be readable by you AND parseable by the agent.*

Every note has two audiences: you (reading it months later) and the agent (searching and surfacing it). Good frontmatter, clear headings, and descriptive titles serve the agent. Clear writing, practical examples, and honest reasoning serve you.

What this means for you: when you write notes (or approve agent-generated notes), ask two questions: "Would I understand this in six months?" and "Would a search for the right terms find this?" If both answers are yes, the note is serving both customers.

Practical tip: use H2 headings to create natural chunks. Each heading becomes a separately searchable unit. "## Why This Matters" is better than a wall of text, for both human scanning and agent retrieval.

## 4. Deposit Before You Withdraw

*Focus on capturing decisions, insights, and patterns — search comes free.*

The unique value of SAME is the write side: session handoffs, decision logs, research notes, entity tracking. Search is important, but it only works if there is something to search. An empty vault with perfect search returns nothing.

What this means for you: bias toward saving things. When the agent offers to save a note, say yes. When you make a decision, log it. When you learn something useful, capture it. The deposits compound. After 50 decisions, the agent can tell you what you decided about anything. After 100 research notes, the agent knows your thinking on every topic you care about.

The asymmetry: capturing takes 10 seconds (the agent writes the note). Searching later saves 10 minutes (instead of re-researching or re-deciding). Every deposit pays forward.

## 5. Every Session Compounds

*Each handoff, each decision, each note makes the next session better.*

Session 1 has no context. Session 2 picks up where Session 1 left off (via the handoff). Session 10 has nine sessions of accumulated context, decisions, and notes. Session 100 starts with a vault that knows your work deeply.

What this means for you: consistency matters more than intensity. Five 20-minute sessions with handoffs create more value than one 3-hour marathon. Each session deposits a little — a decision here, a note there, a handoff with pending items. The compound effect is gradual but powerful.

The handoff is the mechanism. Every session that ends with a handoff makes the next session start faster and smarter.

## 6. Privacy Is Structural

*Sensitive notes go in _PRIVATE/, which is excluded from search and feeds.*

SAME does not rely on access controls, encryption policies, or user promises. Privacy is enforced by architecture: the `_PRIVATE/` directory is excluded from vector search queries at the SQL level and from vault-to-vault feeds by hardcoded path checks.

What this means for you: put genuinely sensitive content in `_PRIVATE/`. Health records, financial details, relationship notes, anything you would not want surfaced in a search result. These files exist on your filesystem (your machine, your control) but are invisible to SAME's search engine.

Everything outside `_PRIVATE/` is searchable. This is the tradeoff: searchability requires the content to be indexed. Design your vault boundaries accordingly.

## 7. Survive the Crash

*Session recovery works even if you close the terminal mid-conversation.*

Sessions end unexpectedly — you close the laptop, the terminal crashes, the power goes out. Normal tools lose your state. SAME's session recovery detects incomplete sessions (handoffs that were never written) and reconstructs what was happening when you return.

What this means for you: you do not need to worry about clean exits. Work naturally. If a session ends abruptly, the next session starts with a recovery message explaining what was in progress. No work is lost because the vault persists independently of the agent session.

This also means the agent can be more ambitious. It can start multi-step workflows (vault reorganization, bulk updates) without fear that a crash will leave things in a broken state.

## 8. Local Until Proven Otherwise

*Your vault stays on your machine, works offline, no cloud dependency.*

SAME runs locally. Your notes are markdown files on your filesystem. Embeddings are generated by a local model (Ollama). The SQLite database lives next to your vault. No internet connection is required for search, indexing, or session management.

What this means for you: your knowledge base is yours. It does not live on someone else's server. It cannot be shut down by a company pivoting or deprecating a product. It works on airplanes, in coffee shops with bad WiFi, and during internet outages. You can back it up, version-control it, or move it to another machine by copying files.

The "until proven otherwise" part: if a cloud feature someday offers clear value (cross-device sync, collaborative vaults), it will be opt-in and additive. The local-first foundation does not change.

See: `research/vault-mastery/human-in-the-loop.md`, `research/same-integration/same-as-executive-function.md`
