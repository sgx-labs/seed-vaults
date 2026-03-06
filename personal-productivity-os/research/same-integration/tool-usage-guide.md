---
title: "SAME Tool Usage Guide"
tags: [same, integration, tools, mcp, showcase, reference]
content_type: research
domain: productivity
workstream: same-integration
---

# SAME Tool Usage Guide

This vault is a flagship demo of the SAME engine. Every SAME feature should be used naturally during normal productivity work. Here is how each tool maps to a real need:

## `search_notes`
**Use:** Every session. Search for relevant context before asking the user something that was already captured.
**Example:** User says "What did I decide about the vendor?" You search `search_notes(query="vendor decision", top_k=5)` instead of asking them to repeat themselves.

## `find_similar_notes`
**Use:** When surfacing related research or finding connections the user did not know existed.
**Example:** User is reading the burnout prevention note. You run `find_similar_notes(path="research/energy/burnout-prevention.md", top_k=5)` and discover it connects to decision fatigue, context switching, and the energy audit.

## `save_decision`
**Use:** Every time the user makes a non-trivial decision during conversation.
**Example:** User decides to delay the product launch by two weeks. You immediately call `save_decision(title="Delay launch to March 15", body="Need two more weeks for testing. Stakeholders agreed. Risk: competitor may ship first, but quality matters more.", status="accepted")`.

## `create_handoff`
**Use:** At the end of every meaningful session.
**Example:** `create_handoff(summary="Completed Q1 OKR planning, set three key results per objective", pending="Need to share OKRs with manager for alignment", blockers="Budget for Project Alpha still pending finance approval")`.

## `get_session_context`
**Use:** At the start of every session. This is how continuity works — the agent picks up where the last session left off.
**Example:** Session starts, you call `get_session_context()` and learn: last session ended with OKR planning, the user still needs to share OKRs with their manager, and budget is still pending. You open with: "Last time we finished your OKRs. Did you get a chance to share them with your manager?"

## `search_across_vaults`
**Use:** When the user's question might have answers in another vault (if they have multiple vaults registered).
**Example:** User asks about a technical decision but this is their productivity vault. You search across vaults: `search_across_vaults(query="authentication architecture decision", top_k=5)` to find it in their engineering vault.

## `save_note`
**Use:** When creating or updating entity files, research notes, dashboard entries, or any persistent content.
**Example:** User describes a new project. You create it: `save_note(path="entities/projects.md", content="...", append=false)` with the updated project list.

## `recent_activity`
**Use:** To see what has changed since last session and orient quickly.
**Example:** `recent_activity(limit=10)` shows which notes were updated recently, helping you understand what the user has been working on.

## `reindex`
**Use:** After creating multiple new notes, to ensure search covers everything.
**Example:** After the discovery interview creates 5 new entity files: `reindex(force=false)` to make them all searchable immediately.

---

## See Also

- [SAME as Executive Function Prosthesis](same-as-executive-function.md) — how SAME features map to cognitive functions
- [Proactive Agent Patterns](proactive-agent-patterns.md) — anticipatory intelligence using SAME tools
- [Context Surfacing Patterns](context-surfacing-patterns.md) — when and how to surface relevant knowledge
