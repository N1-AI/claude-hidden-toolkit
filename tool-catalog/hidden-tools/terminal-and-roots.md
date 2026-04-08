# Terminal & Roots Tools

**Status:** NOT FOUND in any tested session type. `tool_search` returns nothing for "read_terminal", "get_roots", "roots", or "terminal" in Dispatch, Cowork tasks, code tasks, or Claude Code CLI. These are likely injected only in IDE integration contexts (VS Code extension, JetBrains plugin).

**Total:** 2 tools

**Verified:** 2026-03-19. Tested in Dispatch orchestrator, Cowork task session, code task session, and Claude Code CLI — not found in any.

---

## read_terminal

Read the contents of the user's integrated terminal panel.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| lines | number | no | How many trailing lines to return. Default 200, max 1000. |
| wait_for_output_ms | number | no | Block up to this many ms for new output to arrive before reading. |

**Returns:** The last ~200 lines with ANSI codes stripped.

**When to use (from tool description):** "Use when the user references test output, errors, or 'this' in a way that implies terminal content."

**`wait_for_output_ms` behavior:** Blocks up to the specified duration for new output to arrive before reading. Designed for test watchers (Jest, Vitest, etc.) — edit a file, then `read_terminal` with a wait to get the watcher's response. If no new output arrives, returns the current buffer with a note.

**Notes:**
- ANSI codes are stripped from output
- Only reads the integrated terminal panel — not arbitrary shell sessions
- Max 1000 lines; for longer outputs you only get the tail

---

## get_roots

Returns the file roots advertised to MCP servers in this session.

No parameters.

**Returns:** List of root URIs with optional names:
```
Available file roots (N):
• file:///path/to/root (name)
• file:///path/to/another
```

**Notes:**
- Returns "No roots available. No folders have been selected for this session." if no folders are mounted
- Diagnostic/introspection tool — useful for debugging MCP server access issues
