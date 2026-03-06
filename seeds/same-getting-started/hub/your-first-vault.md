---
title: "Your First Vault"
tags: [tutorial, first-vault, quickstart, walkthrough, beginner]
content_type: hub
domain: same-getting-started
---

# Your First Vault

This is a hands-on walkthrough. By the end, you'll have a working vault with notes you can search.

## Step 1: Install SAME

If you haven't already:

```bash
curl -fsSL https://statelessagent.com/install.sh | bash
```

Or via npm:

```bash
npm install -g @sgx-labs/same
```

Verify it worked:

```bash
same --version
```

## Step 2: Initialize Your Vault

Navigate to your project folder (or any folder where you want to keep notes):

```bash
cd ~/my-project
same init
```

`same init` will ask you a few questions:
- Where to store notes (default: current directory)
- Which embedding provider to use (Ollama, OpenAI, or none)
- Whether to install hooks for your AI tool

If you don't have Ollama, choose "none" -- SAME works fine with keyword search. You can add embeddings later.

## Step 3: Create Some Notes

Create a few markdown files. They can be about anything:

```bash
mkdir -p hub decisions
```

Create `hub/project-overview.md`:

```markdown
---
title: "Project Overview"
tags: [overview, architecture]
content_type: hub
pinned: true
---

# Project Overview

This is a web application built with Go and PostgreSQL.
The API serves a React frontend. We deploy to Fly.io.

Key decisions:
- JWT for authentication
- PostgreSQL for data storage
- Redis for session caching
```

Create `decisions/auth-strategy.md`:

```markdown
---
title: "Authentication Strategy"
tags: [auth, security, jwt, decisions]
content_type: decision
---

# Authentication Strategy

## Decision
Use JWT with RS256 signing and refresh tokens.

## Rationale
- Stateless -- no session store needed
- RS256 allows public key verification
- Refresh tokens handle token expiry gracefully

## Alternatives Considered
- Session cookies: Requires sticky sessions or shared store
- OAuth only: Too complex for our current needs
```

## Step 4: Index Your Notes

```bash
same reindex
```

You'll see output like:

```
Indexing 2 notes...
  hub/project-overview.md (indexed)
  decisions/auth-strategy.md (indexed)
Done. 2 notes indexed.
```

## Step 5: Search

```bash
same search "what authentication method are we using?"
```

Output:

```
1. decisions/auth-strategy.md (score: 0.92)
   "Use JWT with RS256 signing and refresh tokens..."

2. hub/project-overview.md (score: 0.78)
   "JWT for authentication..."
```

Try `same ask` for a conversational answer:

```bash
same ask "what did we decide about auth?"
```

## Step 6: Check Status

```bash
same status
```

This shows you what SAME is tracking -- indexed notes, config, hooks status.

## Step 7: Run the Demo (Optional)

If you want to see SAME with more realistic data:

```bash
same demo
```

This creates a temporary vault with sample notes and walks you through search, ask, and other features.

## What's Next?

You have a working vault. From here:

- **Connect to Claude Code** -- `guides/connect-to-claude-code.md`
- **Install a SeedVault** -- `same seed install claude-code-power-user` for instant knowledge
- **Set up your daily workflow** -- `guides/daily-workflow.md`
- **Understand session memory** -- `hub/session-memory.md`

The more notes you add, the smarter your AI gets. Every decision logged, every handoff created, every research note saved -- it all compounds.
