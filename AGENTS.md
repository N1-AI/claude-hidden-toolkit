# AGENTS.md — Context for AI Coding Agents

## What This Repo Is

A technical book ("Claude's Hidden Toolkit") documenting 28 undocumented internal tools in Claude.ai's consumer interfaces. Fully reverse-engineered through systematic testing across browser, desktop app, and mobile app.

## File Roles

| File | Role | Editable? |
|------|------|-----------|
| `Claude_Hidden_Toolkit.md` | Source of truth | Yes — all content changes here |
| `README.md` | GitHub landing page | Yes |
| `LICENSE` | CC BY 4.0 | No |

## Domain Knowledge

- **28 tools** documented with confirmed JSON schemas
- **3 platforms** with different tool inventories (browser: 21 tools, desktop: 22+32 MCP, mobile: 20+11 deferred)
- **Tool cards** in the Technical Annex follow a strict template (see Appendix C in the book)
- **Figures** are numbered sequentially fig_01 through fig_22 — all currently marked [Upcoming] pending screenshot capture
- **Response formats** are all empirically confirmed — no inferred markers remain

## Conventions

- Tool names in backticks: `web_search`, `event_create_v0`
- Platform tags on every claim: "Mobile App only", "Browser + Desktop only", "All"
- Version suffixes: `_v0` (initial/experimental), `_v1` (breaking upgrade)
- Confirmed vs unverified clearly marked — never speculate

## Architecture Context

The book documents a two-tier tool loading system:
- **Always-loaded tools**: injected into every conversation context (~17-22 depending on platform)
- **Deferred tools**: discovered at runtime via `tool_search` (mobile only, 11 consumer tools)
- **MCP tools**: discovered via `tool_search` on desktop only (32 integration tools)

The calendar system has a dual-layer architecture where Projects use MCP Connector tools with email-based IDs, while standard mobile chats use deferred consumer tools with numeric string IDs. The two layers are incompatible.

## Quality Gates

Before any content change:
1. Has the claim been tested on the relevant platform(s)?
2. Is the platform availability clearly stated?
3. Does the tool card follow the template structure?
4. Are figure numbers sequential with no gaps?
5. Is the response format marked as confirmed or unverified?
