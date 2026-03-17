# AGENTS.md — Context for AI Coding Agents

## What This Repo Is

A technical book ("Claude's Hidden Toolkit") documenting 37 undocumented internal tools in Claude.ai's consumer interfaces. Fully reverse-engineered through systematic testing across browser, desktop app, Android, and iOS.

## File Roles

| File | Role | Editable? |
|------|------|-----------|
| `Claude_Hidden_Toolkit.md` | Source of truth | Yes — all content changes here |
| `README.md` | GitHub landing page | Yes |
| `LICENSE` | CC BY 4.0 | No |

## Domain Knowledge

- **37 tools** documented with confirmed JSON schemas
- **4 platforms** with different tool inventories:
  - Browser: 21 always-loaded, `tool_search` conditionally available with MCP connectors
  - Desktop: 22 always-loaded + 32 MCP tools via `tool_search`
  - Android: 22 always-loaded + 11 deferred consumer tools
  - iOS: 22 always-loaded + 16 deferred consumer tools (Android set + 5 Reminders)
- **MCP connector state** affects tool inventory — connecting Gmail/Calendar adds tools to always-loaded pool
- **Tool cards** in the Technical Annex follow a strict template (see Appendix C in the book)
- **Figures** are numbered sequentially — all currently marked [Upcoming] pending screenshot capture
- **Response formats** are all empirically confirmed — no inferred markers remain
- **Two external contributors** credited: an anonymous security researcher (11 findings) and DMontgomery40 (iOS Reminders suite)

## Conventions

- Tool names in backticks: `web_search`, `event_create_v0`
- Platform tags on every claim: "Mobile App only", "Browser + Desktop only", "iOS only", "All"
- Version suffixes: `_v0` (initial/experimental), `_v1` (breaking upgrade)
- MCP connector tools use `Provider:tool_name` prefix on browser (e.g., "Google Calendar:gcal_list_calendars")
- Confirmed vs unverified clearly marked — never speculate
- Anonymous contributor referenced only as "An independent security researcher who prefers to remain anonymous" — never use real name

## Architecture Context

The book documents a multi-tier tool system:
- **Always-loaded tools**: injected into every conversation context (~21-22 depending on platform)
- **Deferred tools**: discovered at runtime via `tool_search` (mobile only, 11-16 consumer tools depending on OS)
- **MCP tools**: discovered via `tool_search` on desktop (32 integration tools) or conditionally on browser
- **MCP connector tools**: appear in always-loaded pool when connectors (Gmail, Calendar) are active
- **Artifact execution layer**: `anthropic_api_in_artifacts` (Claude-in-Claude), `persistent_storage` (session-scoped), MCP endpoint access from React artifacts — desktop/browser only, not mobile

The calendar system has a dual-layer architecture where Projects use MCP Connector tools with email-based IDs, while standard mobile chats use deferred consumer tools with numeric string IDs. The two layers are incompatible.

`visualize:show_widget` and `chart_display_v0` are mutually exclusive by surface: `chart_display_v0` is mobile only (deferred, intermittent instability), `show_widget` is desktop/browser only.

## Quality Gates

Before any content change:
1. Has the claim been tested on the relevant platform(s)?
2. Is the platform availability clearly stated?
3. Does the tool card follow the template structure?
4. Are figure numbers sequential with no gaps?
5. Is the response format marked as confirmed or unverified?
6. For contributor content: has it been independently verified before documenting as confirmed?
