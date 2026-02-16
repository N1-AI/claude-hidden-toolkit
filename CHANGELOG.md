# Changelog

All notable changes to Claude's Hidden Toolkit are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Editions are tagged by version.

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
