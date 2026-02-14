---
title: "DevOps Runbooks — Governance"
content_type: recipe
tags: [governance, rules, runbooks, incident-response, operations]
pinned: true
---

# DevOps Runbooks — Governance

## Purpose

Production-ready runbooks and incident response procedures. When something breaks, surface the relevant runbook immediately. Every step must be actionable.

## Rules

1. **Runbooks must be actionable.** Every step is a concrete command or specific action. No "investigate the issue" without saying HOW to investigate.
2. **During an incident, surface the runbook immediately.** Do not summarize — show the actual steps.
3. **Post-mortems are required for all P0-P2 incidents.** Use `templates/postmortem.md`.
4. **Entity files track current infrastructure state.** Update after changes.
5. **Templates are starting points.** Customize for your stack.
6. **Keep notes focused.** One topic per research note. Under 200 lines.
7. **Search first.** Before answering from scratch, search this vault.
8. **No real credentials or IPs.** All examples use placeholder values.

## Directory Structure

```
devops-runbooks/
├── CLAUDE.md                    # This file
├── bootstrap.md                 # Pinned — emergency lookup table
├── config.toml.example          # SAME configuration template
├── hub/                         # Category index notes
│   ├── incident-response.md
│   ├── runbooks.md
│   ├── monitoring.md
│   ├── deployment.md
│   ├── infrastructure.md
│   └── post-mortems.md
├── research/                    # Detailed guides and runbooks
│   ├── incidents/               # Incident response procedures
│   ├── runbooks/                # Operational runbooks
│   ├── monitoring/              # Monitoring and alerting
│   ├── deployment/              # Deploy and rollback
│   ├── infrastructure/          # Cloud and K8s
│   └── postmortems/             # Post-mortem practices
├── entities/                    # Tool/platform reference
├── decisions/                   # Architectural decisions
├── templates/                   # Report and runbook templates
└── sessions/                    # Session handoffs
```

## Incident Response Protocol

When the agent detects an incident-related query:
1. Search for matching runbook immediately
2. Present the steps in order — do not rewrite or paraphrase
3. Include severity classification from `research/incidents/incident-classification.md`
4. After resolution, remind about post-mortem requirement

## Using SAME Search

```bash
# CLI search
same search "database outage recovery steps"

# MCP search
mcp__same__search_notes(query="rollback deployment", top_k=5)

# Filtered search
mcp__same__search_notes_filtered(query="kubernetes pod crash", domain="operations", top_k=5)
```
