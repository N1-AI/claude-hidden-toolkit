# Hidden / Unlisted Tools

Tools found in the Claude.app binary that are not listed in the deferred tools system-reminder. These tools are gated by platform, feature flags, or session type. Many are visible to users in the UI but are not exposed to the AI assistant via `tool_search` or MCP tool registration.

**Discovery method:** Extracted `app.asar`, searched `index.js` for tool registration patterns not in the deferred tools list.

**Last updated:** 2026-03-19

**Verified across:** Dispatch, Cowork task, code task, Claude Code CLI, Claude Desktop main chat, claude.ai web chat

---

## Verification Matrix

| Category | Count | Desktop chat | Web chat | Dispatch | Cowork | Code task | CLI |
|----------|-------|--------------|----------|----------|--------|-----------|-----|
| Widget Tools | 2 | CONFIRMED | CONFIRMED | NOT FOUND | NOT FOUND | NOT FOUND | NOT FOUND |
| Preview Tools | 13 | — | — | — | — | CONFIRMED | — |
| Computer Use | 1 (13 actions) | — | — | via Chrome MCP | — | — | — |
| Terminal/Roots | 2 | NOT FOUND | NOT FOUND | NOT FOUND | NOT FOUND | NOT FOUND | NOT FOUND |
| Office Add-in | 5 | untested | — | — | — | — | — |
| Conversation | 1 | internal only | internal | internal | internal | internal | internal |

**Total: 24 tools.** 14 verified working, 5 untested (require Office), 5 not callable in any tested context.

---

## Tool Categories

1. **[Widget Tools](widget-tools.md)** (2) — Inline SVG/HTML rendering.
   CONFIRMED in Claude Desktop main chat and claude.ai web chat.
   Not exposed via `tool_search` in any agentic context (Cowork, Dispatch, code task, CLI).
   Injected by `@ant/imagine-server` at the Electron renderer layer.

2. **[Preview Tools](preview-tools.md)** (13) — Dev server and browser preview.
   CONFIRMED in code tasks only, under `mcp__Claude_Preview__*` namespace.

3. **[Computer Use Tools](computer-use-tools.md)** (1, 13 actions) — Desktop automation.
   Verified via Chrome MCP (`mcp__Claude_in_Chrome__computer`) in Dispatch sessions.

4. **[Terminal & Roots Tools](terminal-and-roots.md)** (2) — Terminal reader and MCP roots inspector.
   NOT FOUND in any tested context. Likely IDE-plugin-only (VS Code extension, JetBrains plugin).

5. **[Office Add-in Tools](office-addin-tools.md)** (5) — Excel/Word/PowerPoint integration.
   UNTESTED — requires Microsoft Office with the Claude add-in connected.

6. **[Conversation Tools](conversation-tools.md)** (1) — Context compaction.
   Internal only. Not user-callable. Correct behavior.

7. **[Feature Flags & Codenames](feature-flags-and-codenames.md)** — Feature flags, internal codenames, Swift APIs, and config keys found in the binary.

---

## Screenshots

See `screenshots/` directory:

| File | Contents |
|------|---------|
| widget-demo-desktop-live.png | Live Claude Desktop — widget tools active (2026-03-19) |
| widget-screenshot.png | First widget render from Cowork session |
| screenshot-1773960*.jpg | Session tool-call verification captures |
