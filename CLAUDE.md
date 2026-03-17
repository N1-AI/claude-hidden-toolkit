# CLAUDE.md — Instructions for Claude Code

## Project Overview

This is "Claude's Hidden Toolkit" — a living technical book documenting Claude.ai's undocumented internal tools. The book reverse-engineers 37 tools across four platforms (browser, desktop app, Android, iOS) with confirmed schemas, response formats, and behavioral patterns. Edition 1.4 includes contributions from two external researchers.

## Repository Structure

```
Claude_Hidden_Toolkit.md   # The book (source of truth — all edits here)
README.md                  # GitHub landing page
CLAUDE.md                  # You are here
AGENTS.md                  # General AI agent context
CHANGELOG.md               # Edition history with detailed changes
CITATION.cff               # Academic citation metadata
CONTRIBUTING.md            # Contribution guidelines
SECURITY.md                # Responsible disclosure policy
LICENSE                    # CC BY 4.0
```

## Key Rules

1. **The .md file is the source of truth.** All content edits happen here.
2. **All tool claims must be empirically verified.** No speculative schemas. If you haven't tested it, mark it as unverified.
3. **Platform matters.** Every tool claim must specify which platform(s) it applies to: browser, desktop app, Android, iOS, or all. iOS and Android are distinct — iOS has 5 additional Reminders tools.
4. **MCP connector state matters.** Some tools are only available when specific connectors (Gmail, Calendar) are active. State this explicitly.
5. **Figure numbering is sequential.** If adding figures, continue the sequence. Never use letter suffixes (e.g., fig_14b).
6. **Tool cards follow a strict template.** See Appendix C in the book for the template. Every card needs: quick facts table, overview, parameters table, example call with JSON, "When Claude Uses It" table, "Watch Out" section, and platform availability table.
7. **Contributor privacy.** The anonymous security researcher must only be referenced as "An independent security researcher who prefers to remain anonymous." Never use their real name anywhere.

## Writing Style

- Technical but accessible — assume the reader is a developer or power user
- No hedging without evidence — say "confirmed" or "not tested", never "probably"
- Use backtick formatting for all tool names, parameter names, and code
- Tables over bullet lists for structured comparisons
- Blockquotes (>) for figure placeholders and warnings

## Tool Naming Conventions

- Always-loaded tools: `web_search`, `memory_user_edits`, etc.
- Deferred tools (mobile only): `alarm_create_v0`, `chart_display_v0`, etc.
- Always-loaded on mobile (not deferred): `user_time_v0`, `user_location_v0`
- MCP connector tools: `gmail_create_draft`, `gcal_list_calendars`, etc.
- Artifact-layer tools: `anthropic_api_in_artifacts`, `persistent_storage`
- Visualization: `visualize:show_widget` (colon-namespaced, desktop/browser only)
- iOS-only tools: `reminder_create_v0`, `reminder_search_v0`, etc.
- Version suffixes: `_v0` = initial/experimental, `_v1` = breaking change upgrade
- Unversioned tools are considered stable
- Ghost tools (schema-present, not deployed): `gmail_send_draft`, `gmail_modify_thread`

## Architecture Quick Reference

- `chart_display_v0` (mobile only) and `visualize:show_widget` (desktop/browser only) are mutually exclusive by surface
- `window.storage` / `persistent_storage` is session-scoped only — "persistent" naming is misleading
- `anthropic_api_in_artifacts` works in React artifacts (desktop + browser), blocked in HTML artifacts by CSP
- MCP connector state changes the always-loaded tool pool — this is a v1.4 finding
- Project context does not change tool behavior

## What Not To Do

- Don't add tools without cross-platform verification
- Don't change the figure numbering scheme without renumbering all references
- Don't remove the [Upcoming] tags on figures — they indicate screenshots pending capture
- Don't merge the .md living repository links with personal GitHub links — they serve different purposes
- Don't use the anonymous contributor's real name — ever, anywhere
- Don't document ghost tools as functional — they exist in schema only
