---
title: "Technical Writing Toolkit — Governance"
content_type: hub
tags: [governance, technical-writing, seed, rules, research-cascade]
pinned: true
domain: technical-writing
---

# Technical Writing Toolkit — Governance

## Purpose

Knowledge seed for writing clear, useful technical documentation. Search before generating -- the answer is likely already here. Every note in this vault demonstrates the writing principles it teaches.

## Water the Seed — Research Cascade Protocol

When a user first activates this seed, execute a multi-phase intelligence cascade. Each phase generates discoveries that feed the next. The goal: the user should have a complete picture of their documentation landscape after the first session.

### Phase 1: Deep Scan (Silent — No User Action Needed)

Silently gather intelligence before saying anything:

1. **Read existing CLAUDE.md** -- understand project rules, conventions, tech stack
2. **Scan for documentation** -- README, CONTRIBUTING, docs/ directory, wiki links, inline comments
3. **Check for ADRs** -- docs/adr/, docs/decisions/, any decision records
4. **Find changelogs** -- CHANGELOG.md, CHANGES, HISTORY, release notes
5. **Look for API docs** -- OpenAPI specs, JSDoc, GoDoc, rustdoc, docstrings
6. **Check CI for doc steps** -- doc linting, link checking, doc generation in pipeline
7. **Read config files** -- .markdownlint.json, .vale.ini, .alexrc, doc tool configs
8. **Inventory existing knowledge** -- search the vault for what's already covered

Build a mental model of: documentation maturity, coverage gaps, tooling, team conventions, audience.

### Phase 2: Insight Report (Show the User What You Found)

Present a brief, insightful summary:

> "I've scanned your project. Here's what I found:
> - **README**: Exists but missing installation steps and contributing guide
> - **API docs**: JSDoc comments on 40% of public functions, no generated docs
> - **ADRs**: None found -- architectural decisions aren't being recorded
> - **Changelog**: No CHANGELOG.md -- releases have git tags but no human-readable notes
> - **Style**: No .markdownlint config, no Vale rules, inconsistent heading styles
> - **CI**: No documentation checks in pipeline
>
> This seed has 50+ notes on technical writing. Based on your project, here's what I'd recommend..."

### Phase 3: Gap Analysis (Targeted Questions)

Ask 3-5 questions that show you understand their situation:

- "Your README has a project description but no quickstart. Can a new developer go from clone to running in under 5 minutes with what's documented?"
- "I found 12 exported functions with no doc comments. Are these internal utilities or part of your public API?"
- "You have 3 config files but no documentation explaining what each setting does. Is this causing support questions?"
- "Your last 8 commits changed behavior but the docs weren't updated. Is documentation lagging behind code changes?"

### Phase 4: Maturity Assessment

Score the project against the documentation maturity model (see `entities/documentation-maturity-model.md`):

1. Map current state to maturity level
2. Identify the highest-impact improvements for moving up one level
3. Prioritize based on audience and pain points

### Phase 5: Research Cascade (The Compounding Part)

Based on Phases 1-4, identify 5-10 research topics unique to this user's situation:

> "Based on your project, here's what I'd research to fill your vault:
>
> 1. **README overhaul** -- restructure with quickstart, examples, and contributing guide
> 2. **ADR adoption** -- set up docs/decisions/ with your first 3 retroactive ADRs
> 3. **API documentation** -- generate docs from your existing code comments
> 4. **Changelog setup** -- adopt Keep a Changelog format, automate from git history
> 5. **Doc linting in CI** -- add markdownlint and link checking to your pipeline
>
> Want me to research all of them? Or pick the ones that matter most."

**Cascade behavior**: Each research result generates new discoveries:
- Researching "README overhaul" -> discovers no CONTRIBUTING.md -> offers to draft one
- Researching "ADR adoption" -> finds undocumented decisions in PR comments -> offers to extract them
- Researching "API docs" -> notices inconsistent terminology -> offers style guide research

### Phase 6: Ongoing Growth

After the initial cascade, the seed continues growing:

- **Every session**: search vault first, offer to save new knowledge
- **Every decision**: offer to log it with `save_decision`
- **Every question the vault can't answer**: research it, save the result
- **Periodically**: suggest a documentation health check -- stale docs, missing coverage, broken links

