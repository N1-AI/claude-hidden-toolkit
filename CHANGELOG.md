# Changelog

All notable changes to Claude's Hidden Toolkit are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Editions are tagged by version.

## [1.4] — 2026-03-15

### Added
- **9 new tool cards** (Cards 29–37): `reminder_list_search_v0`, `reminder_create_v0`, `reminder_search_v0`, `reminder_update_v0`, `reminder_delete_v0` (iOS Reminders CRUD suite), `visualize:show_widget`, `gmail_create_draft`, `anthropic_api_in_artifacts`, `persistent_storage`
- **Artifact execution layer section** (Chapter 9) — documents Claude-inside-Claude architecture: `anthropic_api_in_artifacts` (authenticated API calls from artifacts without API key), `persistent_storage` (session-scoped `window.storage`), MCP endpoint access from React artifacts
- **MCP connector state** as an architectural variable (Chapter 2) — connecting/disconnecting Gmail or Google Calendar changes the always-loaded tool pool
- **Project context behavior** section — confirmed Projects do not change tool behavior
- **Ghost tools** section (Chapter 12) — `gmail_send_draft` and `gmail_modify_thread` exist in schema but are not deployed
- **Contributors section** — credited anonymous security researcher (11 findings) and DMontgomery40 (iOS Reminders suite)
- **iOS vs Android differences** — `user_location_v0` returns richer response on iOS (sub_locality, accuracy fields); `user_time_v0` timestamp format differs (no microseconds on iOS)

### Changed (Corrections)
- **`user_time_v0` and `user_location_v0`**: reclassified from deferred to **always-loaded** on mobile — MCP connector state is irrelevant (v1.3 error)
- **`memory_user_edits`**: limit corrected from 200 characters (server-enforced) to **500 characters** (client-side validation). The limit may vary by tier or build
- **`chart_display_v0`**: crash status downgraded from deterministic (100%) to **intermittent/state-dependent**. Tool is always deferred on mobile, not first-class
- **`tool_search` on browser**: corrected from "does not exist" to **conditionally available** when MCP connectors are active, returning tools with `Provider:tool_name` prefix
- **`window.storage`**: reframed from "persistent" to **session-scoped** — data is destroyed when session closes, does not persist across chats even within the same Project
- **`anthropic_api_in_artifacts`**: corrected from Desktop-only to working on **both Desktop and Browser** React artifacts (HTML artifacts blocked by CSP)
- **`web_fetch`**: corrected from 8 to **9 parameters** (added `html_extraction_method`)
- **`bash_tool`**: corrected from 1 to **2 parameters** (added required `description`)
- **`str_replace`**: corrected from 3 to **4 parameters** (added required `description`)
- **`chart_display_v0` values parameter**: accepts integers on iOS (v1.3 documented strings only)
- **Calendar tools**: always-loaded when MCP Calendar connector active (not always deferred)
- **Platform matrix**: updated to reflect iOS as distinct surface with 5 additional Reminders tools
- **`visualize:show_widget` vs `chart_display_v0`**: documented as mutually exclusive by surface — `chart_display_v0` mobile only, `show_widget` desktop/browser only

### Removed
- Deterministic crash claim for `chart_display_v0`
- "tool_search absent on browser" claim
- 200-character server-enforced limit claim for `memory_user_edits`

## [1.3] — 2026-02-16

### Added
- **Platform Architecture section** (new Chapter 2.5) — tool inventory matrix across browser (21 tools), desktop app (22+32 MCP), and mobile app (20+11 deferred)
- **3 new tool cards**: `weather_fetch` (Card 26), `recipe_display_v0` (Card 27), `end_conversation` (Card 28)
- Platform-exclusive tools table showing which tools exist where
- Session instability warning (switching clients changes available tools)
- Fallback behavior documentation for each platform gap
- Settings & Connectors reference integrated into appendix
- 9 new figure placeholders (fig_14 through fig_22)

### Changed
- **Chapter 10 (Discovery Log)**: added mobile-only environment note — the discovery log was produced on mobile and is not reproducible on browser or desktop
- **Chapter 2 (Architecture)**: rewritten to reflect three platform configurations instead of a single binary always-loaded/deferred model
- **Card 5 (message_compose_v1)**: corrected primary button to "Send via Gmail", documented dropdown with "Open in Mail", added no-recipient-field note
- **Card 4 (ask_user_input_v0)**: added "Something else" free-text option, Skip button, keyboard shortcuts, selection counter, drag handles, pagination
- **Cards 17/18 (places)**: added markers mode sidebar details, itinerary mode routing, "Open route" Google Maps behavior, confirmation dialog inconsistency
- **Card 6 (alarm_create_v0)**: confirmed mobile-only, added widget description with Italian locale, removed web/desktop speculation
- **Card 7 (timer_create_v0)**: confirmed mobile-only, added widget description
- **Card 1 (user_time_v0)**: added platform behavior differences (mobile returns exact time, desktop/browser states system date only)
- **Card 3 (user_location_v0)**: confirmed mobile-only, documented two-layer permission system (Claude app + Android OS)
- **Card 10 (chart_display_v0)**: confirmed mobile-only deferred, added app crash warning
- **Card 25 (tool_search)**: major rewrite — browser has no tool_search, desktop finds MCP only, mobile finds consumer tools
- **Cards 8–13 (Calendar suite)**: added mobile-only deferred status, Google Calendar connection requirement
- **Appendix A**: corrected availability matrix to reflect cross-platform testing results
- All response formats upgraded from *(inferred)* to empirically confirmed
- Figure numbering made sequential (fig_01 through fig_22, eliminated fig_14b)

### Removed
- All *(inferred)* response format markers — every schema now confirmed through testing
- Speculative web/desktop availability claims for mobile-only tools
- Screenshot capture instructions from figure placeholders

## [1.2] — 2026-02-13

### Added
- Technical Annex with 25 detailed tool cards
- Appendix C: Tool Template for New Discoveries
- Figure placeholders with capture instructions
- Companion GitHub repository

### Changed
- Expanded Chapter 10 Discovery Log with full query-by-query enumeration
- Added environment note about tool_search behavior in Projects vs standard chats

## [1.1] — 2026-02-10

### Added
- Complete parameter schemas for all 25 tools
- Cross-reference with prior research (Khemani, Willison, Adversa AI, Viticci)
- Discovery methodology (Appendix B)

### Changed
- Restructured from flat tool list to chapter-based organization

## [1.0] — 2026-02-07

### Added
- Initial release documenting 25 internal tools
- Two-tier architecture analysis (always-loaded vs deferred)
- tool_search enumeration methodology
- Availability matrix (Appendix A)
