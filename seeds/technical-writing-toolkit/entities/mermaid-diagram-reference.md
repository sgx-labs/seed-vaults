---
title: "Mermaid Diagram Syntax Reference"
tags: [mermaid, diagrams, flowchart, sequence, reference, entity]
content_type: entity
domain: technical-writing
---

# Mermaid Diagram Syntax Reference

## Flowchart

```mermaid
flowchart LR
    A[Start] --> B{Decision}
    B -->|Yes| C[Action 1]
    B -->|No| D[Action 2]
    C --> E[End]
    D --> E
```

**Direction:** `LR` (left-right), `TD` (top-down), `RL`, `BT`

**Node shapes:**
- `[text]` -- rectangle
- `(text)` -- rounded rectangle
- `{text}` -- diamond (decision)
- `[(text)]` -- cylinder (database)
- `((text))` -- circle
- `>text]` -- flag

**Arrow types:**
- `-->` -- solid arrow
- `-.->` -- dotted arrow
- `==>` -- thick arrow
- `-->|label|` -- labeled arrow

## Sequence Diagram

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    participant D as Database

    C->>S: POST /users
    S->>D: INSERT INTO users
    D-->>S: OK
    S-->>C: 201 Created
```

**Arrow types:**
- `->>` -- solid with arrowhead
- `-->>` -- dashed with arrowhead
- `->>+` -- activate target
- `-->>-` -- deactivate target

**Features:**
- `Note over A,B: text` -- note spanning participants
- `loop Description` ... `end` -- loop block
- `alt Condition` ... `else` ... `end` -- conditional
- `opt Description` ... `end` -- optional block

## Entity Relationship Diagram

```mermaid
erDiagram
    USER ||--o{ ORDER : places
    ORDER ||--|{ LINE_ITEM : contains
    PRODUCT ||--o{ LINE_ITEM : "is in"
```

**Cardinality:**
- `||` -- exactly one
- `o|` -- zero or one
- `}|` -- one or more
- `}o` -- zero or more

## State Diagram

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Review : submit
    Review --> Approved : approve
    Review --> Draft : request_changes
    Approved --> Published : publish
    Published --> [*]
```

## Gantt Chart

```mermaid
gantt
    title Project Timeline
    dateFormat YYYY-MM-DD
    section Phase 1
    Research      :a1, 2025-01-01, 14d
    Design        :a2, after a1, 7d
    section Phase 2
    Development   :b1, after a2, 30d
    Testing       :b2, after b1, 14d
```

## Pie Chart

```mermaid
pie title Traffic Sources
    "Organic" : 45
    "Direct" : 30
    "Referral" : 15
    "Social" : 10
```

## Tips

- Keep diagrams simple. More than 10-12 nodes becomes unreadable.
- Use short labels. Long text in nodes breaks the layout.
- Test in GitHub preview. Rendering varies between platforms.
- Add a text description alongside for accessibility.

## Where Mermaid Renders

- GitHub Markdown (native)
- GitLab Markdown (native)
- Docusaurus (plugin)
- Obsidian (native)
- VS Code preview (with extension)
- Most modern doc tools

## See Also

- `research/diagrams-in-docs.md` -- When to use different diagram tools
- `entities/markdown-syntax-reference.md` -- Embedding Mermaid in Markdown
- `entities/accessibility-in-documentation.md` -- Alt text for diagrams