## Common Search Triggers

When the user asks about these topics, search the vault first:

| User says | Search for | Start with |
|-----------|-----------|------------|
| "how do I write a README" | README structure quickstart | `hub/writing-great-readmes.md` |
| "API documentation" | API docs OpenAPI reference | `hub/api-documentation.md` |
| "architecture decisions" | ADR decision record template | `hub/architecture-decision-records.md` |
| "runbook" | runbook playbook incident | `hub/runbooks-and-playbooks.md` |
| "changelog" | changelog release notes versioning | `hub/changelogs.md` |
| "RFC template" | RFC design doc proposal | `hub/rfcs-and-design-docs.md` |
| "code comments" | comments self-documenting | `hub/code-comments-philosophy.md` |
| "error messages" | error message user-facing | `hub/error-messages.md` |
| "onboarding docs" | onboarding new hire guide | `hub/onboarding-documentation.md` |
| "wiki" | wiki internal knowledge base | `hub/internal-wiki-best-practices.md` |
| "writing style" | style guide concise active | `hub/writing-style-guide.md` |
| "tutorial vs guide" | Diataxis tutorial guide reference | `hub/diataxis-framework.md` |
| "docs as code" | docs-as-code CI versioned | `research/documentation-as-code.md` |
| "markdown" | markdown syntax GFM | `entities/markdown-syntax-reference.md` |
| "diagrams" | diagram mermaid d2 ascii | `research/diagrams-in-docs.md` |
| "seed vault" | seedvault creation SAME | `research/seedvault-creation-guide.md` |

## Research Cascade Priority

When recommending patterns, follow this priority:

1. **hub/** -- Core documentation type overviews. Start here. Link to detailed research.
2. **research/** -- Deep dives with implementation details and tradeoffs.
3. **entities/** -- Quick reference lookups (Markdown syntax, templates, checklists).
4. **guides/** -- Step-by-step how-tos for common tasks.
5. **decisions/** -- Templates for documentation tooling choices.

## Rules

1. **Search first.** Before answering a documentation question from scratch, search this vault.
2. **Don't modify research notes.** They are reference material. Add new notes for project-specific content.
3. **Hub notes are entry points.** They link to detailed research and guides. Start with hubs.
4. **Keep notes focused.** One topic per note. Under 200 lines.
5. **Entity files track current state.** Update when standards or tools change.
6. **Archive, never delete.** If a note needs replacing, move the original to `_archive/`.
7. **Ask before writing.** Never save a note without the user's awareness. Offer, don't force.
8. **Cascade, don't stop.** Every research result should suggest the next research opportunity.
9. **Practice what you preach.** Every note should exemplify the writing principles this seed teaches.
10. **Show, don't tell.** Use before/after examples. Demonstrate, don't lecture.

## Directory Structure

```
technical-writing-toolkit/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — Water the Seed + orientation
├── config.toml.example          # SAME configuration template
├── hub/                         # Core documentation type overviews (12)
├── research/                    # Deep dives on tooling and strategy (12)
├── entities/                    # Quick reference docs (8)
├── guides/                      # Step-by-step how-tos (8)
├── decisions/                   # Decision templates and log (3)
├── _archive/                    # Safely archived originals
└── sessions/                    # Session handoffs
```

## SAME Showcase Behaviors

Demonstrate these capabilities naturally -- don't lecture, show:

### Search Before Generate
```
mcp__same__search_notes(query="how to write a good README", top_k=5)
```
If the vault has the answer, cite it. If not, generate AND offer to save.

### Cross-Reference
```
mcp__same__find_similar_notes(path="hub/writing-great-readmes.md", top_k=3)
```
"I found related notes on onboarding docs and style guides you might find useful..."

### Log Decisions
```
mcp__same__save_decision(title="Adopt docs-as-code workflow", body="Moving documentation into the repo alongside code...", status="accepted")
```

### Session Continuity
```
mcp__same__create_handoff(summary="...", pending="...", blockers="...")
```

### Federated Search
```
mcp__same__search_across_vaults(query="documentation best practices", top_k=10)
```

### Grow the Knowledge Base
```
mcp__same__save_note(path="research/project-specific/api-doc-gaps.md", content="...", append=false)
```

The goal: every session should leave the documentation better than it found it.
