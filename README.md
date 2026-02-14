# Seed Vaults

Pre-built knowledge bases for [SAME](https://github.com/sgx-labs/same) — the persistent memory layer for AI coding agents.

Each seed is a ready-to-use vault that gives your AI agent instant expertise in a specific domain. Clone a seed, water it, and your agent has context from day one.

## Available Seeds

| Seed | Type | Notes | Description |
|------|------|-------|-------------|
| [Claude Code Power User](./claude-code-power-user/) | Knowledge | 50+ | Master hooks, MCP servers, workflows, and prompt patterns |
| [AI Agent Architecture](./ai-agent-architecture/) | Knowledge | 60-80 | Patterns for building production AI agents |
| [Security Audit Framework](./security-audit-framework/) | Knowledge | 50-70 | OWASP Top 10, auth patterns, secure coding |
| [DevOps Runbooks](./devops-runbooks/) | Hybrid | 50-70 | Incident response, monitoring, deployment |
| [Indie Hacker Playbook](./indie-hacker-playbook/) | Hybrid | 50-70 | Idea validation to first revenue |
| [Open Source Launch Kit](./open-source-launch-kit/) | Hybrid | 40-60 | Launch, grow, and maintain OSS projects |
| [Freelancer Business Kit](./freelancer-business-kit/) | Framework | 40-60 | Proposals, contracts, invoicing, clients |
| [Personal Productivity OS](./personal-productivity-os/) | Framework | 30-50 | Your personal operating system |
| [Trading Seed](./TradingSeed/) | Knowledge | 140+ | Algorithmic trading strategies and analysis |

All seeds are **free and open source**.

## Water the Seed

Clone a seed. Water it. Your agent grows expertise instantly.

```bash
# 1. Install SAME
npm install -g @sgx-labs/same

# 2. Pick a seed and copy it
git clone https://github.com/sgx-labs/seed-vaults
cp -r seed-vaults/claude-code-power-user ~/my-vault

# 3. Water the seed
cd ~/my-vault
same init          # Configure your embedding provider
same reindex       # Index all notes (~30 seconds)

# 4. Verify it works
same search "how do hooks work"

# Done. Your agent now has expert knowledge in every session.
```

### What Happens When You Water

| Seed Type | What happens |
|-----------|-------------|
| **Knowledge** | Ready immediately. 50+ expert notes indexed and searchable. |
| **Hybrid** | Starter content indexed. Templates ready for you to customize. |
| **Framework** | Scaffolding indexed. Your agent interviews you on first session to personalize. |

## Seed Types

- **Knowledge** — Pre-filled with curated research and reference material. Water and use immediately.
- **Hybrid** — Starter content plus templates you customize for your situation. Water, then adapt.
- **Framework** — Scaffolding you fill in. Water, then let your agent interview you to personalize.

## Building Your Own Seed

Use the [`_seed-template/`](./_seed-template/) as a starting point:

1. Copy the template directory
2. Fill in `SEED-SPEC.md` with your domain
3. Create hub notes (category indexes)
4. Add research notes (one question per file)
5. Define entities (things that change over time)
6. Set up `CLAUDE.md` governance rules
7. Water it: `same init && same reindex`

See the [SEED-SPEC template](./_seed-template/SEED-SPEC.md) for the full structure.

## Structure

Every seed follows the same layout:

```
seed-name/
  bootstrap.md       # Pinned — "Water the Seed" instructions + orientation
  SEED-SPEC.md        # Seed definition and metadata
  CLAUDE.md            # Agent governance rules
  config.toml.example  # SAME configuration template
  hub/                 # Category index notes
  research/            # Deep-dive reference notes
  entities/            # Living documents (tools, platforms, standards)
  decisions/           # Architectural decisions log
  templates/           # Fill-in templates (hybrid/framework seeds)
  sessions/            # Session handoffs
  _Raw/                # Source material (skipped by indexer)
```

## Made with SAME, Used with SAME

These seeds are built using SAME's agent workflows and designed to be used with SAME. They demonstrate the full lifecycle: persistent memory, session continuity, federated search across vaults, and knowledge that compounds over time.

Plant a seed. Water it. Watch it grow.
