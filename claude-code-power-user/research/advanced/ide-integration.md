---
title: "Claude Code in VS Code, Cursor, and Other Editors"
tags: [ide, vscode, cursor, windsurf, integration, mcp, advanced, mcp-only, extension]
content_type: research
domain: engineering
---

# Claude Code in VS Code, Cursor, and Other Editors

## The Question

When should you use Claude Code in the terminal versus inside an IDE, and how do you get the best of both worlds?

## The Landscape

Claude Code works in three environments:

| Environment | How It Works |
|-------------|-------------|
| **Terminal (CLI)** | Full-featured agent with hooks, MCP, file editing, bash access |
| **VS Code Extension** | Inline suggestions, chat panel, terminal integration |
| **Cursor / Windsurf** | Built-in AI with Claude as the backend model |

Each has trade-offs. The CLI is the most capable. IDE integrations add visual context but may lack features like hooks and MCP.

## VS Code with Claude Code

### The Extension

The official Claude Code VS Code extension brings the agent into your editor. Install it from the VS Code marketplace or via the CLI:

```bash
claude mcp install vscode
```

### What You Get

- **Chat panel**: Conversation with Claude that can see your open files
- **Terminal integration**: Run Claude Code commands in the integrated terminal
- **File context**: Claude can reference files you have open in the editor
- **Inline diffs**: See proposed changes as diffs before accepting

### Terminal Inside VS Code

The simplest approach: use Claude Code in the VS Code integrated terminal. You get the full CLI experience plus the ability to see file changes reflected immediately in the editor tabs.

```
# In VS Code terminal — works exactly like standalone CLI
claude "Add input validation to the handler in the open file"
```

### When VS Code Works Best

- Inline edits where you want to see the diff before accepting
- Quick single-file changes
- When you want visual context (file tree, open tabs, git gutter)
- Teaching scenarios where seeing the editor state helps understanding

## Cursor

Cursor has Claude built in as a model option. It is not the same as Claude Code CLI -- it is Cursor's own agent framework using Claude as the LLM.

### What Is Different

| Feature | Claude Code CLI | Cursor with Claude |
|---------|----------------|-------------------|
| Hooks (SessionStart, Stop, etc.) | Yes | No |
| MCP tools | Yes | Cursor has its own MCP support |
| Multi-file editing | Yes, via Edit tool | Yes, via Composer |
| Bash access | Full | Sandboxed |
| Context window management | Manual + hooks | Cursor manages automatically |
| CLAUDE.md | Yes | Cursor uses `.cursorrules` |

### Composer Mode

Cursor's Composer is its multi-file editing interface. Select Claude as the model, describe your change, and Composer applies it across files. Similar to Claude Code's multi-file editing but with a visual diff interface.

```
# In Cursor Composer:
"Add error handling to all API endpoints in src/routes/"
```

Composer shows a side-by-side diff for each file. You accept or reject per file.

### When Cursor Works Best

- Visual diff review for each file change
- Inline code completion with Claude
- Projects that already use Cursor's workflow
- Teams standardized on Cursor

## Windsurf

Windsurf uses "Cascade" -- its agent mode that chains edits across files. It supports Claude as a backend model.

### Cascade Edits

Windsurf's differentiator is cascading: make a change in one file, and the agent propagates related changes through dependent files automatically.

```
# In Windsurf Cascade:
"Rename the getUserById function to fetchUserById"
→ Windsurf updates the function, all callers, all tests
```

### When Windsurf Works Best

- Cascading rename and refactor operations
- Teams that prefer a visual agent workflow
- Projects where seeing the cascade helps verify correctness

## CLI vs IDE: Decision Framework

### Use the CLI When

- **Multi-file complex work**: The CLI handles 20+ file edits more reliably than IDE agents
- **Hook-driven workflows**: SessionStart context loading, Stop handoff notes, PreToolUse guardrails
- **MCP tool access**: Searching knowledge bases, cross-project context, custom tools
- **Automation**: Scripted workflows, CI integration, batch operations
- **Long sessions**: CLI manages context more transparently; you control when to compact
- **SAME integration**: Hooks and MCP tools are CLI-native features

### Use the IDE When

- **Inline suggestions**: Real-time autocomplete as you type
- **Visual diffs**: See proposed changes before accepting
- **Single-file edits**: Quick fixes, adding a function, updating a string
- **Learning a codebase**: Visual navigation + AI explanations
- **Pair programming feel**: Side-by-side chat while you code

### Use Both

The most productive setup uses both:

```
Terminal (Claude Code CLI):
  - Complex refactors
  - Architecture changes
  - Session continuity with SAME
  - Running tests and builds

IDE (VS Code / Cursor / Windsurf):
  - Quick inline fixes
  - Code review with visual diffs
  - Autocomplete while typing
  - Exploring unfamiliar code visually
```

## MCP-Only Setup for IDE Workflows

If your IDE does not support Claude Code hooks but does support MCP, you can still get SAME's knowledge base features:

```bash
same init --mcp-only
```

This configures SAME as an MCP server without hooks. Your IDE agent can call SAME's search, context, and decision-logging tools directly.

### What --mcp-only Gives You

- `search_notes` -- search the knowledge base
- `get_session_context` -- load orientation context
- `save_decision` -- log decisions for future sessions
- `create_handoff` -- write session handoff notes
- All other SAME MCP tools

### What You Lose Without Hooks

- No automatic context loading at session start
- No automatic handoff on session end
- No PreToolUse guardrails
- No crash recovery

The trade-off: you must manually ask for context instead of having it injected automatically.

## Configuration Tips

### VS Code Settings for Claude Code

Add to your VS Code `settings.json` to optimize the integrated terminal for Claude Code:

```json
{
  "terminal.integrated.scrollback": 10000,
  "terminal.integrated.fontSize": 13
}
```

### Cursor Rules That Complement CLAUDE.md

If you use both Cursor and Claude Code CLI, keep project instructions in `CLAUDE.md` (the CLI reads it) and add a `.cursorrules` file that references the same conventions:

```
# .cursorrules
Follow the coding conventions documented in CLAUDE.md.
Use the patterns established in existing code.
Run tests after making changes.
```

### Shared MCP Config

Both CLI and IDE can share the same MCP server configuration. Configure once, use in both environments:

```json
{
  "mcpServers": {
    "same": {
      "command": "same",
      "args": ["mcp"]
    }
  }
}
```

## Summary

The CLI is the power tool. IDE integration is the convenience layer. Use the CLI for heavy lifting and session continuity. Use the IDE for the visual feedback loop. The `--mcp-only` flag bridges the gap when your IDE does not support hooks.
