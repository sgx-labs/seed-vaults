---
title: "Diagrams in Documentation"
tags: [diagrams, mermaid, d2, ascii, visualization, architecture, documentation]
content_type: research
domain: technical-writing
---

# Diagrams in Documentation

## When to Use Diagrams

A diagram is worth using when:
- You're showing relationships between components (architecture, data flow)
- A table or list would be confusing (state machines, decision trees)
- Spatial relationships matter (network topology, deployment layout)

A diagram is NOT worth using when:
- Text would be clearer (simple sequential steps)
- The diagram would have fewer than 3 elements
- The content changes frequently (diagrams are harder to update than text)

## Mermaid

Text-based diagrams that render in GitHub, GitLab, Docusaurus, and most doc tools. The best default choice for docs-as-code.

```
flowchart LR
    Client --> API[API Server]
    API --> DB[(Database)]
    API --> Cache[(Redis)]
    API --> Queue[Message Queue]
    Queue --> Worker
```

**Best for:** Flowcharts, sequence diagrams, ER diagrams, state machines, Gantt charts.

**Tradeoffs:**
- Renders natively in GitHub Markdown -- no build step needed
- Limited layout control -- you can't pixel-position elements
- Complex diagrams get unreadable in source form
- See `entities/mermaid-diagram-reference.md` for syntax

## D2

A newer diagramming language with better aesthetics and layout control than Mermaid.

```
client -> api: HTTP requests
api -> db: queries
api -> cache: read-through
api -> queue: async jobs
queue -> worker: consume
```

**Best for:** Architecture diagrams, system design docs where aesthetics matter.

**Tradeoffs:**
- Better visual output than Mermaid
- Requires a build step (not rendered natively in GitHub)
- Smaller ecosystem, fewer integrations
- Good CLI tooling for CI pipelines

## ASCII Diagrams

Plain text diagrams that work everywhere with zero dependencies.

```
┌─────────┐     ┌───────────┐     ┌──────────┐
│  Client  │────>│ API Server│────>│ Database │
└─────────┘     └───────────┘     └──────────┘
                      │
                      v
                ┌───────────┐
                │   Cache   │
                └───────────┘
```

**Best for:** Quick diagrams in code comments, terminal output, environments without rendering.

**Tradeoffs:**
- Works in any monospace context (terminal, code comments, plain text)
- Painful to edit by hand (use a tool like asciiflow.com or monodraw)
- No interactivity, no styling
- Fragile -- one character off and the alignment breaks

## Decision Guide

| Situation | Use |
|-----------|-----|
| README on GitHub | Mermaid |
| Architecture doc in repo | Mermaid or D2 |
| Code comment | ASCII |
| Presentation or blog | Image export from D2 or Excalidraw |
| API sequence diagram | Mermaid sequence diagram |
| Quick sketch for a PR | ASCII or Mermaid |

## Best Practices

- **Keep diagrams simple.** If it has more than 10-12 elements, split into multiple diagrams.
- **Label everything.** Arrows without labels are ambiguous.
- **Use consistent notation.** Pick one style (boxes for services, cylinders for databases) and stick with it.
- **Store source, not images.** Text-based diagrams can be diffed, reviewed, and updated. Image files can't.
- **Add alt text.** For accessibility, describe what the diagram shows in a paragraph before or after it.

## Common Mistakes

- **Diagrams that replace text.** A diagram should complement text, not replace it. Some readers can't see images.
- **Undescribed arrows.** An arrow from A to B doesn't say whether it's HTTP, gRPC, a queue message, or a database query.
- **Stale diagrams.** A diagram of the old architecture is worse than no diagram. Date your diagrams.

## See Also

- `entities/mermaid-diagram-reference.md` -- Mermaid syntax quick reference
- `hub/api-documentation.md` -- Diagrams in API docs
- `hub/architecture-decision-records.md` -- Diagrams in ADRs
- `research/documentation-as-code.md` -- Diagrams as code
- `entities/accessibility-in-documentation.md` -- Alt text for diagrams
