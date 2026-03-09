# Seed Vaults

Pre-built knowledge bases for [SAME](https://github.com/sgx-labs/statelessagent). Install one command. Your AI agent gets instant domain expertise.

**17 seeds. 880+ curated notes. All local, all private, no cloud.**

## Quick Start

```bash
# Install SAME if you haven't already
curl -fsSL https://statelessagent.com/install.sh | bash

# Install a seed vault (one command)
same seed install claude-code-power-user

# Your agent now has expert knowledge
same search "how do I write a pre-commit hook"
```

## Available Seeds

### Featured

| Seed | Notes | Size | Description |
|------|------:|-----:|-------------|
| **[Claude Code Power User](./claude-code-power-user/)** | 50 | 384 KB | Master hooks, MCP servers, workflows, and prompt patterns |
| **[AI Agent Architecture](./ai-agent-architecture/)** | 56 | 276 KB | Production patterns for building AI agents — memory, tool use, orchestration |
| **[API Design Patterns](./seeds/api-design-patterns/)** | 56 | 364 KB | Comprehensive reference for REST, GraphQL, gRPC, auth, rate limiting, and API best practices |
| **[Personal Productivity OS](./personal-productivity-os/)** | 117 | 680 KB | Full productivity system with ADHD support, habit tracking, recipes, gamification, and self-growth frameworks |

### All Seeds

| Seed | Notes | Size | Description |
|------|------:|-----:|-------------|
| [Security Audit Framework](./security-audit-framework/) | 61 | 320 KB | OWASP-based security assessment methodology |
| [DevOps Runbooks](./devops-runbooks/) | 55 | 316 KB | Infrastructure playbooks and incident response |
| [Indie Hacker Playbook](./indie-hacker-playbook/) | 52 | 220 KB | From idea validation to first revenue |
| [Open Source Launch Kit](./open-source-launch-kit/) | 54 | 228 KB | Ship, promote, and maintain open source projects |
| [Freelancer Business Kit](./freelancer-business-kit/) | 54 | 332 KB | Business templates for freelance developers |
| [Home Chef Essentials](./home-chef-essentials/) | 56 | 468 KB | Evidence-based cooking techniques, meal prep, and nutrition |
| [Fitness & Wellness](./fitness-and-wellness/) | 48 | 420 KB | Strength training, cardio, mobility, recovery, and habit building |
| [Resume & Interview Prep](./resume-interview-prep/) | 37 | — | Resume tailoring, interview prep, salary negotiation, and networking |
| [Devcontainer Quickstart](./devcontainer-quickstart/) | 12 | — | Set up secure, reproducible dev environments with VS Code devcontainers *(WIP)* |
| [SAME Getting Started](./seeds/same-getting-started/) | 18 | 104 KB | Complete on-ramp to SAME from zero to a working vault with MCP integration |
| [Technical Writing Toolkit](./seeds/technical-writing-toolkit/) | 42 | 244 KB | Documentation, technical writing, and creating knowledge that compounds |
| [Engineering Management Playbook](./seeds/engineering-management-playbook/) | 52 | 352 KB | 1-on-1s, hiring, performance reviews, team health, and engineering leadership |
| [TypeScript Fullstack Patterns](./seeds/typescript-fullstack-patterns/) | 50 | 380 KB | Production patterns for Next.js, React Server Components, Prisma/Drizzle, and modern tooling |

> Personal Productivity OS and Devcontainer Quickstart require SAME v0.8.0+. All other seeds require v0.7.0+.

## What Are Seeds?

A seed is a curated vault of markdown notes designed around a specific domain. Each note is written for retrieval — concise, tagged, and structured so your AI agent can find exactly what it needs during a coding session.

Seeds are not documentation. They are **working memory** — the kind of knowledge an experienced teammate carries in their head. Install a seed and your agent stops guessing and starts knowing.

## How It Works

When you run `same seed install`, SAME:

1. Downloads the seed from this repo
2. Extracts files into `~/same-seeds/<name>/`
3. Indexes all notes into a local SQLite database
4. Registers it as a SAME vault

From that point on, every AI session backed by SAME can search the seed's knowledge. Notes surface automatically through hooks, or on demand through `same search` and `same ask`.

Seeds also ship with a `CLAUDE.md` that teaches your agent how to use the domain — what to search for, when to save decisions, and how to hand off between sessions.

## Share Your Seeds

**Built a vault that helps you? Share it with the community.**

SeedVaults grows when people contribute what they know. Any topic. Any size. If it helps you, it'll help someone else. A 10-note seed on a niche framework you love is just as valuable as a 100-note production playbook.

### How to contribute a seed

1. **Fork** this repo
2. **Copy** [`_seed-template/`](./_seed-template/) to a new directory
3. **Write** your notes — follow the structure in the template
4. **Test** locally: `same init && same reindex && same search "your query"`
5. **Submit** a pull request

For a detailed walkthrough, see the [Building a SAME SeedVault](./seeds/technical-writing-toolkit/research/seedvault-creation-guide.md) guide.

### Ideas for seeds people are looking for

- **Language patterns** — Python idioms, Rust ownership, Go concurrency, TypeScript patterns
- **Framework playbooks** — Django, Rails, Laravel, Flutter, SwiftUI, Next.js
- **Industry knowledge** — Healthcare compliance, fintech regulations, e-commerce operations
- **Hobby expertise** — Woodworking, photography, music production, gardening, cooking
- **Career skills** — Interview prep, salary negotiation, public speaking, leadership

See [SEED-IDEAS.md](./SEED-IDEAS.md) for the full list of ideas.

All community seeds are licensed under [CC BY-SA 4.0](./LICENSE). See [CONTRIBUTING.md](./CONTRIBUTING.md) for full guidelines and quality standards.

## Contributing

We welcome contributions! See [CONTRIBUTING.md](./CONTRIBUTING.md) for guidelines on improving existing seeds and proposing new ones.

## Building Your Own Seed

The [`_seed-template/`](./_seed-template/) directory contains everything you need to create a new seed:

1. Copy `_seed-template/` to a new directory
2. Fill in `SEED-SPEC.md` with your domain, audience, and key questions
3. Write hub notes (category indexes), research notes (one concept per file), and entity notes (living reference docs)
4. Add a `CLAUDE.md` with governance rules and SAME tool usage patterns
5. Test it: `same init && same reindex && same search "your test query"`

See [`_seed-template/SEED-SPEC.md`](./_seed-template/SEED-SPEC.md) for the full spec and quality checklist.

## Seed Structure

Every seed follows a consistent layout:

```
seed-name/
  bootstrap.md          # Pinned — onboarding instructions and orientation
  SEED-SPEC.md          # Seed definition and metadata
  CLAUDE.md             # Agent governance rules and SAME tool patterns
  config.toml.example   # SAME configuration template
  hub/                  # Category index notes (5-7 per seed)
  research/             # Deep-dive reference notes (one concept per file)
  entities/             # Living documents — tools, platforms, standards
  decisions/            # Architectural decision log
  templates/            # Fill-in templates (some seeds)
  sessions/             # Session handoff notes (generated per-user)
  _Raw/                 # Source material (excluded from indexing)
```

<details>
<summary><strong>Manual Install (without <code>same seed</code>)</strong></summary>

If you prefer to install manually or are running an older version of SAME:

```bash
# Clone the repo
git clone https://github.com/sgx-labs/seed-vaults
cd seed-vaults

# Copy the seed you want to a new location
cp -r claude-code-power-user ~/vaults/claude-code

# Initialize and index
cd ~/vaults/claude-code
same init
same reindex
```

</details>

---

All seeds are free and open source under [CC BY-SA 4.0](./LICENSE). See [DISCLAIMER.md](./DISCLAIMER.md) for content notices.

*Plant a seed. Water it. Watch it grow.*
