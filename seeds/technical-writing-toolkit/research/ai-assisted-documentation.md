---
title: "AI-Assisted Documentation"
tags: [ai, llm, automation, drafting, review, maintenance, documentation]
content_type: research
domain: technical-writing
---

# AI-Assisted Documentation

## Where AI Actually Helps

AI is a powerful documentation tool when used correctly. It excels at tasks that are tedious for humans but easy for machines. It struggles with tasks that require judgment, domain knowledge, or understanding your users.

### Strong Use Cases

**Drafting from structure.** Give an AI your code, API schema, or outline and ask for a first draft. You'll spend 30 minutes editing instead of 2 hours writing from scratch.

**Consistency checking.** "Does this doc use the same terminology as the rest of our docs?" AI can spot when you call something a "config" on one page and "settings" on another.

**Generating examples.** AI is good at producing realistic code examples, sample API requests, and test data. Always verify they actually work.

**Summarizing changes.** Feed an AI a git diff and ask for a changelog entry. It handles the tedious part; you handle the editorial judgment.

**Translation assistance.** AI can translate documentation to other languages as a starting point, though human review is essential for technical accuracy.

**Readability review.** "Is this paragraph too complex? Simplify it." AI gives useful suggestions for reducing sentence complexity and removing jargon.

### Weak Use Cases

**Replacing writers entirely.** AI can draft, but it can't understand your users, your product strategy, or what information is actually needed. It generates plausible text that may be wrong, incomplete, or irrelevant.

**Architecture documentation.** AI doesn't know why your system is designed the way it is. It can format your explanation, but it can't write the explanation.

**Accuracy-critical content.** AI hallucinates. Never publish AI-generated technical claims without verification. Incorrect documentation is worse than no documentation.

**Understanding context.** AI doesn't know that your team decided last week to deprecate the feature it just documented in detail.

## The Human-AI Workflow

The most effective approach:

1. **Human decides what to document.** What's needed, for whom, at what depth.
2. **AI drafts.** Generate a first draft from code, schemas, or outlines.
3. **Human edits.** Fix inaccuracies, add context, adjust tone, cut fluff.
4. **AI checks.** Review for consistency, broken links, style violations.
5. **Human publishes.** Final review, approval, and publication.

AI handles the labor. Humans handle the judgment.

## Using AI to Maintain Docs

**Staleness detection.** "Here's a doc and the current code. What's outdated?" AI can compare docs against code and flag discrepancies.

**Link checking.** Automated, no AI needed -- but AI can suggest replacement links.

**Coverage gaps.** "Here's our API. Here are our docs. What's not documented?" AI can identify missing endpoints, parameters, or error codes.

## Risks

- **Confident incorrectness.** AI writes with authority even when it's wrong. Readers trust confident text.
- **Homogeneous voice.** AI-generated docs sound the same. Your docs lose personality and become generic.
- **Maintenance illusion.** Auto-generating docs from code creates docs that look maintained but may lack the human context that makes docs useful.
- **Over-reliance.** Teams stop thinking about what good documentation looks like because "the AI handles it."

## See Also

- `hub/writing-style-guide.md` -- The human style AI should match
- `research/documentation-debt.md` -- AI as a tool for paying down doc debt
- `research/seedvault-creation-guide.md` -- AI-assisted seed creation
- `hub/code-comments-philosophy.md` -- AI-generated comments are rarely useful
