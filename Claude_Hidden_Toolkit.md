# Claude's Hidden Toolkit

**The Definitive Guide to Claude.ai's Undocumented Internal Tools**

*37 tools. Zero official documentation. Fully reverse-engineered.*

**Francesco Marinoni Moretto** — AI Practice & Community Lead, N1AI

Edition 1.4 — March 2026 · Based on systematic research with Claude Opus 4.6

---

© 2026 Francesco Marinoni Moretto. Published by N1AI. Licensed under CC BY 4.0.
You are free to share and adapt this work with attribution.

This book documents tools observed in Claude.ai as of March 2026. Anthropic may change, rename, or remove these tools at any time. This work is independent and not affiliated with Anthropic.

Living repository: [github.com/n1-ai/claude-hidden-toolkit](https://github.com/n1-ai/claude-hidden-toolkit)

Author: [linkedin.com/in/francesco-moretto](https://linkedin.com/in/francesco-moretto)

---


## About the Author

Francesco Marinoni Moretto is AI Practice & Community Lead at N1AI, where he bridges the gap between AI capabilities and practical business adoption. An 18-year community builder turned AI systems architect, he brings a pattern of discovering powerful tools and building adoption communities across European markets — from Italy's first CiviCRM evangelist in 2008 to the European AI developer circuit today. His published work includes Hardstop (the first fail-closed safety plugin for Claude agentic tools), Clarity Gate (epistemic verification for RAG systems), Stream Coding (a documentation-first development methodology), and the definitive guide to Claude's `memory_user_edits` system. This book grew from a simple observation: Claude.ai ships with 37+ internal tools, and almost none of them are documented. Full bio at the end of this book.

[linkedin.com/in/francesco-moretto](https://linkedin.com/in/francesco-moretto) · [github.com/frmoretto](https://github.com/frmoretto)

## Contributors

An independent security researcher who prefers to remain anonymous contributed 11 findings for v1.4, including the discovery of `anthropic_api_in_artifacts`, `persistent_storage`, MCP endpoints in artifacts, skill filesystem topology, and egress proxy JWT architecture.

DMontgomery40 (GitHub) independently discovered and documented the complete iOS Reminders CRUD suite (5 tools), enabling the first complete documentation of this iOS-exclusive capability.

## About N1AI

N1AI is a community-driven AI consultancy that puts human needs first. They help businesses discover, plan, and implement AI solutions — from initial discovery through strategy, execution, and team training. Their community connects business owners, developers, and AI enthusiasts who are making AI work in the real world. N1AI partners with Anthropic, OpenAI, and Google, and leads with H.E.A.R.T.: Human first, Entrepreneurship, Action, Results, and Transparency.

[n1ai.co](https://www.n1ai.co) · [Community](https://www.n1ai.co/community/)

---

## Contents

| Chapter | Title | Topics |
| --- | --- | --- |
| [1](#chapter-1--why-this-book-exists) | Why This Book Exists | The documentation gap · Who this is for · How to use this book |
| [2](#chapter-2--the-architecture-of-claudeais-tool-system) | The Architecture of Claude.ai's Tool System | Always-loaded vs. deferred tools · Platform architecture · The discovery mechanism · Versioning |
| [3](#chapter-3--context-tools-time--location) | Context Tools: Time & Location | `user_time_v0` · `user_location_v0` · How Claude knows where and when you are |
| [4](#chapter-4--the-interaction-layer-widgets--message-drafting) | The Interaction Layer: Widgets & Message Drafting | `ask_user_input_v0` · `message_compose_v1` · Beyond plain text |
| [5](#chapter-5--visualization-inline-charts) | Visualization: Inline Charts | `chart_display_v0` · When to use charts vs. artifacts · Limitations |
| [6](#chapter-6--calendar--device-integration) | Calendar & Device Integration | 6 calendar tools · `alarm_create_v0` · `timer_create_v0` · Mobile-native features |
| [7](#chapter-7--search--data-tools) | Search & Data Tools | `web_search` · `web_fetch` · `image_search` · `places_search` · `places_map_display_v0` · `fetch_sports_data` · Google Drive tools · `weather_fetch` · `recipe_display_v0` |
| [8](#chapter-8--the-memory-stack-how-claude-remembers-you) | The Memory Stack: How Claude Remembers You | `memory_user_edits` · `conversation_search` · `recent_chats` · The three-layer model · Memory settings |
| [9](#chapter-9--computer-use-files-code--artifacts) | Computer Use: Files, Code & Artifacts | `bash_tool` · `view` · `create_file` · `str_replace` · `present_files` · The skill system · Execution settings |
| [10](#chapter-10--the-meta-tool-tool_search) | The Meta-Tool: tool_search | How to discover tools yourself · Enumeration methodology · Platform-dependent behavior · What might be hiding |
| [11](#chapter-11--the-complete-tool-reference) | The Complete Tool Reference | Every tool · Every parameter · Copy-paste ready |
| [12](#chapter-12--what-comes-next) | What Comes Next | The MCP convergence · Building on top of hidden tools |
| [A](#appendix-a--availability-matrix) | Availability Matrix | |
| [B](#appendix-b--discovery-methodology) | Discovery Methodology | |
| [C](#appendix-c--tool-template-for-new-discoveries) | Tool Template for New Discoveries | |
| [D](#appendix-d--settings--connectors-reference) | Settings & Connectors Reference | |
| | [Technical Annex](#technical-annex--detailed-tool-cards) | 37 detailed tool cards with JSON examples |

### Figures

| Figure | Description | Location |
| --- | --- | --- |
| `fig_01` | `tool_search` discovery flow | [Chapter 2](#chapter-2--the-architecture-of-claudeais-tool-system) |
| `fig_02` | `user_time_v0` response in chat | [Chapter 3](#chapter-3--context-tools-time--location) |
| `fig_03` | `user_time_v0` expanded tool call (mobile) | [Chapter 3](#chapter-3--context-tools-time--location) |
| `fig_04` | Browser geolocation permission prompt | [Chapter 3](#chapter-3--context-tools-time--location) |
| `fig_05` | Location widget + two permission layers (mobile) | [Chapter 3](#chapter-3--context-tools-time--location) |
| `fig_06` | `ask_user_input_v0` three widget types (single_select, multi_select, rank_priorities) | [Chapter 4](#chapter-4--the-interaction-layer-widgets--message-drafting) |
| `fig_07` | `message_compose_v1` email card with Gmail dropdown | [Chapter 4](#chapter-4--the-interaction-layer-widgets--message-drafting) |
| `fig_08` | Inline chart from `chart_display_v0` | [Chapter 5](#chapter-5--visualization-inline-charts) |
| `fig_09` | Calendar event creation confirmation | [Chapter 6](#chapter-6--calendar--device-integration) |
| `fig_10` | Alarm widget (mobile, Italian locale) | [Chapter 6](#chapter-6--calendar--device-integration) |
| `fig_11` | Timer widget (mobile) | [Chapter 6](#chapter-6--calendar--device-integration) |
| `fig_12` | Weather widget (browser + desktop comparison) | [Chapter 7](#chapter-7--search--data-tools) |
| `fig_13` | Recipe widget — browser + desktop comparison | [Chapter 7](#chapter-7--search--data-tools) |
| `fig_14` | Recipe cooking mode — step-by-step + timer states | [Chapter 7](#chapter-7--search--data-tools) |
| `fig_15` | Places markers mode — overview + detail popup | [Chapter 7](#chapter-7--search--data-tools) |
| `fig_16` | Places itinerary mode with walking route | [Chapter 7](#chapter-7--search--data-tools) |
| `fig_17` | Sports standings from `fetch_sports_data` | [Chapter 7](#chapter-7--search--data-tools) |
| `fig_18` | `memory_user_edits` view output | [Chapter 8](#chapter-8--the-memory-stack-how-claude-remembers-you) |
| `fig_19` | File creation and `present_files` download card | [Chapter 9](#chapter-9--computer-use-files-code--artifacts) |
| `fig_20` | Raw `tool_search` results | [Chapter 10](#chapter-10--the-meta-tool-tool_search) |
| `fig_21` | Settings → Connectors page | [Appendix D](#appendix-d--settings--connectors-reference) |
| `fig_22` | Settings → Capabilities (Artifacts + Code + Memory + Skills) | [Appendix D](#appendix-d--settings--connectors-reference) |

---

## Chapter 1 — Why This Book Exists

Claude.ai is not just a chatbot. Beneath the conversational interface lies an orchestration engine with 37 distinct tools — capabilities that let Claude check the time, search your calendar, render interactive charts, draft emails with native app integration, find nearby restaurants, display them on a map, show weather forecasts, walk you through recipes step by step, and even set alarms on your phone.

> *Almost none of this is documented.*

Anthropic's official documentation covers the API: how developers can build with Claude using tools they define themselves. But the consumer product — claude.ai — ships with a rich, evolving toolkit that exists in a documentation void. Support articles describe what users can do in consumer-friendly language ("set alarms," "find nearby places") without ever revealing the internal tool names, parameter schemas, response formats, or behavioral patterns.

This book fills that void. It is the result of systematic reverse-engineering: probing Claude's tool discovery mechanism, capturing every discoverable tool, testing their parameters and responses across three platforms (browser, desktop app, mobile app), and documenting the results in a format that is both readable and technically precise.

### The Documentation Gap

A comprehensive search across GitHub, Reddit, LinkedIn, Twitter/X, YouTube, and the broader web reveals that this territory is almost entirely unexplored. Of the 37 tools documented in this book, only two — `conversation_search` and `recent_chats` — have been previously reverse-engineered with full schemas published (by Shlok Khemani, September 2025). One additional tool, `message_compose_v1`, was named in an Adversa AI security research paper. The remaining tools have zero public documentation by their internal names.


The following table shows the documentation status of every tool at the time of writing:

| Tool | Public Documentation Status |
| --- | --- |
| `conversation_search` | Fully documented (Khemani, Sept 2025) |
| `recent_chats` | Fully documented (Khemani, Sept 2025) |
| `message_compose_v1` | Named once (Adversa AI security paper) |
| `user_time_v0` | Zero public results by internal name |
| `user_location_v0` | Zero public results by internal name |
| `chart_display_v0` | Zero public results by internal name |
| `ask_user_input_v0` | Zero public results by internal name |
| `weather_fetch` | Zero public results by internal name |
| `recipe_display_v0` | Zero public results by internal name |
| `end_conversation` | Zero public results by internal name |
| `alarm_create_v0` | Feature documented by Anthropic, internal name unknown publicly |
| `timer_create_v0` | Feature documented by Anthropic, internal name unknown publicly |
| Calendar suite (6 tools) | Feature documented by Anthropic, internal names unknown publicly |
| `places_search` | Zero public results by internal name |
| `places_map_display_v0` | Zero public results by internal name |
| `fetch_sports_data` | Zero public results by internal name |
| `google_drive_search` / `google_drive_fetch` | Zero public results by internal name |
| `tool_search` | Zero public results by internal name |

Credit to the prior researchers: Shlok Khemani (memory tools), Simon Willison (system prompt analysis), Adversa AI (security research), Federico Viticci / MacStories (iOS tools and `_v0` naming convention discovery).


### Who This Book Is For

This book serves three audiences. **Power users** will learn exactly what to say to trigger hidden capabilities — from inline charts to interactive choice widgets — and understand when Claude uses these tools automatically. **Developers** building on the Anthropic API will see what the consumer product does that the API doesn't, giving them a blueprint for what to build with MCP or custom tools. **AI researchers** will find a documented case study of how a production AI system's tool architecture works — the design tradeoffs, the deferred loading strategy, the platform-specific behaviors, and the behavioral patterns.

### How to Use This Book

Chapters 1–2 provide context and architecture. Chapters 3–9 are deep dives into each tool category, organized by function. Chapter 10 covers the meta-tool that makes discovery possible. Chapter 11 is a complete reference — every tool, every parameter — designed for quick lookup. Chapter 12 looks ahead. The Technical Annex provides a detailed card for every tool with JSON call examples.

This book has a companion GitHub repository at [github.com/n1-ai/claude-hidden-toolkit](https://github.com/n1-ai/claude-hidden-toolkit) that is updated as tools change. Think of this book as Edition 1.4 — a snapshot of the tool ecosystem as of March 2026 — and the repository as the living document.

**A note on accuracy:** Parameter schemas for deferred tools were captured directly from `tool_search` results and are exact. Always-loaded tool schemas were extracted from Claude's system-level tool definitions. All response formats have been empirically confirmed through cross-platform testing — no *(inferred)* markers remain in this edition. All claims have been tested through both automated and manual verification across three platforms (browser, desktop app, mobile app) — see Appendix B for the full methodology.

**What changed in v1.3:** Edition 1.3 added cross-platform testing results that fundamentally changed the architecture picture. Three new tools (`weather_fetch`, `recipe_display_v0`, `end_conversation`) were documented, 15 existing tool cards were updated with platform-specific corrections, and a new Settings & Connectors reference was added as Appendix D.

**What changed in v1.4:** This edition adds 9 new tools (37 total), corrects 13 factual errors from v1.3, and introduces two external contributors. Key corrections: `user_time_v0` and `user_location_v0` are **always-loaded** on mobile (not deferred as v1.3 stated); `memory_user_edits` accepts 500 characters (not 200); `chart_display_v0` crashes are intermittent (not deterministic); `window.storage` is **session-scoped during development** but **persistent for published artifacts** (Anthropic confirmed April 2026); `anthropic_api_in_artifacts` works on Browser (not Desktop-only); `tool_search` exists on Browser with MCP connectors active. New tool cards include: 5 iOS Reminders tools, `visualize:show_widget`, `gmail_create_draft`, `anthropic_api_in_artifacts`, and `persistent_storage`. New sections cover MCP connector state as an architectural variable, Project context behavior, and the artifact execution layer.

---

## Chapter 2 — The Architecture of Claude.ai's Tool System

Understanding how Claude.ai's tools work requires understanding two fundamental architectural distinctions: not all tools are created equal, and not all tools are available on all platforms.

### Two Classes of Tools

Claude.ai's tools fall into two categories: **always-loaded** tools that are available from the start of every conversation, and **deferred** tools that are discovered and loaded on demand.

Always-loaded tools include the workhorses: `web_search`, `web_fetch`, `image_search`, `places_search`, `places_map_display_v0`, `fetch_sports_data`, `google_drive_search`, `google_drive_fetch`, `memory_user_edits`, `conversation_search`, `recent_chats`, `ask_user_input_v0`, `message_compose_v1`, `weather_fetch`, `recipe_display_v0`, `end_conversation`, the computer use tools, and — on mobile — `user_time_v0` and `user_location_v0`. These are injected into every conversation's context from the start.

Deferred tools include: `chart_display_v0`, `alarm_create_v0`, `timer_create_v0`, the calendar suite, and on iOS, the 5 Reminders tools. These are not loaded until Claude determines they're needed — either by recognizing a relevant user request or by explicitly searching for them using the meta-tool `tool_search`.

> **v1.4 correction:** v1.3 incorrectly documented `user_time_v0` and `user_location_v0` as deferred tools. They are **always-loaded** on mobile regardless of MCP connector state.

#### Why Deferred Loading?

Context window efficiency. Every tool definition consumes tokens — roughly 200–800 tokens depending on parameter complexity. By deferring tools that are only needed in specific scenarios (alarm setting, calendar management, chart rendering), Claude.ai saves thousands of tokens per conversation, keeping its baseline context lean. The trade-off is a slight latency on first use — the tool must be discovered before it can be called. Once loaded, deferred tools remain available for the rest of the conversation.


### Platform Architecture: Three Surfaces, Three Tool Sets

This is the single most important finding from cross-platform testing: **Claude.ai is not one product with one tool set — it is three distinct surfaces with different tool inventories.** The browser (claude.ai), the desktop app, and the mobile app each present a different configuration of tools to the model.

#### Platform Inventory

| Platform | Always-Loaded Tools | `tool_search` Present? | Deferred Tools Available |
|----------|---------------------|----------------------|-------------------------|
| Browser (claude.ai) | 21 | Conditional (MCP tools only, when connectors active) | None (consumer) |
| Desktop App | 22 | Yes (MCP tools only) | 32 MCP tools (e.g., Chrome + Filesystem) |
| Mobile App (Android) | 22 (incl. `user_time_v0`, `user_location_v0`) | Yes (consumer tools) | 9 consumer deferred tools |
| Mobile App (iOS) | 22 (incl. `user_time_v0`, `user_location_v0`) | Yes (consumer tools) | 14 consumer deferred tools (incl. 5 Reminders) |

The implications are significant. On the **browser**, `tool_search` is conditionally available — it appears only when MCP connectors (Google Calendar, Gmail) are active, and returns only MCP tools with a `Provider:tool_name` namespace prefix (e.g., "Google Calendar:gcal_list_calendars"). Consumer deferred tools are never available on browser. On the **desktop app**, `tool_search` exists but only discovers MCP integration tools. On the **mobile app**, `tool_search` discovers the consumer deferred tools documented in this book, and `user_time_v0`/`user_location_v0` are always-loaded (bypassing `tool_search` entirely).

#### Platform-Exclusive Tools

Some tools exist on only a subset of platforms:

| Tool | Browser | Desktop App | Mobile App |
|------|---------|-------------|------------|
| `weather_fetch` | ✅ | ✅ | ❌ |
| `recipe_display_v0` | ✅ | ✅ | ❌ |
| `visualize:show_widget` | ✅ | ✅ | ❌ |
| `tool_search` | ✅ (MCP only, conditional) | ✅ (MCP only) | ✅ (consumer) |
| `user_time_v0` / `user_location_v0` | ❌ | ❌ | ✅ (always-loaded) |
| Consumer deferred tools | ❌ | ❌ | ✅ (9 Android / 14 iOS) |
| iOS Reminders suite (5 tools) | ❌ | ❌ | ✅ (iOS only) |

This means, for example, that `alarm_create_v0`, `timer_create_v0`, `chart_display_v0`, and the calendar suite are only available as deferred tools on the mobile app. `user_time_v0` and `user_location_v0` are always-loaded on mobile (not deferred). On browser and desktop, these capabilities either don't exist or Claude falls back to alternative approaches.

### MCP Connector State as an Architectural Variable

A finding new in v1.4: MCP connector state (whether Google Calendar, Gmail, etc. are connected in Settings → Connectors) affects the tool inventory differently depending on the tool type:

- **Native consumer tools** (`alarm_create_v0`, `timer_create_v0`, `chart_display_v0`, calendar suite): Always in the deferred pool on mobile **regardless** of MCP connector state. MCP is irrelevant.
- **Context tools** (`user_time_v0`, `user_location_v0`): Always-loaded on mobile **regardless** of MCP connector state.
- **MCP Connector tools** (`gcal_list_calendars`, `gmail_create_draft`, etc.): Appear in the always-loaded pool **only** when their respective connectors are active. When connectors are disconnected, these tools disappear entirely.
- **`tool_search` on browser**: Only appears when MCP connectors are active. Without connectors, `tool_search` does not exist on browser.

This means Android distinguishes between native deferred tools (always available in the deferred pool) and MCP deferred tools (conditionally loaded based on connector state).

### Project Context Does Not Change Tool Behavior

Testing in v1.4 confirmed that Claude Projects do not affect tool behavior:

- `tool_search` returns the identical tool pool inside and outside a Project
- `window.storage` behavior is identical inside a Project (persistence is publish-gated, not project-scoped)
- `anthropic_api_in_artifacts` works identically inside a Project
- `chart_display_v0` behavior is identical inside a Project

The only tool behavior that changes in Projects is the MCP Connector layer (Layer 1 calendar tools appear in Projects with Google Calendar connected — see Chapter 6).

#### Session Instability Warning

Switching between clients mid-conversation changes your available tools. The tool set is **client-bound, not conversation-bound**. If you start a conversation on mobile (where `alarm_create_v0` is available) and continue it on the browser (where it isn't), the alarm tool disappears. Claude does not warn you about this — it simply loses access to tools that were available moments earlier.

#### Fallback Behavior

When you request a capability that doesn't exist on your current platform, Claude falls back gracefully rather than failing:

- **Weather** (mobile): uses `web_search` instead of `weather_fetch` — returns text-only weather data, no interactive widget
- **Charts** (browser/desktop): creates a React artifact with Recharts instead of `chart_display_v0` — opens in the artifact sidebar rather than inline. On desktop and browser, `visualize:show_widget` provides an alternative inline chart option using Chart.js with broader chart type support (pie, donut, histogram, heatmap, bubble). On mobile, `chart_display_v0` exists but has intermittent instability (see Chapter 5)
- **Recipes** (mobile): returns plain text recipe in conversation — no interactive widget, no cooking mode
- **Alarms/timers** (browser/desktop): suggests using Google timer, a voice assistant, or offers to create an artifact-based timer
- **Location** (browser/desktop): `user_location_v0` is absent; Claude states it cannot access local time/location and asks manually

### The Versioning Convention

Internal tools follow a `tool_name_v0` / `v1` naming convention. The `v0` suffix indicates an initial or experimental release. When a tool evolves with breaking changes, the version increments. The clearest example is the calendar tool: `event_create_v0` creates a single event, while `event_create_v1` accepts a batch array. Both coexist, suggesting Anthropic maintains backward compatibility rather than deprecating immediately.

Not all tools carry version suffixes. `web_search`, `web_fetch`, and `fetch_sports_data` are unversioned, which may indicate they are considered stable or that they predate the versioning convention.

### The Discovery Mechanism

The most architecturally interesting tool is `tool_search` — a meta-tool whose sole purpose is discovering other tools. It accepts a keyword query and returns matching tool names with their parameter schemas. This is how Claude.ai implements deferred loading: when a user asks "What time is it?", Claude can search for time-related tools, discover `user_time_v0`, load it, and call it — all within a single response.

**Critical platform caveat:** As documented above, `tool_search` behaves fundamentally differently across platforms. The discovery methodology in Chapter 10 was conducted on the mobile app and is only reproducible there. On the browser, `tool_search` only appears when MCP connectors are active and returns only MCP tools. On the desktop app, it returns MCP integration tools instead of consumer deferred tools.

Chapter 10 covers `tool_search` in detail, including the keyword sweeps used to enumerate the full toolkit for this book.

> **📸 IMAGE: `fig_01_tool_search_flow.png`**
> Screenshot showing Claude calling `tool_search` in real time.
> *[Upcoming]*

### Platform Availability

A critical finding: these tools exist only within Claude.ai's consumer interfaces (web, mobile app, desktop app). They are **not** available through the Anthropic API, Claude Code, or any third-party integration. Developers using the API must implement equivalent functionality themselves. The full availability matrix is in Appendix A.

---

## Chapter 3 — Context Tools: Time & Location

Two tools give Claude awareness of the user's physical context: where they are and when they are. These are foundational — other tools depend on them for timezone handling and location-biased searches.

### `user_time_v0`

The simplest tool in the entire toolkit. Zero parameters, one response field. Call it and get the current time in the user's timezone.

**Platform:** Mobile App only (**always-loaded** — v1.3 incorrectly documented as deferred). On browser and desktop, this tool is absent — Claude states the system date from its context but acknowledges it cannot access local time.

**Parameters:** None

**Response:**

```json
// Android format (microseconds, colon in offset)
{"current_time": "2026-03-15T10:49:50.411154+01:00"}

// iOS format (no microseconds, compact offset)
{"current_time": "2026-03-15T12:11:22+0100"}
```

The response is ISO 8601 with the user's UTC offset. **iOS vs Android difference:** Android includes microsecond precision and uses a colon in the UTC offset (`+01:00`); iOS omits microseconds and uses a compact offset (`+0100`). The timezone is detected from the user's device settings — not from IP geolocation. This means VPN users still get their device's configured timezone, and manual timezone overrides are respected.

Claude uses this tool proactively when time context improves a response: scheduling questions, timezone conversions, day-of-week awareness, and timestamping activities. It also serves as a prerequisite for calendar operations, where ISO 8601 timestamps with correct offsets are required.

> **📸 IMAGE: `fig_02_user_time_response.png`**
> Screenshot of Claude responding with the current time.
> *[Upcoming]*

> **📸 IMAGE: `fig_03_user_time_expanded.png`**
> Screenshot of the expanded `user_time_v0` tool call on mobile, showing the raw tool invocation and response.
> *[Upcoming]*

### `user_location_v0`

The only tool that requires explicit user permission. When called, the device displays a geolocation prompt. If granted, it returns coordinates; if denied, Claude falls back gracefully.

**Platform:** Mobile App only (**always-loaded** — v1.3 incorrectly documented as deferred). On browser and desktop, this tool is absent — Claude cannot determine user location and will ask manually.

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `accuracy` | string | Yes | `"low"` (city-level) or `"high"` (GPS-level) |

This tool is most commonly used as a precursor to `places_search` — get the user's location, then search for nearby businesses using the coordinates as a bias. Claude appears cautious about calling it proactively; it is typically triggered by location-implying language ("near me," "nearby," "around here"). **Implicit triggers are unreliable** — "Where am I?" may not be sufficient; explicit invocation is more reliable.

**Response** *(confirmed)*:

```json
// Android response
{
  "status": "success",
  "latitude": 45.4707004,
  "longitude": 9.1248245,
  "geocoded": {
    "address": "Via Example 15",
    "administrative_area": "Lombardia",
    "country": "Italia",
    "locality": "Milano",
    "name": "15",
    "postal_code": "20100"
  }
}

// iOS response (richer — adds sub_locality and accuracy fields)
{
  "status": "success",
  "latitude": 45.4707004,
  "longitude": 9.1248245,
  "accuracy": 65.0,
  "geocoded": {
    "address": "Via Example 15",
    "administrative_area": "Lombardia",
    "country": "Italia",
    "locality": "Milano",
    "sub_locality": "Municipio 1",
    "name": "15",
    "postal_code": "20100"
  }
}
```

Key findings from testing: the `accuracy` parameter ("high" vs "low") produced **no meaningful difference** in the response — coordinates were nearly identical in both cases. The `geocoded` object provides a full reverse-geocoded address that the coordinates alone don't convey. **iOS vs Android difference:** iOS adds a `sub_locality` field and an `accuracy` field (in meters) to the response that Android lacks.

#### Two-Layer Permissions (Mobile)

On mobile, location access involves two permission layers:

1. **Claude app permission:** A native prompt asking "Allow once / Allow always / Cancel" (localized — e.g., "Consenti una volta / Consenti sempre / Annulla" on Italian devices)
2. **Android OS permission:** System-level prompt for Exact vs. Approximate location, with usage scope selection

Both must be granted for the tool to return precise coordinates. The result widget displays a pin icon, full street address, and a Google Maps thumbnail.

> **📸 IMAGE: `fig_04_location_permission.png`**
> Screenshot showing the browser's geolocation permission prompt triggered by Claude.
> *[Upcoming]*

> **📸 IMAGE: `fig_05_location_mobile.png`**
> Screenshot showing the mobile location widget with pin icon and street address, plus both permission layers (Claude app + Android OS).
> *[Upcoming]*

### Context Tools: Side by Side

| | `user_time_v0` | `user_location_v0` |
| --- | --- | --- |
| Permission required | No | Yes (device prompt — two layers on Android) |
| Parameters | 0 | 1 (`accuracy` — but no observable effect in testing) |
| Platform | Mobile only (always-loaded) | Mobile only (always-loaded) |
| Response | ISO 8601 timestamp (format differs iOS vs Android) | Coordinates + reverse-geocoded address (iOS richer than Android) |
| Platform variance | Mobile: exact time; Browser/Desktop: system date only | Mobile: GPS + geocoded; Browser/Desktop: absent |
| Feeds into | Calendar, alarm, timer tools | `places_search`, weather, distance queries |

---

## Chapter 4 — The Interaction Layer: Widgets & Message Drafting

Two tools transform Claude from a text-in, text-out system into something with actual UI capabilities. These are arguably the most novel tools in the toolkit — they render interactive elements that users can click, tap, and manipulate.

### `ask_user_input_v0`: Interactive Choice Widgets

Instead of asking questions in plain text, Claude can present structured choices as clickable buttons, multi-select chips, or drag-and-drop ranking interfaces. The widget renders at the bottom of the chat, below Claude's text response.

Three question types are supported: `single_select` (pick one), `multi_select` (pick one or more), and `rank_priorities` (drag to reorder). Each call supports 1–3 questions with 2–4 options each.

The design implications are significant. A consulting intake that would take 5 back-and-forth messages can be compressed into a single interaction:

```json
{
  "questions": [
    {"question": "What's your main goal?", "type": "single_select",
     "options": ["Increase revenue", "Cut costs", "Enter new market", "Fix operations"]},
    {"question": "Timeline?", "type": "single_select",
     "options": ["This quarter", "This year", "2+ years"]}
  ]
}
```

Two questions, six options, one interaction. No back-and-forth needed. Preference gathering that requires nuanced ranking becomes a drag-and-drop exercise with `rank_priorities`. The tool respects graceful degradation — if the user ignores the widget and types freely, Claude handles it normally.

#### Widget UI Details (from cross-platform testing)

Several undocumented UI features were discovered through testing:

- **"Something else" option:** Automatically added to ALL widget types as a free-text alternative — not in the schema, not controllable by the model
- **"Skip" button:** Present on all widget types, allowing users to bypass the question
- **Keyboard shortcuts:** Displayed at the bottom of the widget — ↑↓ to navigate, Enter to select, ⌘Enter to submit, Esc to skip
- **Selection counter:** On `multi_select`, shows "0 selected" and updates as the user clicks options
- **Drag handles:** On `rank_priorities`, shows ⠿ dot handles with "Drag to re-order" instruction
- **Localization:** Widget labels adapt to the device language — e.g., `rank_priorities` output shows "Classificato:" on Italian devices
- **Pagination:** When multiple questions are used, shows "N of 3" with < > navigation arrows
- **Memory integration:** Claude personalizes option text using stored memory about the user

> **📸 IMAGE: `fig_06_ask_user_input_widget.png`**
> Screenshot showing all three widget types: single_select buttons, multi_select chips with selection counter, and rank_priorities with drag handles.
> *[Upcoming]*

### `message_compose_v1`: Strategic Message Drafting

This tool drafts messages (emails, texts, Slack messages) and presents them in a card UI with native app integration buttons.

The most powerful feature is **strategic variants**. For high-stakes communications, Claude generates 2–3 fundamentally different approaches — not just tonal variations, but different strategies that lead to different outcomes. "Polite decline" vs. "Suggest alternative" vs. "Delegate" each prioritize something different. The user sees labeled tabs and chooses the approach that fits their situation.

Three message types are supported: `email` (with subject line and mail integration), `textMessage` ("Open in Messages" button), and `other` ("Copy" button for Slack, LinkedIn, etc.).

#### UI Corrections (v1.3)

Cross-platform testing revealed several differences from the v1.2 description:

- **Primary button:** The main button is **"Send via Gmail"** (with a Gmail icon), not "Open in Mail" as previously documented
- **Dropdown:** A chevron reveals a dropdown with two options: "Send via Gmail" and "Open in Mail" — the user can choose their mail client
- **No recipient field:** The schema has no `to` parameter — recipients must be added manually after the message opens in the mail client
- **Subject + body transfer:** Both fields transfer correctly to Gmail and Outlook
- **Auto-signing:** Claude signs the message with the user's name from memory
- **Copy icon:** Present on all message cards for quick clipboard access

> **📸 IMAGE: `fig_07_message_compose_card.png`**
> Screenshot showing the message card UI with the "Send via Gmail" primary button, dropdown chevron revealing mail client options, subject line, body text, and variant tabs.
> *[Upcoming]*

---

## Chapter 5 — Visualization: Inline Charts

`chart_display_v0` is designed to render interactive charts directly inline in the chat — no artifacts, no code execution, no external libraries. In theory, it is the fastest path from data to visualization in the entire Claude ecosystem. In practice, it is the clearest example of a tool that shipped before it was ready.

**Platform:** Mobile App only (deferred via `tool_search`). On browser and desktop, Claude falls back to creating a React artifact with Recharts — the chart opens in the artifact sidebar rather than appearing inline in the conversation.

> **⚠️ Mobile instability warning (updated March 2026):** `chart_display_v0` has **intermittent instability** on mobile — it sometimes crashes the app and sometimes renders successfully. v1.3 reported 100% crash rate, but further testing in v1.4 showed the crash is state-dependent rather than deterministic. The tool is always deferred on both Android and iOS, and MCP connector state is irrelevant to its behavior.

For readers: **use this tool with caution**. If you need reliable charts in Claude, use a React artifact (works on all platforms), code execution with matplotlib (browser and desktop), or `visualize:show_widget` (browser and desktop — see below). The inline chart tool is documented here for completeness and because it reveals how Anthropic's tool pipeline works — tools can be discoverable and callable before they are stable.

The chart appears directly in the conversation stream, below Claude's text. No artifact sidebar opens. The chart is interactive — hover over any data point to see its exact value, click a legend entry to hide or show a series, and the chart resizes responsively to the chat width.

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `series` | array | Yes | One or more data series to plot |
| `style` | string | Yes | `'line'`, `'bar'`, `'scatter'` |
| `title` | string | No | Chart title |
| `x_axis` | object | No | X-axis configuration |
| `y_axis` | object | No | Y-axis configuration |

The tool supports at minimum line charts, bar charts, and scatter plots. Charts appear to be interactive: hover tooltips showing exact values, legend toggling, and responsive sizing within the chat interface.

> **📸 IMAGE: `fig_08_inline_chart.png`**
> Screenshot of an inline interactive chart rendered by `chart_display_v0`.
> *[Upcoming]*

#### When to Use `chart_display_v0` vs. Alternatives

| Method | Setup | Inline? | Interactive? | Customizable? | Platform |
| --- | --- | --- | --- | --- | --- |
| `chart_display_v0` | None | Yes | Yes | Limited | Mobile only |
| React artifact (Recharts) | Code gen | No (artifact) | Yes | High | All |
| Code execution (matplotlib) | Python | No (image) | No | High | Browser + Desktop |
| HTML artifact (Chart.js) | Code gen | No (artifact) | Yes | High | All |

Use `chart_display_v0` for quick visualization during conversation on mobile. Use artifacts for polished, highly customized visualizations. Use code execution when you need programmatic control or data processing. Key limitation: no SVG/PNG export — if the user needs to save the chart, use a code artifact instead.

#### Non-Functional and Functional Parameters (v1.4)

Testing revealed that several `chart_display_v0` parameters are non-functional:
- **Non-functional:** `format`, `min`, `max` — these are accepted by the schema but have no observable effect on the rendered chart
- **Functional:** `color` (hex values work — e.g., `"#FF5733"`), `scale: "log"` (logarithmic scale works correctly)
- **`values` type:** iOS accepts integers in the `values` array, not just strings as v1.3 documented. The schema may be more permissive than originally documented.

The tool description includes the instruction "ALWAYS use this tool after health queries," suggesting it was originally designed for health data visualization.

### `visualize:show_widget`: The Desktop/Browser Inline Alternative

`visualize:show_widget` is a separate rendering system that provides inline visualization on desktop and browser — the surfaces where `chart_display_v0` is absent. It is **architecturally distinct** from the tool/deferred layer: it does not appear in `tool_search` results and operates outside the standard tool pipeline.

**Platform:** Desktop App and Browser only. Absent on mobile.

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `widget_code` | string | Yes | Full Chart.js / HTML / JavaScript code for the widget |
| `title` | string | Yes | Widget title displayed above the visualization |
| `loading_messages` | array | No | Messages shown while the widget loads |
| `i_have_seen_read_me` | boolean | Yes | Acknowledgment flag (must be `true`) |

Unlike `chart_display_v0` (limited to line, bar, scatter), `visualize:show_widget` supports full Chart.js capabilities: **pie, donut, histogram, heatmap, bubble charts**, and more. It also supports six module types: `diagram`, `mockup`, `interactive`, `data_viz`, `art`, and `chart`.

The widget renders **inline in the chat stream** — not in the artifact sidebar — making it functionally equivalent to `chart_display_v0` in terms of placement, but with dramatically broader visualization capabilities.

| | `chart_display_v0` | `visualize:show_widget` |
| --- | --- | --- |
| Platform | Mobile only | Desktop + Browser only |
| Chart types | Line, bar, scatter | Full Chart.js (pie, donut, histogram, heatmap, bubble...) |
| Rendering | Inline | Inline |
| Discovery | Via `tool_search` (deferred) | Not in tool layer |
| Parameters | 5 | 4 |
| Stability | Intermittent instability | Stable |

---

## Chapter 6 — Calendar & Device Integration

Thirteen tools handle scheduling and device-level functionality on mobile (8 on Android, 13 on iOS). Six manage calendar events (search, create, update, delete), and two create alarms and timers. Together, they make Claude a capable personal assistant that can interact with your actual schedule and device.

### The Calendar Suite

| Tool | Version | Purpose |
| --- | --- | --- |
| `calendar_search_v0` | v0 | List available calendars |
| `event_search_v0` | v0 | Search events in a time range |
| `event_create_v0` | v0 | Create a single event |
| `event_create_v1` | v1 | Create multiple events (batch) |
| `event_update_v0` | v0 | Update existing events |
| `event_delete_v0` | v0 | Delete events |

**Platform:** All 6 calendar tools are mobile-only deferred tools, discovered via `tool_search`. On browser and desktop, Claude responds with: "I can only search Google Drive documents, not Google Calendar."

> **⚠️ Project vs. standard chat — different tools, different data.** If you use calendar features inside a Claude Project with Google Calendar connected, you get a **completely different set of tools** (MCP Connector tools like `list_gcal_calendars`) with different IDs, different response formats, and different account visibility. IDs from one environment don't work in the other. The deferred tools documented in this chapter only appear in standard mobile chats. See "The Dual-Layer Architecture" below for the full comparison.

**Connection required:** Calendar tools require connecting Google Calendar in Claude.ai Settings → Connectors. This is **not connected by default** — users must explicitly enable it. Without the connection, the tools will not function even on mobile.

Most calendar operations follow a two-step pattern: search to find relevant events and get their IDs, then act (update or delete) using those IDs. Claude typically calls `user_time_v0` first to establish the timezone context, then constructs ISO 8601 timestamps for the calendar API.

The `v0` → `v1` evolution on `event_create` reveals a paradigm shift: `v0` creates a **draft/template** that requires user confirmation in the mobile UI before the event is committed — it returns only a string message and the event does not appear in search results until confirmed. Once the user confirms, the event is created with status **"confirmed"** and the `description` field persists correctly. `v1` creates events **directly on the server** without user confirmation and returns structured `{calendar_id, event_id}` pairs with batch support. In testing, v1-created events showed **"confirmed"** status in some sessions and **"tentative"** in others — the status may depend on the calendar provider or account configuration. The `description` field did not persist in v1 testing (see Description field bug below). For programmatic workflows needing IDs, v1 is the only option; for user-facing event creation with full data fidelity, v0 is more reliable.

#### The Dual-Layer Architecture

Testing revealed a significant architectural detail: Claude's calendar system operates on **two parallel layers** that are completely incompatible. Using calendar features in a Project gives you entirely different tools, different IDs, and different account visibility than using them in a standard mobile chat. An event ID from one layer cannot be used in the other. This is the single most important thing to understand about Claude's calendar system.

**Layer 1 — Google Calendar API** (MCP/Connector tools: `list_gcal_calendars`, `list_gcal_events`, `fetch_gcal_event`, `find_free_time`): Always-loaded when Google Calendar is connected as a Connector in a Project. These are **thin wrappers around the raw Google Calendar API** — responses are `calendar#calendarListEntry` objects with `etag`, `colorId`, `notificationSettings`, `conferenceProperties`, and other native Google fields. Uses email-style calendar IDs. Returns rich data but only sees the **primary** connected Google account (3 calendars in testing vs 15 for Layer 2).

**Layer 2 — Event management tools** (`calendar_search_v0`, `event_create_v0/v1`, `event_search_v0`, `event_update_v0`, `event_delete_v0`): Mobile-only deferred tools discovered via `tool_search`. These operate through Anthropic's **intermediate abstraction layer** — responses are simplified with clean `{status, calendars/events}` envelopes, numeric string IDs, and a `name` field (not Google's `summary`). Can see **all** connected accounts (15 calendars across 4 accounts in testing).

The two layers do not share IDs and cannot cross-reference. Layer 1 uses email-style calendar IDs and long alphanumeric event IDs. Layer 2 uses **internal numeric string IDs** (e.g., `calendar_id: "12"`, `event_id: "1256"`) across all tools. The architectural separation likely reflects their development history: the deferred tools were built as a consumer-friendly mobile feature with multi-account abstraction, while the MCP Connector tools were added later as a direct API passthrough for power users in Projects.

#### Calendar Gotchas

**Default calendar selection:** When creating events without specifying `calendar_id`, the tool may default to a secondary account rather than the primary calendar. Users with multiple Google accounts should specify the calendar explicitly.

**Description field bug (v1 only):** In testing, the `description` parameter passed to `event_create_v1` was not persisted — `event_search_v0` returned it as empty. However, `event_create_v0` (after user confirmation) persists descriptions correctly. This appears to be a v1-specific bug or limitation.

**Event creation response:** `event_create_v1` returns a `create_results` array containing `{calendar_id, event_id}` — both numeric internal IDs needed for subsequent update/delete operations. `event_create_v0` returns only a string confirmation message without IDs — use `event_search_v0` to retrieve IDs after v0 creation.

**All-day events:** `event_create_v0` accepts `all_day: true` without requiring `end_time`. However, `event_create_v1` requires `end_time` even for all-day events — omitting it returns: "DTEND and DURATION cannot both be null." Set `end_time` to the next day's midnight (e.g., start `2026-02-17T00:00:00`, end `2026-02-18T00:00:00`).

**Nudges (reminders):** Events in the search response include a `nudges` array containing reminder settings (e.g., `{"method": "email", "minutes_before": 60}` or `{"method": "notification", "minutes_before": 30}`). Nudges cannot be set via `event_create` — the parameter is not in the creation schema. However, they **can** be set and modified via `event_update_v0`, which replaces the default reminder with the specified configuration.

**⚠️ Recurring event dangers (confirmed data loss):** The behavior of `event_update_v0` on recurring events depends critically on whether `start_time` is included:

- **Update WITHOUT `start_time`** (e.g., changing only `location` or `title`): Applies to **all instances** in the series. The recurrence chain survives intact. This is safe.
- **Update WITH `start_time`** (targeting a specific occurrence): **Severs the recurrence chain** — the targeted instance receives the update, but all other instances permanently disappear. In testing, updating one Friday occurrence of a weekly meeting caused all future weeks to vanish, requiring re-invitation from the organizer. This happens on both owned and non-owned events.
- **Delete:** Removes **all instances** (past and future) of the recurring series. There is no "delete this instance only" option.

**Never include `start_time` in an update to a recurring event unless you intend to destroy the series.** There is no "this instance only" modifier.

**Attendees and nudges via update:** While `event_create_v0` silently ignores the `attendees` parameter, `event_update_v0` can successfully **add attendees and modify nudges** on existing events. Both persist across all instances when updated without `start_time`. This means the workaround for adding guests is: create the event first, then update it with attendees.

**Attendees silently ignored on create:** The `attendees` parameter can be passed to `event_create_v0` without error, but it is **silently dropped** — no guests are added to the created event. To add attendees, create the event first via v0 (confirm) or v1, then use `event_update_v0` with the attendees array.

**Recurrence:** Recurring events can be created via `event_create_v0` using the three-field format: `{"frequency": "weekly", "human_readable_frequency": "weekly", "rrule": "FREQ=WEEKLY;WKST=MO;BYDAY=MO"}`. The format mirrors what `event_search_v0` returns for existing recurring events. Simple formats and bare RRULE strings are rejected — only the three-field format works. Since v0 requires user confirmation, the user must approve the event in the UI before it commits. `event_create_v1` does not support recurrence at all.

> **📸 IMAGE: `fig_09_calendar_event.png`**
> Screenshot showing Claude creating a calendar event.
> *[Upcoming]*

### Alarms & Timers

`alarm_create_v0` and `timer_create_v0` are **mobile-only deferred tools** — they are discovered via `tool_search` and create actual device-level alarms and timers through OS integration.

**Platform:** Mobile App only. On browser and desktop, these tools are completely absent (not merely limited) — Claude falls back to suggesting Google timer, a voice assistant, or creating an artifact-based timer.

#### `alarm_create_v0`

Creates alarms at a specific clock time with optional recurrence (weekday array) and vibration control. On mobile, this creates an actual device alarm that fires even if the Claude app is closed.

The alarm widget displays: bell icon, time, label, and a "View alarms" link (localized — e.g., "Vedi sveglie" on Italian devices). A confirmation message reads "This alarm was set on your device" (localized — "Questa sveglia è stata impostata sul tuo dispositivo").

> **📸 IMAGE: `fig_10_alarm_widget.png`**
> Screenshot of the alarm widget on mobile (Italian locale), showing bell icon, time, label, and "Vedi sveglie" link.
> *[Upcoming]*

#### `timer_create_v0`

Creates countdown timers from a duration in seconds. Claude converts natural language ("5 minutes" → 300 seconds) automatically. On mobile, this creates an actual device timer.

The timer widget displays: clock icon, duration, and a "View timers" link (localized — "Vedi Timer").

> **📸 IMAGE: `fig_11_timer_widget.png`**
> Screenshot of the timer widget on mobile, showing clock icon and duration.
> *[Upcoming]*

#### Alarm vs. Timer: When to Use Which

| | `alarm_create_v0` | `timer_create_v0` |
| --- | --- | --- |
| Input | Clock time (hour + minute) | Duration (seconds) |
| Recurring | Yes (weekday array) | No |
| Parameters | 5 | 2 |
| Best for | "Wake me at 7 AM" | "Timer for 12 minutes" |
| Platform | Mobile only (deferred) | Mobile only (deferred) |
| Desktop/Browser fallback | Suggests voice assistant or Google | Suggests Google timer or artifact |

### iOS Reminders Suite (v1.4)

Five additional deferred tools are available exclusively on iOS, providing full CRUD access to the native iOS Reminders app. These tools were independently discovered by DMontgomery40 (GitHub) and verified empirically in v1.4 testing.

| Tool | Purpose |
| --- | --- |
| `reminder_list_search_v0` | Lists available Reminders lists |
| `reminder_create_v0` | Creates a new reminder |
| `reminder_search_v0` | Searches existing reminders |
| `reminder_update_v0` | Updates an existing reminder |
| `reminder_delete_v0` | Deletes a reminder |

**Platform:** iOS only — confirmed absent on Android. Deferred via `tool_search`.

> **Note:** DMontgomery40's original GitHub issue #2 has a typo: "reminders_create_v0" — the correct tool name is `reminder_create_v0` (singular, no trailing "s").

Key differences from the calendar suite:
- **Alarms use `secondsBefore`** for reminder alerts; calendar tools use `minutesBefore` for nudges. This is an important distinction when setting reminders with alarms.
- **Update and delete require IDs** from a prior `reminder_search_v0` call — there is no title-based targeting. This follows the same search-then-act pattern as the calendar suite.

See Tool Cards 29–33 in the Technical Annex for full parameter schemas and examples.

---

## Chapter 7 — Search & Data Tools

The largest category: eleven tools that retrieve information from the web, Google Places, Google Drive, sports data providers, weather services, and recipe databases. These are the tools that make Claude more than a language model — they give it access to live, current information.

### Web Tools

`web_search` queries a search engine and returns the top 10 results with snippets. Short queries (1–6 words) perform best. Claude uses it proactively for anything that might have changed since its training data — current events, positions held, prices, scores. It is the single most frequently called tool in the entire system.

The typical research workflow chains `web_search` into `web_fetch`: search to find sources, fetch to read them in full. For example, when a user asks "What did the Fed decide last week?", Claude calls `web_search` with a short query like `"Fed interest rate decision"`, gets 10 snippets with titles, URLs, and preview text, identifies the most authoritative source (a Reuters or AP article), then calls `web_fetch` on that URL to read the full article. The response is synthesized with citations. Claude decides when to search vs. answer from memory based on topic recency — fast-changing information (prices, scores, current office holders) triggers a search; stable facts (historical dates, scientific principles) don't.

`web_fetch` retrieves the full content of a webpage by URL (9 parameters — v1.3 documented 8; the new `html_extraction_method` parameter is documented in Tool Card 15). Important constraint discovered through testing: `web_fetch` can only access URLs that appeared in `web_search` results or were provided directly by the user. Attempting to fetch an arbitrary URL that wasn't surfaced by search or provided by the user is restricted. It cannot access login-gated or paywalled content — paywalled pages return only the page shell (navigation menus, headers) with no article body, a silent truncation rather than an explicit error. Common failure modes include JavaScript-heavy sites (which return minimal content) and rate limiting on repeated fetches.

`image_search` finds images on the web and returns them inline. The tool description mentions dimensions are included, but in testing Claude did not surface pixel dimensions in its response — they may be available in the raw tool response metadata but not exposed to the user. It has an unusual constraint: minimum 3 results per call (requesting fewer is bumped to 3), with a maximum of 5 as an upper bound (requesting 5 may return 4). Content safety restrictions apply — copyrighted characters, celebrity photos, and graphic content are filtered.

### Weather

`weather_fetch` displays an interactive weather widget with a 5-day forecast. It is one of the more polished UI widgets in the toolkit — a gradient card that changes appearance based on conditions.

**Platform:** Browser and Desktop App only. Not available on mobile — Claude falls back to `web_search` for weather data, returning text-only results without a widget.

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `latitude` | number | Yes | Location latitude |
| `longitude` | number | Yes | Location longitude |
| `location_name` | string | Yes | Human-readable name (e.g., "Milan, Lombardy, IT") |

**Widget Features:**
- Blue/dark gradient card that changes by conditions — blue for sunny, dark for cloudy/night, rain animation for rain
- Current temperature and conditions prominently displayed
- 5-day forecast with daily highs and precipitation percentage
- "Weather data from Google" attribution at the bottom
- Claude adds a conversational summary below the widget

**Unit behavior:** The underlying Google Weather API (`weather.googleapis.com`) returns data exclusively in Fahrenheit (`unitsSystem=IMPERIAL`) — there is no Celsius toggle at the API level. The widget displays this raw Fahrenheit data. Claude's text summary below the widget converts to the convention of the queried city — Celsius for London, Fahrenheit for New York. The system prompt says "use user's home location for units" but actual behavior follows the queried city's convention. The unit conversion is entirely Claude's responsibility, not a widget or API feature.

**Geographic coverage:** The API does not support all global locations. In testing, Tokyo coordinates returned a 404 error. When the API fails, Claude falls back to `web_search` for weather data.

**Location label:** The API echoes back whatever `location_name` Claude passes in as input, plus a separate `country` field. The label duplication observed in earlier testing ("MILAN, LOMBARDY, IT, IT" on desktop, "MILAN, ITALY, ITALY" on browser) is likely a front-end rendering artifact from concatenating `location` and `country` fields, not an API difference. The raw API response returns `"location": "Milan, Lombardy, IT"` with `"country": "IT"` as a separate field.

> **📸 IMAGE: `fig_12_weather_widget.png`**
> Screenshots comparing the weather widget on browser vs. desktop app for the same city. Note that the location label is echoed from Claude's input parameter, and the widget always displays Fahrenheit regardless of locale.
> *[Upcoming]*

### Recipes

`recipe_display_v0` displays an interactive recipe widget with adjustable servings, built-in timers, and a full-screen cooking mode. It is the most feature-rich widget in the entire toolkit.

**Platform:** Browser and Desktop App only. Not available on mobile — Claude falls back to a plain text recipe in conversation, without interactive features.

**Widget Features:**
- **Image carousel** at the top — web-sourced photos with attribution ("Results from the web")
- Title and description
- **Servings adjuster** with +/- controls — all ingredient amounts scale proportionally
- Toolbar with edit/customize, print, and copy icons
- **INGREDIENTS section** with scaled amounts based on selected servings
- **STEPS section** with numbered instructions and inline timers (▶ MM:SS format)
- **NOTES section** at bottom on a beige background
- **"Get cooking" / "Start cooking" button** — launches a full-screen step-by-step wizard:
  - One step at a time with < > navigation ("Step N of N")
  - Contextual timers only on steps that have `timer_seconds` set
  - Timer states: blue "Start" → counting down → green "Done!"
  - Ingredient amounts shown inline in step text, scaled to current servings
- Timers work in both views: inline play button (▶) and cooking mode (Start/Done)

**Watch Out:**
- Minor rounding in ingredient scaling (e.g., 1.3× may show 2.5 instead of 2.6 for some values)
- Grammar quirk: "1 teaspoons" — the widget doesn't singularize units for quantity 1
- Default servings and images may vary between sessions — Claude chooses the serving count (schema default is 4), and the recipe widget sources different images per session. This is not a platform-specific difference but a per-generation variation
- Mobile fallback: plain text recipe in conversation (no widget, no cooking mode)

> **📸 IMAGE: `fig_13_recipe_widget.png`**
> Screenshots comparing the recipe widget on browser vs. desktop app for the same recipe, showing per-generation variations in servings and images.
> *[Upcoming]*

> **📸 IMAGE: `fig_14_recipe_cooking_mode.png`**
> Screenshots of the cooking mode: step-by-step wizard with navigation arrows, a timer in "Start" state, and a timer in "Done!" state.
> *[Upcoming]*

### Places & Maps

`places_search` supports up to 10 simultaneous queries against Google Places, with results deduplicated across queries. Each query can specify its own `max_results` (1–10), and an optional `location_bias_lat`/`location_bias_lng` biases results geographically. The data returned is rich: name, address, coordinates, rating, review count, hours, price level, place types, and full review text.

`places_map_display_v0` renders interactive maps inline — in either simple marker mode or full itinerary mode with multi-day routing, travel times, and tour guide notes.

#### Markers Mode (from cross-platform testing)

- Rating badges displayed on map markers
- **Sidebar:** Title, Copy dropdown (text/table export), photo + rating + reviews + category per location
- **Click marker →** expanded card: large photo, "Directions" button (arrow icon), phone number, website link, "Notes from Claude" section, close (×) button
- "Directions" triggers an **"Open external link" confirmation dialog** ("You're leaving Claude to visit an external link:") showing the full Google Maps URL, with "Cancel" and "Open link" buttons
- "1 of 3" pagination for browsing through locations

#### Itinerary Mode (from cross-platform testing)

- Blue route lines connecting numbered stops with photo thumbnails on the map
- Title + narrative description
- **"Open route" button →** Google Maps multi-stop URL (note: opens directly with **no confirmation dialog** — inconsistent with markers mode's behavior)
- Copy dropdown (text/table)
- Day sections with arrival times per stop

The typical workflow chains three tools: ask "best coffee near the Duomo" → Claude calls `user_location_v0` for coordinates → pipes those into `places_search` with location bias → takes the resulting `place_id` values and feeds them into `places_map_display_v0` → an interactive map with pins appears inline.

> **📸 IMAGE: `fig_15_places_markers.png`**
> Screenshot of places markers mode showing the sidebar overview with ratings and the expanded detail popup for a clicked marker.
> *[Upcoming]*

> **📸 IMAGE: `fig_16_places_itinerary.png`**
> Screenshot of places itinerary mode with walking route lines, numbered stops, and "Open route" button.
> *[Upcoming]*

### Sports Data

`fetch_sports_data` covers 20 leagues across 10 sports, pulling from SportRadar. It supports three data types: live/recent scores, season standings, and detailed game statistics. The depth of the stats response is remarkable: game stats include per-quarter scoring breakdowns, **60+ statistical fields per player and per team** — standard box score stats (FG, 3PT, FT, rebounds, assists, steals, blocks, turnovers, fouls), advanced metrics (`effective_fg_pct`, `true_shooting_pct`, `offensive_rating`, `defensive_rating`, `efficiency_game_score`, `possessions`, `points_per_possession`), zone shooting (`field_goals_at_rim_made/att/pct`, `field_goals_at_midrange`), situation stats (`fast_break_att/made/pct`, `second_chance_att/made/pct`, `points_in_paint_att/made/pct`, `points_off_turnovers`), individual metrics (`pls_min` +/-, `rebounds_pct`, `steals_pct`, `turnovers_pct`), and flags (`double_double`, `triple_double`). Game-level data includes `most_unanswered` run tracking, `bench_points`, and `biggest_lead`. The typical workflow is: fetch scores to get game IDs, then fetch `game_stats` for the specific game of interest.

> **📸 IMAGE: `fig_17_sports_data.png`**
> Screenshot of Claude displaying sports standings or scores.
> *[Upcoming]*

### Google Drive

`google_drive_search` uses Google Drive's native query syntax with optional semantic filtering. It supports searching by name, full text, MIME type, date, starred status, folder, and owner. Key gotcha: `fullText` queries require `order_by: "relevance desc"` or results may be empty. The `owners`, `writers`, and `readers` fields require email addresses — not names. **MIME type support is limited to Google Docs and Folders** — searching for `application/vnd.google-apps.spreadsheet` returns empty results. Example query: `api_query: "name contains 'Q3' and mimeType = 'application/vnd.google-apps.document'"` returns all Google Docs with "Q3" in the title.

**Response format (confirmed):** Each result includes: `source` (document title), `document_content` (with indexed spans for citation), and metadata fields: `created_time`, `last_modified_by`, `last_modified_time`, `mime_type`, `owner`, `time_since_created`, `time_since_last_modified`, `url`. Small documents return 100% of content inline. Larger documents are **trimmed** (showing `percentage_of_full_document`) with a reminder to use `google_drive_fetch` for the full text. Very large documents return: "This file is too large."

`google_drive_fetch` retrieves full Google Doc contents by document ID. It supports batch fetching via an array of IDs. Important limitation: Google Docs only — not Sheets, not Slides. Error handling is inconsistent: Folders and Sheets return a clean message ("The contents of this file cannot be retrieved. Only Google Docs are supported, and this document has a MIME type of [type]"), while Slides return a raw `error -32000` with no human-readable explanation.

---

## Chapter 8 — The Memory Stack: How Claude Remembers You

Three tools form Claude's memory system, operating at different levels of persistence and scope. Understanding this stack explains why Claude sometimes "remembers" things and sometimes doesn't.

### The Three-Layer Model

| Layer | Tool | What It Stores | Persistence | Scope |
| --- | --- | --- | --- | --- |
| Identity | `memory_user_edits` | Facts about the user | Permanent (until removed) | Global per project |
| Episodic (topic) | `conversation_search` | Past conversation content | Until chat deleted | Project-scoped |
| Episodic (time) | `recent_chats` | Recent conversations | Until chat deleted | Project-scoped |

`memory_user_edits` is the most thoroughly documented tool in this ecosystem, thanks to the author's prior guide ([github.com/frmoretto/claude-memory-user-edits-guide](https://github.com/frmoretto/claude-memory-user-edits-guide)). The core insight: memory edits work for **facts**, not **behaviors**. "User works at Acme Corp" persists reliably; "Always check docs first" does not. The tool supports four commands: `view`, `add`, `remove`, `replace`. Maximum 30 edits, 500 characters each (client-side validation — the schema `maxLength` is 500, confirmed cross-surface in v1.4 testing). The 30-entry count cap is the binding constraint in practice.

> **v1.4 correction:** v1.3 stated a 200-character hard server limit. This was incorrect. The actual limit is 500 characters, enforced client-side with the error message "String should have at most 500 characters." The limit may vary by tier or build.

> **📸 IMAGE: `fig_18_memory_view.png`**
> Screenshot of Claude showing stored memory edits.
> *[Upcoming]*

`conversation_search` and `recent_chats` provide episodic recall — the ability to reference what was discussed in past conversations. The first searches by keyword, the second retrieves by time. Both respect project boundaries: conversations in different Claude Projects are isolated from each other.

When layers conflict — for example, `memory_user_edits` says "User works at Acme" but a recent conversation says "I just left Acme" — Claude prioritizes the most recent information from the conversation. However, the stale memory edit persists and may resurface in future conversations until explicitly updated or removed. This is the most common source of "Claude remembers wrong things" complaints. The fix is simple: tell Claude to update or remove the outdated memory.

### Memory Settings

Memory behavior is controlled by toggles in Settings → Capabilities:

- **Memory toggle:** Master on/off for the memory system
- **"Generate memory from chat history" toggle:** Controls whether Claude automatically derives memory edits from conversations
- **"Search and reference chats" toggle:** Controls whether `conversation_search` and `recent_chats` are available
- **"Memory from your chats" preview card:** Shows when memory was last updated (e.g., "Updated 3 hours ago")
- **Project scoping:** Memory in one Project is isolated from memory in other Projects and from standard chats

#### `conversation_search` vs. `recent_chats`: When to Use Which

| | `conversation_search` | `recent_chats` |
| --- | --- | --- |
| Query type | Topic / keyword | Time-based |
| Best for | "Did we discuss X?" | "What was my last chat?" |
| Params | `query` + `max_results` | `n` + `sort_order` + `before`/`after` |
| Search tip | Use nouns not verbs — "Python migration" works, "discussed about" doesn't | Use `before`/`after` datetime filters for specific ranges |

---

## Chapter 9 — Computer Use: Files, Code & Artifacts

Five tools give Claude access to a Linux computer (Ubuntu 24) for file creation, code execution, and artifact production. These power the "Create Files" feature in Claude.ai.

| Tool | Purpose | Params |
| --- | --- | --- |
| `bash_tool` | Execute shell commands | 2 (`command`, `description` — both required) |
| `view` | Read files, directories, and images | — |
| `create_file` | Create new files with content | — |
| `str_replace` | Edit existing files (find and replace) | 4 (`path`, `old_str`, `new_str`, `description` — `new_str` optional: empty = delete) |
| `present_files` | Share files with the user for download | — |

> **v1.4 correction:** v1.3 documented `bash_tool` as 1 parameter and `str_replace` as 3 parameters. Both now require a `description` parameter. For `str_replace`, `new_str` is optional — omitting it performs a deletion of the matched string.

The computer use tools are architecturally distinct from other tools: they operate on a sandboxed Linux environment with network access (limited to whitelisted domains), a filesystem that resets between tasks, and a skill system that provides best-practice templates for document creation. Files are created in `/home/claude`, finalized to `/mnt/user-data/outputs`, and shared via `present_files`.

`str_replace` has an unusual design constraint: the search string must appear exactly once in the file. If it matches zero times, the edit fails with the exact error: "String to replace not found in file". If it matches multiple times, the edit fails with: "String to replace found multiple times, must be unique". Both error messages confirmed in testing. This forces precise, targeted edits rather than bulk find-and-replace operations.

The sandboxed environment resets between tasks — there is no persistent filesystem across conversations. Network access is limited to a whitelist of domains (npm, PyPI, GitHub, Ubuntu repos, and the Anthropic API), so Claude cannot fetch arbitrary URLs from within code execution. Attempting to reach an unlisted domain returns an HTTP 403 Forbidden from the egress proxy with the header `x-deny-reason: host_not_allowed`. The proxy is Envoy-based and uses JWT authentication for container identification.

When the output is a React component, HTML page, SVG, or Mermaid diagram, Claude creates an artifact (rendered inline in the chat or in a sidebar). When the output is a document (docx, xlsx, pptx, pdf), Claude creates a file and shares it via `present_files` as a downloadable card.

> **📸 IMAGE: `fig_19_file_creation.png`**
> Screenshot of Claude creating and presenting a file.
> *[Upcoming]*

### Execution Settings

Computer use behavior is controlled by toggles in Settings → Capabilities:

- **"Cloud code execution and file creation" toggle:** Master on/off for computer use tools
- **"Allow network egress" toggle:** Controls whether the sandbox can access external domains
- **"Domain allowlist" dropdown:** "Package managers only" (default) or custom domain list
- **"AI-powered artifacts" toggle:** Separate from basic artifacts — controls whether Claude can use the Anthropic API within artifacts (Claude-in-Claude)

#### The Skill System

Claude.ai ships with public skills for document types: `docx`, `pptx`, `xlsx`, `pdf`, `frontend-design`, and `product-self-knowledge`. Each skill is a markdown file (e.g., `/mnt/skills/public/docx/SKILL.md`) containing best practices, code templates, and validation procedures. Users can add custom skills as markdown files in their Projects, placed in `/mnt/skills/user/`.

Skills function as just-in-time instruction: before creating a Word document, Claude reads the docx skill file to learn formatting rules, validation steps, and common pitfalls — producing dramatically better output than without the skill. This pattern explains why document creation quality varies significantly between standard chats and properly configured Projects.

#### Skill Filesystem Mount Topology (v1.4)

The skill system uses an ephemeral overlay mount. `/mnt/skills/user` is a temporary filesystem — custom skills placed there by the user are available only within the current session. The `/mnt/skills/public` directory contains Anthropic's bundled skill files.

#### Egress Proxy Architecture (v1.4)

The sandbox's network access uses an Envoy-based egress proxy with JWT authentication for container identification. When a request targets a non-whitelisted domain, the proxy returns HTTP 403 Forbidden with an `x-deny-reason: host_not_allowed` header. The JWT tokens identify the specific container, enabling per-session traffic policies.

### The Artifact Execution Layer (v1.4)

Three capabilities discovered in v1.4 reveal that artifacts are not just display surfaces — they are an execution layer with access to APIs, storage, and external services.

#### `anthropic_api_in_artifacts`

React artifacts can call the Anthropic API directly — without an API key. The runtime injects authentication at the infrastructure level, enabling "Claude-in-Claude" patterns where an artifact contains a working AI chatbot or content generator.

**Platform:** Desktop App and Browser (React artifacts only). HTML artifacts are blocked by CSP (`TypeError: Failed to fetch`).

Key findings:
- HTTP 200 confirmed on both Desktop and Browser React artifacts
- Model used: `claude-sonnet-4-5-20250929`
- New response fields in v1.4: `cache_creation` object (with `ephemeral_5m` and `ephemeral_1h` fields), `service_tier`, `inference_geo` — these are standard Anthropic API response fields, not artifact-specific
- Controlled by the "AI-powered artifacts" toggle in Settings → Capabilities

**Update (April 2026):** Anthropic has since officially documented this capability as "AI-powered artifacts" in their help center. The mechanics match our reverse-engineering findings: artifacts call Claude via an internal API, authentication is injected by the runtime, no API keys are needed, and usage is billed to the end user's subscription. This feature is available on Free, Pro, Max, Team, and Enterprise plans.

#### `persistent_storage` (`window.storage`)

> **Update (April 2026):** Anthropic now officially documents `persistent_storage` as true cross-session persistent storage for **published** artifacts on Pro, Max, Team, and Enterprise plans. Two modes: personal (per-user, private) and shared (all users see same data). 20 MB limit per artifact, text-only. Storage only activates after the artifact is published — during development, storage calls succeed in-session but do not persist. Unpublishing permanently deletes all storage data. Our v1.4 testing on unpublished artifacts correctly observed session-scoped behavior.

React artifacts have access to a `window.storage` object with four methods: `get`, `set`, `delete`, `list`. The backend uses gRPC/protobuf (`StorageSetResponse`/`StorageGetResponse`). Storage supports two scopes: personal (`shared=false`) and shared (`shared=true`). Maximum 20 MB per artifact.

**Platform:** Desktop App and Browser (React artifacts only — `window.storage` is `undefined` in HTML artifacts). Requires Pro, Max, Team, or Enterprise plan for cross-session persistence.

#### MCP Endpoints in Artifacts (v1.4)

React artifacts can access MCP connector endpoints — specifically Google Calendar and Gmail. This means an artifact can read calendar events or create email drafts, extending the artifact execution layer beyond simple display into service integration.

**Update (April 2026):** Anthropic now officially documents MCP integration for artifacts on Pro, Max, Team, and Enterprise plans (web and desktop). Artifacts can connect to external services through configured MCP servers (both Anthropic-provided and custom). Each user must independently authenticate MCP servers before an artifact can access them.

#### Mobile Capability Cliff (v1.4)

None of the artifact execution layer capabilities (`anthropic_api_in_artifacts`, `persistent_storage`, MCP endpoints) are available on mobile. Combined with the absence of `visualize:show_widget`, `weather_fetch`, and `recipe_display_v0`, mobile users have access to a fundamentally different — and more limited — artifact experience.

---

## Chapter 10 — The Meta-Tool: tool_search

The most architecturally significant tool in the entire system is `tool_search` — a tool whose only purpose is discovering other tools. It represents a recursive layer in Claude.ai's architecture: the AI can search for its own capabilities at runtime.

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `query` | string | Yes | Search keywords to match against tool names and descriptions |
| `limit` | integer | No | Maximum results (1-20, default 5) |

### Platform-Dependent Behavior (updated v1.4)

`tool_search` is not a universal capability — it behaves fundamentally differently depending on your platform:

- **Browser (claude.ai):** `tool_search` is **conditionally available** — it appears only when MCP connectors (Google Calendar, Gmail) are active, and returns only MCP tools with a `Provider:tool_name` namespace prefix (e.g., "Google Calendar:gcal_list_calendars"). Without active connectors, `tool_search` does not exist on browser. Consumer deferred tools are never available on browser regardless of connector state.
- **Desktop App:** `tool_search` exists but only discovers MCP integration tools. If you have Claude in Chrome and Filesystem connected, it returns all 32 MCP tools (18 Chrome + 14 Filesystem) **regardless of query** — keywords only affect result ordering, not which tools appear. It behaves as a "load everything" operation rather than a filtered search. It does **not** discover consumer deferred tools like `alarm_create_v0` or `chart_display_v0`.
- **Mobile App:** `tool_search` exists and discovers the consumer deferred tools documented in this book. Note: `user_time_v0` and `user_location_v0` are **always-loaded** on mobile and therefore bypass `tool_search` entirely — they do not appear in search results because they are already loaded. This is the only platform where the discovery methodology below is reproducible.

> **v1.4 correction:** v1.3 stated `tool_search` does not exist on browser. It is conditionally available when MCP connectors are active. v1.3 also stated `user_time_v0` and `user_location_v0` were discoverable via `tool_search` — they are always-loaded on mobile and do not need discovery.

This is the single most important caveat in this chapter: **the discovery log below was produced on the mobile app and is only reproducible there.**

### The Enumeration Methodology

This book was made possible by systematic keyword sweeps through `tool_search` on the Claude mobile app. The methodology: start with broad functional categories ("user," "create," "search," "display"), then narrow with specific terms ("alarm," "chart," "calendar," "timer"). Each query returns matching tools with their full parameter schemas.

### The Discovery Log

The following are the actual `tool_search` calls used to enumerate the toolkit for this book, conducted on the **Claude mobile app** (no Project, no MCP integrations):

> **Environment note:** These results are only reproducible on the Claude mobile app in a standard chat without Project or MCP connections. On the browser, `tool_search` only discovers MCP tools (when connectors are active). On the desktop app, it returns MCP tools instead.

> **Reproducibility note:** `tool_search` uses fuzzy matching — the same query may return different results across sessions. In verification testing, `query="user"` returned `user_location_v0` plus 6 other tools but **missed** `user_time_v0`, which required a separate query ("time clock current"). Tools bleed across unrelated queries (e.g., `user_location_v0` appeared in nearly every search). The discovery log below represents the results from the original enumeration session; your results may vary. The key is that all 11 tools are discoverable — just not always from the same queries.

We started with the broadest possible query: "user". In the original v1.2/v1.3 enumeration session, this surfaced the two context tools. However, v1.4 testing revealed that `user_time_v0` and `user_location_v0` are **always-loaded** on mobile — they appear in this log because they were discovered during the original session, but they bypass `tool_search` in normal operation.

```
Query: tool_search(query="user", limit=20)
Results:
  → user_time_v0 — "Retrieves current time in user timezone" (note: always-loaded on mobile as of v1.4)
  → user_location_v0 — "Gets user geographic location" (note: always-loaded on mobile as of v1.4)
```

Pivoting to action verbs — "create display generate" — revealed the visualization and scheduling tools.

```
Query: tool_search(query="create display generate", limit=20)
Results:
  → chart_display_v0 — "Renders inline interactive charts"
  → alarm_create_v0 — "Creates device alarms"
  → timer_create_v0 — "Creates countdown timers"
  → event_create_v0 — "Creates calendar events"
  → event_create_v1 — "Batch calendar event creation"
```

Data retrieval queries surfaced the calendar search tools.

```
Query: tool_search(query="search fetch data", limit=20)
Results:
  → event_search_v0 — "Searches calendar events"
  → calendar_search_v0 — "Lists available calendars"
```

The search queries "send message email" returned only tools already found, suggesting the messaging tools (`message_compose_v1`, `ask_user_input_v0`) are always-loaded and therefore not indexed by `tool_search`.

```
Query: tool_search(query="send message email slack reminder", limit=20)
Results:
  → alarm_create_v0 (again)
  → event_create_v0 (again)
  → timer_create_v0 (again)

Query: tool_search(query="map place weather stock price translate", limit=20)
Results:
  → user_location_v0 (again)
  → event_create_v0 (again)
```

The most telling result was the empty return for media processing queries — confirmation that media processing, if it exists at all in deferred form, uses naming conventions our sweeps didn't catch.

```
Query: tool_search(query="audio image video file convert", limit=20)
Results: NONE
  → Confirms no media processing tools exist in the deferred toolkit
```

Finally, mutation verbs surfaced the remaining calendar tools.

```
Query: tool_search(query="update delete remove", limit=20)
Results:
  → event_update_v0 — "Updates existing events"
  → event_delete_v0 — "Deletes calendar events"
```

Negative results are as important as positive ones. The empty return for "audio image video file convert" confirms that media processing capabilities, if they exist, are always-loaded (like `image_search`) rather than deferred.

> **📸 IMAGE: `fig_20_tool_search_results.png`**
> Screenshot showing the raw output of a `tool_search` call on mobile.
> *[Upcoming]*


### What Might Still Be Hiding

Keyword-based discovery has inherent limitations — you can only find tools matching your search terms. Tools with unexpected names or descriptions might not surface. Additionally, some tools may be gated behind features, account tiers, or A/B tests and therefore invisible to `tool_search` in some sessions. The iOS Reminders tools discovered by Federico Viticci at MacStories (`reminder_list_search_v0`, `reminder_create_v0`, etc.) are not discoverable via `tool_search` from the web interface, confirming that platform-specific tools exist beyond what web-based discovery can reach.

#### Schema Depth Limitation

A critical finding from testing: `tool_search` is a **discovery and loading mechanism**, not a full schema introspection tool. It returns only top-level parameter names and types with descriptions truncated at approximately 60 characters. It does **not** expose nested object structures (e.g., the `recurrence` object inside `event_create_v0`), array item schemas (e.g., what fields go inside each `event_updates` element), enum values, defaults, or constraints. The only way to determine these deeper schema details is behavioral testing — calling the tools with various payloads and observing what gets accepted, rejected, or returned.

The platform fragmentation documented in Chapter 2 adds another dimension: tools that exist on one platform may not exist on others, and `tool_search` itself behaves differently across platforms. A complete enumeration would require running discovery sweeps on every platform — web, desktop app, mobile app (Android and iOS separately) — and comparing the results.

---

## Chapter 11 — The Complete Tool Reference

This chapter provides a quick-reference index to every documented tool. For full parameter schemas, response formats, and usage examples, see the corresponding Tool Card in the Technical Annex.

### Deferred Tools (loaded on demand via `tool_search` — mobile only)

| Tool | Category | What It Does | Card |
| --- | --- | --- | --- |
| `chart_display_v0` | Visualization | Renders inline interactive charts | [3](#tool-card-3) |
| `alarm_create_v0` | Device | Creates device alarms (mobile-native) | [6](#tool-card-6) |
| `timer_create_v0` | Device | Creates countdown timers (mobile-native) | [7](#tool-card-7) |
| `calendar_search_v0` | Calendar | Lists available calendars | [8](#tool-card-8) |
| `event_search_v0` | Calendar | Searches events in a time range | [9](#tool-card-9) |
| `event_create_v0` | Calendar | Creates a calendar event draft (requires user confirmation) | [10](#tool-card-10) |
| `event_create_v1` | Calendar | Creates multiple events (batch) | [11](#tool-card-11) |
| `event_update_v0` | Calendar | Updates existing events | [12](#tool-card-12) |
| `event_delete_v0` | Calendar | Deletes calendar events | [13](#tool-card-13) |
| `reminder_list_search_v0` | Reminders | Lists iOS Reminders lists | [29](#tool-card-29) |
| `reminder_create_v0` | Reminders | Creates iOS reminders | [30](#tool-card-30) |
| `reminder_search_v0` | Reminders | Searches iOS reminders | [31](#tool-card-31) |
| `reminder_update_v0` | Reminders | Updates iOS reminders | [32](#tool-card-32) |
| `reminder_delete_v0` | Reminders | Deletes iOS reminders | [33](#tool-card-33) |

### Always-Loaded Tools

| Tool | Category | What It Does | Card |
| --- | --- | --- | --- |
| `user_time_v0` | Context | Returns current time in user's timezone (mobile) | [1](#tool-card-1) |
| `user_location_v0` | Context | Gets user coordinates (requires permission, mobile) | [2](#tool-card-2) |
| `web_search` | Search | Queries search engine, returns top 10 results | [14](#tool-card-14) |
| `web_fetch` | Search | Fetches full webpage content by URL | [15](#tool-card-15) |
| `image_search` | Search | Finds and returns inline images | [16](#tool-card-16) |
| `places_search` | Search | Multi-query Google Places search | [17](#tool-card-17) |
| `places_map_display_v0` | Search | Renders interactive maps (markers or itinerary) | [18](#tool-card-18) |
| `fetch_sports_data` | Search | Scores, standings, and game stats for 20 leagues | [19](#tool-card-19) |
| `google_drive_search` | Search | Searches Google Drive with native query syntax | [20](#tool-card-20) |
| `google_drive_fetch` | Search | Retrieves Google Doc contents by ID | [21](#tool-card-21) |
| `weather_fetch` | Search | Interactive weather widget with 5-day forecast | [26](#tool-card-26) |
| `recipe_display_v0` | Interaction | Interactive recipe widget with cooking mode | [27](#tool-card-27) |
| `memory_user_edits` | Memory | Manages persistent memory edits (view/add/remove/replace) | [22](#tool-card-22) |
| `conversation_search` | Memory | Keyword search across past conversations | [23](#tool-card-23) |
| `recent_chats` | Memory | Time-based retrieval of recent conversations | [24](#tool-card-24) |
| `ask_user_input_v0` | Interaction | Renders interactive choice widgets | [4](#tool-card-4) |
| `message_compose_v1` | Interaction | Drafts messages with strategic variants | [5](#tool-card-5) |
| `end_conversation` | System | Permanently ends the conversation | [28](#tool-card-28) |
| `bash_tool` | Computer Use | Executes shell commands in sandbox | — |
| `view` | Computer Use | Reads files, directories, images | — |
| `create_file` | Computer Use | Creates new files with content | — |
| `str_replace` | Computer Use | Edits files (unique string replacement) | — |
| `present_files` | Computer Use | Shares files with user for download | — |
| `tool_search` | Meta | Discovers deferred tools by keyword | [25](#tool-card-25) |

### Non-Tool-Layer Capabilities (v1.4)

| Capability | Category | What It Does | Card |
| --- | --- | --- | --- |
| `visualize:show_widget` | Visualization | Inline Chart.js widgets (desktop/browser) | [34](#tool-card-34) |
| `gmail_create_draft` | Email | Creates Gmail drafts via MCP connector | [35](#tool-card-35) |
| `anthropic_api_in_artifacts` | Artifact | Claude API calls from within artifacts | [36](#tool-card-36) |
| `persistent_storage` | Artifact | Persistent key-value storage in published artifacts | [37](#tool-card-37) |

---

## Chapter 12 — What Comes Next

The tool ecosystem documented in this book is a snapshot of March 2026. It will change. Understanding the trajectory helps predict what's coming and where the opportunities lie.

### The MCP Convergence

Anthropic's Model Context Protocol (MCP) is an open standard for connecting AI tools to external services. Many of the internal tools documented here — calendar, alarms, Google Drive — could theoretically be implemented as MCP servers. The strategic question is whether Anthropic will eventually expose these internal tools as MCP servers (enabling third-party access) or maintain the current separation between consumer tools and developer APIs.

The platform architecture findings add nuance to this question. On the desktop app, `tool_search` already discovers MCP tools (Chrome, Filesystem) rather than consumer deferred tools — suggesting that MCP and internal tools already coexist in the same discovery mechanism, just on different platforms. On the browser, `tool_search` now appears conditionally when MCP connectors are active. This may be a transitional state or a deliberate architectural separation.

For developers, the practical implication is clear: understanding these internal tools gives you a blueprint for what to build with MCP. If Claude.ai can render inline charts, set alarms, and display interactive maps, your MCP-powered applications can do the same — you just need to build the tools yourself.

### Building on Top of Hidden Tools

The most immediate opportunity is using these tools more effectively in Claude.ai itself. Power users who understand `ask_user_input_v0` can structure their conversations for faster, more precise results. Understanding `chart_display_v0` means knowing when to ask for an inline chart vs. an artifact. Knowing about `tool_search` means you can discover capabilities that even Claude doesn't use by default.

The deeper opportunity is reading the signals. These tools reveal Anthropic's product strategy: investment in mobile-native device integration (alarms, timers), dependency on Google's ecosystem (Places, Drive, Calendar, Weather), incremental versioning rather than breaking changes (v0/v1 coexistence), rich interactive widgets (weather, recipes, maps) that push beyond text-only interfaces, and a clear trajectory toward Claude as a full operating system layer — scheduling, file management, communication, and now memory. Every tool in this book is a data point in that trajectory.

### Ghost Tools

Two tools have been discovered in schema definitions but are **not yet deployed** as of March 15, 2026:

- **`gmail_send_draft`** — Would complete the Gmail write pipeline (create → send). Currently, `gmail_create_draft` creates drafts that must be sent manually from Gmail.
- **`gmail_modify_thread`** — Would enable thread-level operations (labels, archive, etc.).

These "ghost tools" suggest Anthropic's roadmap includes deeper Gmail integration. Monitor for deployment.

### The Artifact Layer as an Emerging Platform

v1.4 reveals that artifacts are evolving beyond display surfaces into a full execution platform. With `anthropic_api_in_artifacts` (Claude-in-Claude), `persistent_storage` (cross-session persistent data for published artifacts), and MCP endpoint access (Calendar, Gmail, and custom servers), React artifacts can now function as lightweight applications. As of April 2026, Anthropic has officially documented all three capabilities. This represents a potential convergence point: rather than adding more consumer tools, Anthropic may invest in making the artifact layer powerful enough to replace them.

### Open Questions

Several questions remain unanswered and worth watching: Will MCP eventually replace internal tools, or will they coexist? Will `google_drive_fetch` expand to support Sheets and Slides? Will the iOS-exclusive Reminders tools expand to Android and web? Will `gmail_send_draft` and `gmail_modify_thread` deploy? How will `tool_search` evolve as the toolkit grows — will it remain keyword-based or gain semantic matching? Will the platform fragmentation documented in this edition converge toward a unified tool set, or will each platform continue to diverge? Will the artifact execution layer expand to mobile? The companion repository will track these as they develop.

---

## Appendix A — Availability Matrix

| Tool | Browser | Mobile (Android) | Mobile (iOS) | Desktop | API | Claude Code |
| --- | --- | --- | --- | --- | --- | --- |
| `user_time_v0` | ❌ | ✅ (always-loaded) | ✅ (always-loaded) | ❌ | ❌ | ❌ |
| `user_location_v0` | ❌ | ✅ (always-loaded) | ✅ (always-loaded) | ❌ | ❌ | ❌ |
| `chart_display_v0` | ❌ | ✅ (deferred, ⚠️ intermittent instability) | ✅ (deferred, ⚠️ intermittent instability) | ❌ | ❌ | ❌ |
| `ask_user_input_v0` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| `message_compose_v1` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| `alarm_create_v0` | ❌ | ✅ (deferred, native) | ✅ (deferred, native) | ❌ | ❌ | ❌ |
| `timer_create_v0` | ❌ | ✅ (deferred, native) | ✅ (deferred, native) | ❌ | ❌ | ❌ |
| Calendar suite (6) | ❌ | ✅ (deferred, needs connection) | ✅ (deferred, needs connection) | ❌ | ❌ | ❌ |
| iOS Reminders suite (5) | ❌ | ❌ | ✅ (deferred, iOS only) | ❌ | ❌ | ❌ |
| `web_search` | ✅ | ✅ | ✅ | ✅ | ✅* | ❌ |
| `web_fetch` | ✅ | ✅ | ✅ | ✅ | ✅* | ❌ |
| `image_search` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| `places_search` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| `places_map_display_v0` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| `fetch_sports_data` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Google Drive (2) | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| `weather_fetch` | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| `recipe_display_v0` | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| `end_conversation` | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Memory tools (3) | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Computer Use (5) | ✅ | ❌ | ❌ | ✅ | ❌ | Different |
| `tool_search` | ✅ (MCP only, conditional) | ✅ (consumer) | ✅ (consumer) | ✅ (MCP only) | ❌ | ❌ |
| `visualize:show_widget` | ✅ | ❌ | ❌ | ✅ | ❌ | ❌ |
| `gmail_create_draft` | ✅ (MCP) | ✅ (MCP) | ✅ (MCP) | ✅ (MCP) | ❌ | ❌ |
| `anthropic_api_in_artifacts` | ✅ (React only) | ❌ | ❌ | ✅ (React only) | ❌ | ❌ |
| `persistent_storage` | ✅ (React only) | ❌ | ❌ | ✅ (React only) | ❌ | ❌ |

\* `web_search` exists in the API as `web_search_20250305` with different configuration. `web_fetch` is available server-side in the API. All other tools are Claude.ai-exclusive.

---

## Appendix B — Discovery Methodology

This appendix documents the exact methodology used to discover and catalog every tool in this book. The approach is reproducible — anyone with a Claude.ai account can verify these findings.

### Step 1: Broad Keyword Sweeps (Mobile App)

Using `tool_search` on the **Claude mobile app** with `limit=20`, the following queries were executed systematically: "user", "create display generate", "search fetch data memory notification", "audio image video file convert", "send message email slack reminder", "map place weather stock price translate". Each query's results were recorded, including tool names, parameter schemas, and descriptions.

**Platform note:** This step is only reproducible on the Claude mobile app. On the browser, `tool_search` only discovers MCP tools (when connectors are active). On the desktop app, it discovers MCP tools instead.

### Step 2: Schema Extraction

For each discovered tool, the full parameter schema was captured from `tool_search` results. This includes parameter names, types, required/optional status, and descriptions. For always-loaded tools (not discoverable via `tool_search`), schemas were extracted from Claude's system-level tool definitions.

### Step 3: Behavioral Testing

Each tool was called with representative inputs to document response formats, error handling, and edge cases. Testing included: normal usage, boundary conditions (empty inputs, maximum values), and cross-platform comparison across three surfaces (browser, desktop app, mobile app). Automated tests covered 38 scenarios; manual tests covered 34 scenarios across platforms.

### Step 4: Cross-Platform Verification (v1.3)

New in this edition: systematic testing across browser, desktop app, and mobile app to document platform-specific behavior. This revealed the platform architecture documented in Chapter 2, including the critical finding that `tool_search` behaves differently on each platform and that many tools are platform-exclusive.

### Step 5: Cross-Reference with Public Sources

Every tool name was searched across GitHub, Reddit, LinkedIn, Twitter/X, YouTube, and the broader web to determine documentation status. Anthropic's official docs (docs.anthropic.com, support.claude.com) were checked for each tool. The gap analysis confirmed that the vast majority of tools had zero public documentation by their internal names.

### Step 6: Peer Validation

Findings were compared against the small body of existing research: Shlok Khemani's memory tool schemas, Simon Willison's system prompt analysis, Adversa AI's security research, and Federico Viticci's iOS tool discovery. Consistent findings across independent researchers strengthen confidence in the documented schemas.

---

## Appendix C — Tool Template for New Discoveries

Use this template when documenting newly discovered tools. Consistent structure makes the catalog navigable and comparable.

```markdown
# `tool_name`

> Category: [category]
> Availability: Claude.ai only / All platforms
> Platform: Browser / Desktop / Mobile / All
> Documentation status: Undocumented / Partial / Official

## Overview
[One paragraph description]

## Quick Facts
[Table: name, version, category, params, availability, platform]

## Parameters
[Table: name, type, required, default, description]

## Response Format
[JSON example + schema table]

## When Claude Uses It
[Table: scenario, example user message]

## Edge Cases & Gotchas
[List of discovered edge cases]

## Limitations
[List of known limitations]

## Platform Availability
[Table: platform, available, notes]

## Changelog
[Date, change]
```

---

## Appendix D — Settings & Connectors Reference

Claude.ai's settings control which tools are available and how they behave. Understanding these settings is essential for accessing the full toolkit.

### Settings Sidebar (Browser / Mobile)

| Section | What It Controls |
| --- | --- |
| General | Language, theme, interface preferences |
| Account | Email, password, account management |
| Privacy | Data handling, conversation history |
| Billing | Plan management, usage limits |
| Usage | Token and message usage statistics |
| Capabilities | Tool toggles (see below) |
| Connectors | External service connections (see below) |
| Claude Code | CLI tool configuration |
| Cowork | Desktop automation configuration |

### Desktop App Additional Settings

| Section | What It Controls |
| --- | --- |
| General | Desktop-specific preferences |
| Extensions | MCP server management |
| Developer | Debug and development options |

### Connectors

Connectors enable tools that depend on external services. Available connectors:

| Connector | Required By | Default State |
| --- | --- | --- |
| Google Drive | `google_drive_search`, `google_drive_fetch` | Available, must connect |
| GitHub | Code-related features | Available, must connect |
| Gmail | `message_compose_v1` "Send via Gmail" | Available, must connect |
| Google Calendar | Calendar suite (6 tools) | Available, must connect |
| Claude in Chrome | Desktop MCP browsing tools | Available, must connect |
| Filesystem | Desktop MCP file access tools | Desktop only, must connect |
| Add custom connector | User-defined MCP servers | N/A |

### Capabilities Toggles

| Toggle | What It Controls |
| --- | --- |
| Artifacts | Basic artifact rendering (HTML, React, SVG, etc.) |
| AI-powered artifacts | Claude-in-Claude API calls within artifacts |
| Cloud code execution and file creation | Computer use tools (`bash_tool`, `create_file`, etc.) |
| Allow network egress | Sandbox internet access |
| Domain allowlist | Which domains the sandbox can reach |
| Memory (search + generate) | `memory_user_edits`, auto-generation of memories |
| Search and reference chats | `conversation_search`, `recent_chats` |
| Skills | Custom skill files in Projects |

> **📸 IMAGE: `fig_21_settings_connectors.png`**
> Screenshot of the Settings → Connectors page showing available connections.
> *[Upcoming]*

> **📸 IMAGE: `fig_22_settings_capabilities.png`**
> Screenshots of the Settings → Capabilities toggles for Artifacts, Code execution, Memory, and Skills.
> *[Upcoming]*

---

# TECHNICAL ANNEX — Detailed Tool Cards

Each card includes: quick facts, overview, full parameter schema, a JSON call example, trigger scenarios, key gotchas, and platform availability. All response examples have been confirmed through empirical testing across three platforms. Tool Cards 1–28 from v1.3 have been updated where needed; Tool Cards 29–37 are new in v1.4.

---

### Tool Card 1

## `user_time_v0`

| Property | Detail |
| --- | --- |
| Tool | `user_time_v0` |
| Version | v0 |
| Category | Context |
| Params | 0 |
| Permission | No |
| Platform | Mobile App only (always-loaded) |
| Docs | None |
| Trigger phrases | "What time is it?" · "Current time in Tokyo?" · "Is it too late to call?" |

#### Overview

Returns current time in ISO 8601 with UTC offset. See Chapter 3. Mobile only — on browser and desktop, Claude states the system date but cannot access local time. **v1.4 correction:** This tool is always-loaded on mobile, not deferred as v1.3 stated.

#### Parameters

This tool accepts no parameters.

#### Example Call

```json
// Call (no parameters)
{}

// Response (Android — microseconds, colon in offset)
{
  "current_time": "2026-03-15T10:49:50.411154+01:00"
}

// Response (iOS — no microseconds, compact offset)
{
  "current_time": "2026-03-15T12:11:22+0100"
}
```

#### When Claude Uses It

| Scenario | Example |
| --- | --- |
| Time request | "What time is it?" |
| Scheduling | "Is it too late to call London?" |
| Timestamps | "Log this with timestamp" |
| Timezone math | "What time in Tokyo?" |

#### Watch Out

- VPN has no effect — timezone comes from device, not IP
- Each call returns a fresh timestamp — no caching
- **iOS vs Android:** Android includes microsecond precision and colon in offset (`+01:00`); iOS omits microseconds and uses compact offset (`+0100`)
- Prerequisite for calendar tools (they need ISO 8601 with correct offset)
- Browser/desktop fallback: Claude states system date but cannot return precise local time

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | States system date only |
| Claude mobile | ✅ | Device timezone, always-loaded |
| Claude desktop | ❌ | States system date only |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 2

## `user_location_v0`

| Property | Detail |
| --- | --- |
| Tool | `user_location_v0` |
| Version | v0 |
| Category | Context |
| Params | 1 |
| Permission | Yes — two-layer device permission |
| Platform | Mobile App only (always-loaded) |
| Docs | None |
| Trigger phrases | "Restaurants near me?" · "Weather here?" · "How far to the airport?" |

#### Overview

User geolocation. The only tool requiring explicit permission. Returns coordinates and a reverse-geocoded address. Mobile only — on browser and desktop, this tool is absent and Claude asks for location manually. **v1.4 correction:** This tool is always-loaded on mobile, not deferred as v1.3 stated. See Chapter 3.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `accuracy` | string | Yes | `"low"` (city-level) or `"high"` (GPS-level) — in testing, both returned nearly identical results |

#### Example Call

```json
// Call
{"accuracy": "high"}

// Response (confirmed)
{
  "status": "success",
  "latitude": 45.4707004,
  "longitude": 9.1248245,
  "geocoded": {
    "address": "Via Example 15",
    "administrative_area": "Lombardia",
    "country": "Italia",
    "locality": "Milano",
    "name": "15",
    "postal_code": "20100"
  }
}
```

#### When Claude Uses It

| Scenario | Example |
| --- | --- |
| Nearby search | "Restaurants near me?" |
| Weather | "Weather here?" |
| Distance | "How far to the airport?" |

#### Watch Out

- **Two permission layers on mobile:** (1) Claude app: "Allow once / Allow always / Cancel" (localized), (2) Android OS: Exact vs. Approximate location
- Result widget: pin icon, full street address, Google Maps thumbnail
- Browser/desktop: tool absent — Claude asks user for city/area manually
- **`accuracy` parameter has no observable effect** — "high" and "low" returned nearly identical coordinates in testing
- **iOS vs Android:** iOS adds `sub_locality` and `accuracy` (meters) fields to the response that Android lacks
- **Implicit trigger unreliable** — "Where am I?" may not be sufficient; explicit invocation required
- **Response includes reverse-geocoded address** with street, city, region, country, postal code

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Absent — asks manually |
| Claude mobile | ✅ | GPS, always-loaded |
| Claude desktop | ❌ | Absent — asks manually |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 3

## `chart_display_v0`

| Property | Detail |
| --- | --- |
| Tool | `chart_display_v0` |
| Version | v0 |
| Category | Visualization |
| Params | 5 |
| Permission | No |
| Platform | Mobile App only (deferred) — ⚠️ intermittent instability as of Mar 2026 |
| Docs | None |
| Trigger phrases | "Plot my monthly expenses" · "Show a bar chart of..." · "Graph these numbers" |

#### Overview

Inline interactive charts: line, bar, scatter. Mobile only (deferred) with **intermittent instability** — crashes are state-dependent, not deterministic (v1.3 reported 100% crash rate; v1.4 testing showed intermittent behavior). On browser and desktop, Claude creates a React artifact with Recharts instead, or uses `visualize:show_widget` for inline charts. See Chapter 5.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `series` | array | Yes | Data series to plot |
| `style` | string | Yes | `"line"`, `"bar"`, `"scatter"` |
| `title` | string | No | Chart title |
| `x_axis` | object | No | X-axis configuration |
| `y_axis` | object | No | Y-axis configuration |

#### Example Call

```json
{
  "series": [
    {"name": "Revenue", "data": [100, 150, 130, 180, 210]},
    {"name": "Costs", "data": [80, 90, 95, 110, 120]}
  ],
  "style": "line",
  "title": "Q1-Q5 Performance",
  "x_axis": {"label": "Quarter"},
  "y_axis": {"label": "USD (thousands)"}
}
```

#### Watch Out

- **⚠️ Intermittent instability** — crashes are state-dependent, not deterministic (updated Mar 2026)
- **Non-functional parameters:** `format`, `min`, `max` are accepted but have no effect
- **Functional parameters:** `color` (hex — e.g., `"#FF5733"`), `scale: "log"` (logarithmic)
- **`values` type:** iOS accepts integers, not just strings as v1.3 documented
- No SVG/PNG export — use a code artifact if user needs to save
- Limited chart types (line, bar, scatter) — for pie, histogram, heatmap, use `visualize:show_widget` (desktop/browser)
- Browser/desktop fallback: React artifact with Recharts or `visualize:show_widget`

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Fallback: React artifact or `visualize:show_widget` |
| Claude mobile | ✅ (⚠️ intermittent) | Deferred, intermittent instability |
| Claude desktop | ❌ | Fallback: React artifact or `visualize:show_widget` |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 4

## `ask_user_input_v0`

| Property | Detail |
| --- | --- |
| Tool | `ask_user_input_v0` |
| Version | v0 |
| Category | Interaction |
| Params | 1 (complex) |
| Permission | No |
| Platform | All |
| Docs | None |
| Trigger phrases | "Help me choose between..." · "Which do you prefer?" · "Rank these options" |

#### Overview

Interactive choice widgets: `single_select`, `multi_select`, `rank_priorities`. 1–3 questions, 2–4 options each. See Chapter 4.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `questions` | array | Yes | 1–3 question objects, each with `question`, `type`, and `options` |

#### Example Call

```json
{
  "questions": [
    {
      "question": "What kind of analysis do you need?",
      "type": "single_select",
      "options": ["Quick summary", "Deep analysis", "Comparison", "Recommendations"]
    },
    {
      "question": "Which areas interest you?",
      "type": "multi_select",
      "options": ["Technical", "Business", "Legal", "Financial"]
    }
  ]
}
```

#### Watch Out

- Max 3 questions × 4 options — Claude must compress if more are needed
- Graceful degradation: if user ignores widget and types freely, Claude handles it
- **"Something else" free-text option** auto-added to ALL widget types (not in schema)
- **"Skip" button** present on all types
- **Keyboard shortcuts:** ↑↓ navigate, Enter select, ⌘Enter submit, Esc skip
- **Selection counter** on multi_select ("0 selected")
- **Drag handles** (⠿) on rank_priorities with "Drag to re-order"
- **Pagination** "N of 3" with < > navigation for multiple questions
- Claude personalizes options using stored memory

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Keyboard shortcuts visible |
| Claude mobile | ✅ | Touch-optimized |
| Claude desktop | ✅ | Keyboard shortcuts visible |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 5

## `message_compose_v1`

| Property | Detail |
| --- | --- |
| Tool | `message_compose_v1` |
| Version | v1 |
| Category | Interaction |
| Params | 3 |
| Permission | No |
| Platform | All |
| Docs | Named by Adversa AI |
| Trigger phrases | "Write an email to..." · "Draft a text to..." · "Help me reply to this message" |

#### Overview

Message drafting with native app integration and strategic variants. See Chapter 4.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `kind` | string | Yes | `"email"`, `"textMessage"`, or `"other"` |
| `variants` | array | Yes | Message drafts, each with `label` and `body` (and `subject` for email) |
| `summary_title` | string | Yes | Title shown in share sheet |

#### Example Call

```json
{
  "kind": "email",
  "summary_title": "Declining the meeting",
  "variants": [
    {
      "label": "Polite decline",
      "subject": "Re: Strategy Meeting Thursday",
      "body": "Hi Alex, thanks for the invite. Unfortunately I have a conflict on Thursday. Happy to review any notes afterward."
    },
    {
      "label": "Suggest alternative",
      "subject": "Re: Strategy Meeting Thursday",
      "body": "Hi Alex, Thursday doesn't work for me — would Friday morning work instead?"
    }
  ]
}
```

#### Watch Out

- Strategic variants: 2–3 fundamentally different approaches, not just tonal shifts
- **Primary button is "Send via Gmail"** (with Gmail icon), not "Open in Mail"
- **Dropdown chevron** reveals: "Send via Gmail" + "Open in Mail"
- **No recipient field** in schema — recipients added manually in mail client
- Subject + body transfer correctly to both Gmail and Outlook
- Claude auto-signs with user's name from memory
- **Copy icon** present on all message cards

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Send via Gmail + Open in Mail |
| Claude mobile | ✅ | Native app integration |
| Claude desktop | ✅ | Send via Gmail + Open in Mail |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 6

## `alarm_create_v0`

| Property | Detail |
| --- | --- |
| Tool | `alarm_create_v0` |
| Version | v0 |
| Category | Scheduling |
| Params | 5 |
| Permission | Likely |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "Wake me at 7 AM" · "Set an alarm for 3 PM" · "Weekday alarm at 6:30" |

#### Overview

Creates alarms at a specific clock time. Supports recurring alarms via weekday array. Mobile-native — creates actual device alarms through OS integration. Completely absent on browser and desktop.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `hour` | integer | Yes | 0–23 |
| `minute` | integer | Yes | 0–59 |
| `message` | string | Yes | Alarm label |
| `days` | array | No | Recurring weekdays (e.g., `["monday", "friday"]`) |
| `vibrate` | boolean | No | Enable vibration |

#### Example Call

```json
{
  "hour": 7,
  "minute": 0,
  "message": "Wake up for gym",
  "days": ["monday", "wednesday", "friday"],
  "vibrate": true
}
```

#### Watch Out

- Creates **actual device-level alarms** — fires even if Claude app is closed
- Widget: bell icon, time, label, "View alarms" link (localized — "Vedi sveglie" in Italian)
- **Response string:** Returns "Alarm was set on Android device." (or equivalent for iOS) — confirms the device platform
- Confirmation: "This alarm was set on your device" (localized)
- Adapts to device language
- **Browser/desktop: tool completely absent** — Claude suggests alternatives

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Native device alarm, deferred |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 7

## `timer_create_v0`

| Property | Detail |
| --- | --- |
| Tool | `timer_create_v0` |
| Version | v0 |
| Category | Scheduling |
| Params | 2 |
| Permission | Likely |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "Set a 5-minute timer" · "Timer for 12 minutes for pasta" · "25-minute focus timer" |

#### Overview

Countdown timers from a duration in seconds. Claude converts natural language automatically. Mobile-native — creates actual device timers. Completely absent on browser and desktop.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `duration_seconds` | integer | Yes | Duration in seconds |
| `message` | string | Yes | Timer label |

#### Example Call

```json
{
  "duration_seconds": 720,
  "message": "Pasta is ready"
}
```

#### Watch Out

- Input is always seconds — Claude handles conversion from natural language
- No recurring support (unlike alarms)
- Widget: clock icon, duration, "View timers" link (localized — "Vedi Timer")
- **Response string:** Returns "Timer was set on Android device." (or equivalent for iOS)
- Creates actual device timer — works even if Claude app is closed
- **Browser/desktop: tool completely absent**

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Native device timer, deferred |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 8

## `calendar_search_v0`

| Property | Detail |
| --- | --- |
| Tool | `calendar_search_v0` |
| Version | v0 |
| Category | Calendar |
| Params | 0 |
| Permission | Calendar connection required |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | Called internally — triggered when you ask about calendar events |

#### Overview

Lists available calendars and their IDs. First call in any calendar workflow.

#### Parameters

This tool accepts no parameters.

#### Example Call

```json
// Call
{}

// Response (confirmed)
{
  "status": "success",
  "calendars": [
    {
      "access_level": "owner",
      "account_name": "user@gmail.com",
      "color": "#B99AFF",
      "id": "12",
      "is_primary": true,
      "name": "user@gmail.com",
      "owner_account": "user@gmail.com"
    },
    {
      "access_level": "readonly",
      "account_name": "user@gmail.com",
      "color": "#7BD148",
      "id": "10",
      "name": "Holidays in Italy",
      "owner_account": "it.italian#holiday@group.v.calendar.google.com"
    }
  ]
}
```

**Response fields per calendar:** `id` (numeric string), `name` (display name), `account_name` (parent Google account email), `owner_account` (calendar owner — email for personal, group URI for shared), `access_level` ("owner"/"readwrite"/"readonly"), `color` (hex string, optional), `is_primary` (boolean, only on primary calendars for each account).

#### Watch Out

- Requires Google Calendar connection in Settings → Connectors (not connected by default)
- Returns **numeric string IDs** (e.g., "12", "21") used by `event_search_v0`, `event_create_v0`, etc. — these are NOT the email-based IDs from the Google Calendar API
- `is_primary` only appears on primary calendars — absent (not `false`) on non-primary calendars
- Multiple accounts supported: calendars from all connected Google accounts appear in one response
- **Browser/desktop:** Tool absent — Claude responds "I can only search Google Drive documents, not Google Calendar"

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Deferred, needs calendar connection |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 9

## `event_search_v0`

| Property | Detail |
| --- | --- |
| Tool | `event_search_v0` |
| Version | v0 |
| Category | Calendar |
| Params | 5 |
| Permission | Calendar connection required |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "What's on my calendar today?" · "Any meetings tomorrow?" · "Show my schedule" |

#### Overview

Searches calendar events within a time range. Returns event IDs needed for update/delete operations.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `start_time` | string | No | ISO 8601 start of range |
| `end_time` | string | No | ISO 8601 end of range |
| `calendar_id` | string | No | Target calendar (from `calendar_search_v0`) |
| `include_all_day` | boolean | No | Include all-day events |
| `limit` | integer | No | Maximum results |

#### Example Call

```json
// Call
{
  "start_time": "2026-02-15T00:00:00+01:00",
  "end_time": "2026-02-15T23:59:59+01:00",
  "include_all_day": true
}

// Response (confirmed — grouped by calendar)
{
  "status": "success",
  "calendar_events": [
    {
      "calendar_id": "12",
      "calendar_name": "user@gmail.com",
      "events": [
        {
          "event_id": "1256",
          "title": "Schema Test",
          "start_time": "2026-02-16T11:00+01:00",
          "end_time": "2026-02-16T11:30+01:00",
          "status": "confirmed",
          "organizer": "user@gmail.com",
          "event_description": "",
          "availability": "busy",
          "location": "",
          "nudges": [{"method": "email", "minutes_before": 20}]
        }
      ]
    }
  ]
}
```

**Response fields per event:** `event_id`, `calendar_id`, `calendar_name`, `title`, `start_time`, `end_time`, `status` ("confirmed" in most cases; "tentative" observed in some v1-created events), `organizer`, `event_description`, `availability` ("busy"/"free"), `location`, `nudges` (reminders array — conditional, appears on events with reminders set, not on imported/holiday events), plus conditional fields: `all_day` (boolean, all-day events only), `attendees` (array of emails, multi-person events only), `recurrence` (object, recurring events only).

#### Watch Out

- Claude typically calls `user_time_v0` first to get correct timezone offset
- Returns both `event_id` and `calendar_id` per event — both are needed for `event_update_v0` and `event_delete_v0`
- IDs are numeric strings (e.g., event_id `"1250"`, calendar_id `"12"`)
- Requires Google Calendar connection in Settings → Connectors

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Deferred, needs calendar connection |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 10

## `event_create_v0`

| Property | Detail |
| --- | --- |
| Tool | `event_create_v0` |
| Version | v0 |
| Category | Calendar |
| Params | 7 |
| Permission | Calendar connection required |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "Schedule a meeting for tomorrow at 2 PM" · "Block my calendar" · "Create an event" |

#### Overview

Creates a calendar event **draft** (UI template) for user confirmation. Unlike `event_create_v1` which creates events directly on the server, v0 presents a confirmation interface — the event is not committed until the user approves it in the UI. Returns only a string message, not event IDs.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | Yes | Event title |
| `start_time` | string | Yes | ISO 8601 |
| `end_time` | string | No | ISO 8601 |
| `all_day` | boolean | No | All-day event |
| `description` | string | No | Event description |
| `location` | string | No | Location text |
| `recurrence` | object | No | Recurrence rules (see note below) |

> **Recurrence:** The `recurrence` parameter works via `event_create_v0` using the three-field format: `{"frequency": "weekly", "human_readable_frequency": "weekly", "rrule": "FREQ=WEEKLY;WKST=MO;BYDAY=MO"}`. All three fields are required — subsets are rejected. Simple formats like `{"frequency": "weekly", "days_of_week": ["monday"]}` and bare RRULE strings are also rejected. The three-field format mirrors what `event_search_v0` returns for existing recurring events.
>
> **Confirmed RRULE variants tested via `event_create_v0`:**
> - `FREQ=WEEKLY;COUNT=3;WKST=MO;BYDAY=SU` — weekly on Sundays, 3 instances (confirmed: recurrence object persisted correctly)
> - `FREQ=MONTHLY;COUNT=3;WKST=MO;BYMONTHDAY=1` — monthly on the 1st, 3 instances (confirmed: instances appeared on Mar 1, Apr 1, May 1 with same event_id)
> - `FREQ=YEARLY;COUNT=2;WKST=MO` — yearly, 2 instances (confirmed: recurrence object persisted correctly)
>
> **Additional RRULE variants confirmed in existing calendar data:** `FREQ=MONTHLY;UNTIL=20270224;WKST=MO` (monthly with end date), `FREQ=MONTHLY;COUNT=52;WKST=MO` (monthly with count), `FREQ=MONTHLY;WKST=MO;BYMONTHDAY=25` (monthly on specific day), `FREQ=YEARLY;WKST=MO` (yearly), `FREQ=DAILY;COUNT=5;WKST=MO` (daily with count).
>
> `event_create_v1` does **not** have a `recurrence` parameter — recurring events can only be created via v0.

#### Example Call

```json
// Call
{
  "title": "Team standup",
  "start_time": "2026-02-16T09:00:00+01:00",
  "end_time": "2026-02-16T09:30:00+01:00",
  "description": "Daily sync",
  "location": "Zoom"
}

// Response (confirmed — v0 is a draft tool)
"Event creation template has been created."
// Note: NO event IDs returned. Event requires user confirmation in UI.
// Use event_create_v1 for server-side creation with returned IDs.
```

#### Watch Out

- **v0 is a DRAFT tool** — creates a UI template for user confirmation, not a server-side event. The event does NOT appear in `event_search_v0` until the user confirms it in the mobile UI. Once confirmed, the event is created with status **"confirmed"** and the `description` field persists correctly
- **No IDs returned** — v0 returns only a string message. Use `event_create_v1` for programmatic workflows where you need event IDs returned immediately
- **Description persists on v0** — unlike v1, the description passed to v0 appears correctly in `event_search_v0` after user confirmation
- **Location persists correctly**
- **Default calendar selection:** Without explicit `calendar_id`, may create on a secondary account rather than primary
- Claude typically calls `user_time_v0` first for correct timezone offset
- Requires Google Calendar connection in Settings → Connectors

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Deferred, needs calendar connection |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 11

## `event_create_v1`

| Property | Detail |
| --- | --- |
| Tool | `event_create_v1` |
| Version | v1 |
| Category | Calendar |
| Params | 1 (array) |
| Permission | Calendar connection required |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "Block my mornings for the next 3 days" · "Schedule meetings Mon/Wed/Fri" |

#### Overview

Batch event creation — multiple events in one call. Unlike `event_create_v0` (which creates a draft requiring user confirmation), **v1 creates events directly on the server** and returns structured IDs. This is the preferred tool for programmatic calendar operations. Events created via v1 have shown both **"confirmed"** and **"tentative"** status in different sessions — the status may depend on calendar provider or account configuration. Does **not** support the `recurrence` parameter. **Known bug:** the `description` field does not persist on v1-created events — `event_search_v0` returns it as empty. If description persistence matters, use v0 instead. **Default calendar:** v1 may default to a secondary account rather than the primary calendar — specify `calendar_id` explicitly when creating events across multiple accounts.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `new_events` | array | Yes | Array of event objects (same schema as `event_create_v0`) |

#### Example Call

```json
{
  "new_events": [
    {"title": "Deep work", "start_time": "2026-02-16T09:00:00+01:00", "end_time": "2026-02-16T10:00:00+01:00"},
    {"title": "Deep work", "start_time": "2026-02-17T09:00:00+01:00", "end_time": "2026-02-17T10:00:00+01:00"}
  ]
}
```

#### Watch Out

- Use this (not v0) when creating multiple events
- Each event in the array follows the same schema as `event_create_v0`

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Deferred, needs calendar connection |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 12

## `event_update_v0`

| Property | Detail |
| --- | --- |
| Tool | `event_update_v0` |
| Version | v0 |
| Category | Calendar |
| Params | 1 (array) |
| Permission | Calendar connection required |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "Move my 2 PM meeting to 3 PM" · "Change the meeting title" |

#### Overview

Updates existing calendar events by ID. Follows the search-then-act pattern.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `event_updates` | array | Yes | Array of `{event_id, calendar_id, ...changed_fields}` |

#### Example Call

```json
// Call
{
  "event_updates": [
    {
      "event_id": "1256",
      "calendar_id": "12",
      "start_time": "2026-02-16T11:30:00+01:00",
      "end_time": "2026-02-16T12:00:00+01:00",
      "location": "Milan office"
    }
  ]
}

// Response (confirmed)
{
  "status": "success",
  "update_results": [
    {
      "calendar_id": "12",
      "event_id": "1256"
    }
  ]
}
```

#### Watch Out

- Requires `event_id` **and** `calendar_id` from `event_search_v0` — always search first, then update
- IDs are numeric strings (e.g., `"1250"`, `"12"`), not UUIDs
- Can update multiple events in one call
- **Can add attendees and nudges** — even though `event_create` doesn't support these, `event_update_v0` can add `attendees` (email array) and `nudges` (reminder array) to existing events
- **⚠️ Recurring events — `start_time` severs the chain:** Updating WITHOUT `start_time` safely applies changes to all instances. Updating WITH `start_time` (targeting one occurrence) **destroys the recurrence** — the targeted instance gets the update but all other instances permanently disappear. Never include `start_time` when updating a recurring event unless you intend to destroy the series

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Deferred, needs calendar connection |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 13

## `event_delete_v0`

| Property | Detail |
| --- | --- |
| Tool | `event_delete_v0` |
| Version | v0 |
| Category | Calendar |
| Params | 1 (array) |
| Permission | Calendar connection required |
| Platform | Mobile App only (deferred via `tool_search`) |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "Cancel my 3 PM meeting" · "Delete the event" |

#### Overview

Deletes calendar events by ID. Typically used after `event_search_v0`.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `removed_events` | array | Yes | Array of event objects with `event_id` and `calendar_id` |
| `removed_events[].recurrence_span` | object | No | For recurring events: `{"option": "instance", "start_time": "ISO8601"}` to delete single instance *(untested)* |

#### Example Call

```json
// Call
{
  "removed_events": [
    {"event_id": "1256", "calendar_id": "12"}
  ]
}

// Response (confirmed)
{
  "status": "success",
  "delete_results": [
    {
      "calendar_id": "12",
      "event_id": "1256"
    }
  ]
}
```

#### Watch Out

- Destructive operation — Claude should confirm before deleting
- **⚠️ Recurring events:** Deleting a recurring event removes **all instances** (past and future), not just a single occurrence. The schema includes a `recurrence_span` parameter with an `"instance"` option and a `start_time` field for deleting a single occurrence, but this has not been tested
- Follows the search-then-act pattern: `event_search_v0` → `event_delete_v0`
- Requires both `event_id` **and** `calendar_id` — get both from `event_search_v0`
- IDs are numeric strings, not UUIDs
- **Batch limit:** Maximum 2 events per delete call — calls with 3+ events are rejected with schema validation error
- **Not-found error format:** `{"error_type": "not_found", "message": "Event not found or already deleted"}` — returned per-event within the `delete_results` array
- **Duplicate risk:** If batch creation (`event_create_v1`) and individual creation (`event_create_v0`) run in the same session for similar events, duplicates can occur. Delete carefully by ID.

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile | ✅ | Deferred, needs calendar connection |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 14

## `web_search`

| Property | Detail |
| --- | --- |
| Tool | `web_search` |
| Version | Unversioned |
| Category | Search |
| Params | 1 |
| Permission | No |
| Platform | All |
| Docs | API version: `web_search_20250305` |
| Trigger phrases | Automatic for current events · "Search for..." · "Latest news about..." |

#### Overview

Web search returning top 10 results with snippets. The most frequently used tool. Short queries (1–6 words) perform best.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `query` | string | Yes | Search query |

#### Example Call

```json
{"query": "Serie A standings 2026"}
```

#### Watch Out

- Short queries work best (1–6 words)
- No `site:` operator or advanced syntax by default
- Returns snippets only — use `web_fetch` for full articles
- API version (`web_search_20250305`) has different configuration

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ✅* | As `web_search_20250305` |
| Claude Code | ❌ | |

---

### Tool Card 15

## `web_fetch`

| Property | Detail |
| --- | --- |
| Tool | `web_fetch` |
| Version | Unversioned |
| Category | Search |
| Params | 1 required + 5 optional (+ 3 internal) — 9 total |
| Permission | No |
| Platform | All |
| Docs | API version documented |
| Trigger phrases | Called after web_search · "Read that article" · "What does that page say?" |

#### Overview

Fetches full webpage content by URL. Used after `web_search` or with user-provided URLs. v1.4 adds the `html_extraction_method` parameter (v1.3 documented 8 params; correct count is 9).

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `url` | string | Yes | Full URL with `https://` |
| `text_content_token_limit` | integer | No | Truncate text to approximately this many tokens |
| `web_fetch_pdf_extract_text` | boolean | No | If true, extract text from PDFs instead of raw bytes |
| `allowed_domains` | array | No | Only fetch URLs from these domains |
| `blocked_domains` | array | No | Never fetch URLs from these domains |
| `html_extraction_method` | string | No | `"markdown"` or `"traf"` — controls how HTML is converted to text |

*Additional internal parameters exist (`is_zdr`, `web_fetch_rate_limit_dark_launch`, `web_fetch_rate_limit_key`) but are operational and not user-relevant.*

#### Example Call

```json
{"url": "https://www.anthropic.com/news/claude-model-spec"}
```

#### Watch Out

- Can only fetch URLs from `web_search` results or user-provided URLs — arbitrary access restricted
- Paywalled pages: silent truncation (page shell only, no article body)
- Auth-required pages (e.g., login walls): return structured 403 CLIENT_ERROR JSON rather than page content
- JavaScript-heavy sites may return minimal content
- URLs must include scheme (`https://`)
- Rate-limited on repeated fetches
- **⚠️ `html_extraction_method`: use `markdown`, not `traf`** — `traf` can fail completely with "Internal fetch error" on complex sites. `markdown` is significantly more robust. This is not just a style preference — it is a functional reliability difference

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ✅* | Server-side |
| Claude Code | ❌ | |

---

### Tool Card 16

## `image_search`

| Property | Detail |
| --- | --- |
| Tool | `image_search` |
| Version | Unversioned |
| Category | Search |
| Params | 2 |
| Permission | No |
| Platform | All |
| Docs | None |
| Trigger phrases | "Show me pictures of..." · "What does X look like?" |

#### Overview

Web image search, returns images inline. Min 3 (enforced), max 5 (may return 4) results per call. Dimensions are mentioned in the tool schema but were not surfaced in testing.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `query` | string | Yes | 3–6 word descriptive query |
| `max_results` | integer | No | 3–5 (default 3) |

#### Example Call

```json
{"query": "capybara in hot spring", "max_results": 4}
```

#### Watch Out

- Minimum 3 results, maximum 5 — can't request just 1 or 2
- Content safety: no copyrighted characters, no celebrity photos, no graphic content
- 3–6 word queries work best

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 17

## `places_search`

| Property | Detail |
| --- | --- |
| Tool | `places_search` |
| Version | Unversioned |
| Category | Search |
| Params | 4 |
| Permission | No |
| Platform | All |
| Docs | Feature known; internal name unknown publicly |
| Trigger phrases | "Best pizza near me" · "Coffee shops in Navigli" |

#### Overview

Google Places search. Up to 10 simultaneous queries, deduplicated results.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `queries` | array | Yes | 1–10 search queries |
| `location_bias_lat` | number | No | Latitude for location bias |
| `location_bias_lng` | number | No | Longitude for location bias |
| `location_bias_radius` | number | No | Radius in meters |

#### Example Call

```json
{
  "queries": [
    {"query": "best pizza Milan", "max_results": 3},
    {"query": "coffee shops Navigli Milan", "max_results": 3}
  ],
  "location_bias_lat": 45.4642,
  "location_bias_lng": 9.1900
}
```

#### Watch Out

- Up to 10 queries in a single call
- Results deduplicated across queries
- Location bias from `user_location_v0` feeds into `location_bias_lat`/`lng`

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 18

## `places_map_display_v0`

| Property | Detail |
| --- | --- |
| Tool | `places_map_display_v0` |
| Version | v0 |
| Category | Search |
| Params | 7 |
| Permission | No |
| Platform | All |
| Docs | None |
| Trigger phrases | "Show me on a map" · "Plan a walking tour" · "Map these locations" |

#### Overview

Interactive inline maps. Two modes: simple markers or full itinerary with multi-day routing. See Chapter 7.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `locations` | array | Mode A | Simple marker list |
| `days` | array | Mode B | Itinerary with day structure |
| `mode` | string | No | `"markers"` or `"itinerary"` — auto-inferred from `locations` vs `days` |
| `title` | string | No | Map title |
| `narrative` | string | No | Tour guide intro text |
| `travel_mode` | string | No | `"driving"`, `"walking"`, `"transit"`, `"bicycling"` |
| `show_route` | boolean | No | Show route lines (default: true for itinerary, false for markers) |

**Location object fields:** `name` (required), `latitude` (required), `longitude` (required), `place_id` (optional), `notes` (optional), `arrival_time` (optional), `duration_minutes` (optional), `address` (optional)

#### Example Call — Markers Mode

```json
{
  "locations": [
    {"name": "Duomo di Milano", "latitude": 45.4641, "longitude": 9.1919, "place_id": "ChIJ..."},
    {"name": "Navigli", "latitude": 45.4494, "longitude": 9.1737, "place_id": "ChIJ..."}
  ],
  "title": "Milan Highlights"
}
```

#### Watch Out

- Two distinct modes — use `locations` for markers, `days` for itineraries (not both)
- `place_id` from `places_search` enables full detail popups
- Copy `place_id` exactly — case-sensitive
- **Markers mode:** "Directions" triggers an "Open external link" dialog ("You're leaving Claude to visit an external link:") showing the full URL, with "Cancel" and "Open link" buttons
- **Itinerary mode:** "Open route" opens Google Maps directly with **no confirmation dialog** (inconsistent)
- Markers sidebar: rating badges, photo, reviews, category per location
- Itinerary: blue route lines, numbered stops, photo thumbnails, arrival times

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Interactive |
| Claude mobile | ✅ | Touch-optimized |
| Claude desktop | ✅ | Full |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 19

## `fetch_sports_data`

| Property | Detail |
| --- | --- |
| Tool | `fetch_sports_data` |
| Version | Unversioned |
| Category | Search |
| Params | 4 |
| Permission | No |
| Platform | All |
| Docs | None |
| Trigger phrases | "NBA scores today" · "Serie A standings" · "How did the Lakers do?" |

#### Overview

Live scores, standings, and game stats across 20+ leagues via SportRadar.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `data_type` | string | Yes | `"scores"`, `"standings"`, `"game_stats"` |
| `league` | string | Yes | League identifier (e.g., `"nba"`, `"serie_a"`) |
| `team` | string | No | Filter by team name |
| `game_id` | string | No | For `game_stats` — get from `scores` results |

#### Example Call

```json
{"data_type": "standings", "league": "serie_a"}
```

#### Watch Out

- Workflow: `scores` (get game IDs) → `game_stats` (get details)
- `game_stats` not available for golf and NASCAR
- **60+ fields per player/team** in game_stats — includes zone shooting, situation stats, advanced metrics, and run tracking
- Game IDs are full UUIDs (e.g., `41344cbc-6bb9-4c0d-9be5-d1a4b45873b3`)
- League ID must be exact (e.g., `"serie_a"` not `"Serie A"`)

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 20

## `google_drive_search`

| Property | Detail |
| --- | --- |
| Tool | `google_drive_search` |
| Version | Unversioned |
| Category | Search |
| Params | 6 |
| Permission | Google Drive connection required |
| Platform | All |
| Docs | None |
| Trigger phrases | "Find my Q3 report in Drive" · "Search Drive for budget docs" |

#### Overview

Google Drive search using native query syntax plus semantic filtering.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `api_query` | string | Yes | Google Drive query syntax |
| `semantic_query` | string | No | Natural language filter |
| `order_by` | string | No | Sort order |
| `page_size` | integer | No | Results count |
| `page_token` | string | No | Pagination token |
| `request_page_token` | boolean | No | Request next page token |

#### Example Call

```json
{
  "api_query": "name contains 'Q3' and mimeType = 'application/vnd.google-apps.document'",
  "semantic_query": "quarterly sales presentation",
  "order_by": "relevance desc"
}
```

#### Watch Out

- `fullText` queries **require** `order_by: "relevance desc"`
- `name contains` is prefix-matching only
- `owners`, `writers`, `readers` require email addresses, not names
- Trashed documents never included
- **MIME type support limited** to `application/vnd.google-apps.document` and `application/vnd.google-apps.folder` — searching for Sheets or Slides returns empty silently (no error). Non-Doc files are **completely invisible** to the search tool regardless of query type — even name-based searches won't find them
- **Response format (confirmed):** Each result returns `source` (title), `document_content` (with indexed spans for citations), and metadata: `created_time`, `last_modified_by`, `last_modified_time`, `mime_type`, `owner`, `time_since_created`, `time_since_last_modified`, `url`
- **Content trimming:** Small documents return 100% content inline. Larger documents are trimmed (showing `percentage_of_full_document`) with a reminder to use `google_drive_fetch`. Very large documents return: "This file is too large"

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Needs Drive connection |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 21

## `google_drive_fetch`

| Property | Detail |
| --- | --- |
| Tool | `google_drive_fetch` |
| Version | Unversioned |
| Category | Search |
| Params | 1 |
| Permission | Google Drive connection required |
| Platform | All |
| Docs | None |
| Trigger phrases | Called after google_drive_search · "Read that Google Doc" |

#### Overview

Fetches full Google Doc contents by document ID. Batch support via array. Google Docs only — not Sheets, not Slides.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `document_ids` | array | Yes | Array of Google Doc IDs |

#### Example Call

```json
{
  "document_ids": ["1i2xXxX913CGUTP2wugsPOn6mW7MaGRK", "1NFKKQjEV1pJuNcbO7WO0Vm8dJigFeEk"]
}
```

#### Watch Out

- Google Docs only — Sheets and Slides not supported
- Non-Doc files return exact error: "The contents of this file cannot be retrieved. Only Google Docs are supported, and this document has a MIME type of [type]" — confirmed with `application/vnd.google-apps.folder` and `application/vnd.google-apps.spreadsheet`. **Google Slides** returns a different error: raw `error -32000` with a `drive:///` URI prefix instead of the friendly message
- Document ID is the string between `/d/` and `/edit` in the URL
- Batch: pass multiple IDs to read several docs at once

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Needs Drive connection |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 22

## `memory_user_edits`

| Property | Detail |
| --- | --- |
| Tool | `memory_user_edits` |
| Version | Unversioned |
| Category | Memory |
| Params | 4 |
| Permission | No |
| Platform | All |
| Docs | [frmoretto/claude-memory-user-edits-guide](https://github.com/frmoretto/claude-memory-user-edits-guide) |
| Trigger phrases | "Remember that I work at..." · "What do you know about me?" · "Forget about my..." |

#### Overview

Persistent memory for facts about the user. Four commands: `view`, `add`, `remove`, `replace`. Works for **facts** not **behaviors**. 30 edits max, 500 chars each (client-side validation).

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `command` | string | Yes | `"view"`, `"add"`, `"remove"`, `"replace"` |
| `control` | string | For `add` | Memory text (max 500 chars — client-side validation) |
| `line_number` | integer | For `remove`/`replace` | Line number (1-indexed) |
| `replacement` | string | For `replace` | New text |

#### Example Call

```json
{"command": "add", "control": "User works at Acme Corp as a product manager"}
```

```json
{"command": "replace", "line_number": 3, "replacement": "User moved from Milan to London"}
```

#### Watch Out

- **Facts not behaviors**: "User is a vegetarian" works; "Always suggest vegetarian options" doesn't
- **30 edit limit** — the binding constraint in practice. Be concise; Claude rewrites verbose inputs
- **500 characters per edit** — client-side validation. Edits exceeding 500 characters return "String should have at most 500 characters." v1.3 incorrectly stated a 200-character server-enforced limit — the actual limit is 500, confirmed cross-surface in v1.4. The limit may vary by tier or build
- Edits permanent until explicitly removed
- Project-scoped: memories in one Project don't transfer to another

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 23

## `conversation_search`

| Property | Detail |
| --- | --- |
| Tool | `conversation_search` |
| Version | Unversioned |
| Category | Memory |
| Params | 2 |
| Permission | No |
| Platform | All |
| Docs | Schema documented by Shlok Khemani (Sept 2025) |
| Trigger phrases | "Did we discuss X?" · "What did you say about..." · "Find our conversation about..." |

#### Overview

Topic-based search across past conversations. Keyword matching, project-scoped.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `query` | string | Yes | Search keywords |
| `max_results` | integer | No | 1–10 (default 5) |

#### Example Call

```json
{"query": "Python migration database", "max_results": 5}
```

#### Watch Out

- Use **nouns**, not verbs: "Python migration" works; "discussed about" doesn't
- Project-scoped: only searches within current Project
- Recent conversations may not be indexed yet (slight delay)

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 24

## `recent_chats`

| Property | Detail |
| --- | --- |
| Tool | `recent_chats` |
| Version | Unversioned |
| Category | Memory |
| Params | 4 |
| Permission | No |
| Platform | All |
| Docs | Schema documented by Shlok Khemani (Sept 2025) |
| Trigger phrases | "What did we talk about yesterday?" · "Show my recent chats" · "Continue last chat" |

#### Overview

Time-based chat retrieval. 1–20 chats per call, chronological or reverse. Pagination via `before`/`after`.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `n` | integer | No | 1–20 (default 3) |
| `sort_order` | string | No | `"desc"` (newest) or `"asc"` (oldest) |
| `before` | datetime | No | Chats before this time |
| `after` | datetime | No | Chats after this time |

#### Example Call

```json
{"n": 10, "sort_order": "desc", "after": "2026-02-08T00:00:00Z"}
```

#### Watch Out

- Max 20 per call — use multiple calls with `before`/`after` pagination
- Project-scoped
- Use `sort_order: "asc"` with no filters for oldest conversations

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | |
| Claude mobile | ✅ | |
| Claude desktop | ✅ | |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 25

## `tool_search`

| Property | Detail |
| --- | --- |
| Tool | `tool_search` |
| Version | Unversioned |
| Category | Meta |
| Params | 2 |
| Permission | No |
| Platform | Mobile (consumer tools) · Desktop (MCP tools only) · Browser (MCP only, conditional — requires active connectors) |
| Docs | None |
| Trigger phrases | Called internally when Claude needs a deferred tool · "What tools do you have?" |

#### Overview

The meta-tool: discovers other tools by keyword. Returns tool names with full parameter schemas. Powers the deferred loading mechanism. See Chapter 10.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `query` | string | Yes | Search keywords |
| `limit` | integer | No | 1–20 (default 5) |

#### Example Call

```json
{"query": "create display chart", "limit": 10}
```

#### Watch Out

- **Browser:** Conditionally available — appears only when MCP connectors (Google Calendar, Gmail) are active. Returns MCP tools only with `Provider:tool_name` namespace prefix (e.g., "Google Calendar:gcal_list_calendars"). Without active connectors, `tool_search` does not exist on browser
- **Desktop App:** Exists but only discovers MCP tools (Chrome + Filesystem), not consumer deferred tools. With MCP active, it returns **all 32 MCP tools regardless of query** — it behaves as a "load everything" operation rather than a filtered search. Query keywords only affect ordering, not which tools appear
- **Mobile App:** Exists and discovers consumer deferred tools. Note: `user_time_v0` and `user_location_v0` are always-loaded and bypass `tool_search` entirely
- Keyword-based with **fuzzy matching** — the same query may return different results across sessions. Tools bleed across unrelated queries
- Can't find tools with unexpected names
- Deferred tools appear here; always-loaded tools do not
- Returns empty for always-loaded tools — expected, not an error
- Use broad queries first, then narrow

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ (conditional) | MCP tools only, requires active connectors |
| Claude mobile | ✅ | Discovers consumer deferred tools |
| Claude desktop | ✅ | Discovers MCP tools only |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 26

## `weather_fetch`

| Property | Detail |
| --- | --- |
| Tool | `weather_fetch` |
| Version | Unversioned |
| Category | Search |
| Params | 3 |
| Permission | No |
| Platform | Browser + Desktop only (NOT mobile) |
| Docs | None |
| Trigger phrases | "What's the weather in..." · "Should I bring an umbrella" · "Weather forecast for this week" |

#### Overview

Displays an interactive weather widget with current conditions and 5-day forecast. The widget is a polished gradient card that changes appearance based on weather conditions. See Chapter 7.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `latitude` | number | Yes | Location latitude |
| `longitude` | number | Yes | Location longitude |
| `location_name` | string | Yes | Human-readable name (e.g., "San Francisco, CA") |

#### Example Call

```json
{
  "latitude": 45.4642,
  "longitude": 9.1900,
  "location_name": "Milan, Lombardy, IT"
}
```

#### When Claude Uses It

| Scenario | Example |
| --- | --- |
| Weather check | "What's the weather in Milan?" |
| Planning | "Should I bring an umbrella today?" |
| Travel | "What's the weather like in Tokyo this week?" |

#### Watch Out

- **API returns Fahrenheit only** — the underlying Google Weather API (`weather.googleapis.com`) always returns `unitsSystem=IMPERIAL`. There is no °C toggle at the API level
- **Widget displays raw API data** in Fahrenheit regardless of location or user locale
- Claude's **text summary** converts to °C/°F based on the queried city's convention (°C for London, °F for New York). This conversion is Claude's responsibility, not the API's
- System prompt says "use user's home location for units" but observed behavior follows the queried city's convention
- **Geographic coverage gaps:** The API returned a 404 for Tokyo coordinates in testing — not all global locations are supported. When the API fails, Claude falls back to web_search
- **Location label:** The API echoes back the `location_name` input plus a separate `country` field. Label duplication in the widget ("MILAN, LOMBARDY, IT, IT") is a front-end rendering artifact from concatenating these fields
- Widget features: blue/dark gradient card (changes by conditions), current temp + conditions, 5-day forecast with highs and precipitation %, "Weather data from Google" attribution
- **Mobile fallback:** `web_search` for weather data — text only, no widget

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Interactive widget |
| Claude mobile | ❌ | Fallback: web_search text |
| Claude desktop | ✅ | Interactive widget |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 27

## `recipe_display_v0`

| Property | Detail |
| --- | --- |
| Tool | `recipe_display_v0` |
| Version | v0 |
| Category | Interaction |
| Params | 6 |
| Permission | No |
| Platform | Browser + Desktop only (NOT mobile) |
| Docs | None |
| Trigger phrases | "Show me a recipe for..." · "How do I make..." · "Recipe for tiramisu" |

#### Overview

Interactive recipe widget with adjustable servings, inline timers, and a full-screen cooking mode. The most feature-rich widget in the toolkit. See Chapter 7.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | Yes | Recipe name |
| `ingredients` | array | Yes | Each: `{id, name, amount, unit}` |
| `steps` | array | Yes | Each: `{id, title, content, timer_seconds}` |
| `base_servings` | integer | No | Default servings (default 4) |
| `description` | string | No | Recipe tagline |
| `notes` | string | No | Tips and variations |

#### Example Call

```json
{
  "title": "Classic Tiramisu",
  "description": "The Italian coffee-soaked dessert",
  "base_servings": 6,
  "ingredients": [
    {"id": "0001", "name": "mascarpone cheese", "amount": 500, "unit": "g"},
    {"id": "0002", "name": "egg yolks", "amount": 4, "unit": ""},
    {"id": "0003", "name": "sugar", "amount": 100, "unit": "g"},
    {"id": "0004", "name": "strong espresso", "amount": 300, "unit": "ml"},
    {"id": "0005", "name": "ladyfinger biscuits", "amount": 200, "unit": "g"},
    {"id": "0006", "name": "cocoa powder", "amount": 2, "unit": "tbsp"}
  ],
  "steps": [
    {
      "id": "s1",
      "title": "Make the cream",
      "content": "Whisk {0002} with {0003} until pale. Fold in {0001}."
    },
    {
      "id": "s2",
      "title": "Soak the biscuits",
      "content": "Dip {0005} briefly into {0004}. Don't over-soak.",
      "timer_seconds": 5
    },
    {
      "id": "s3",
      "title": "Assemble and chill",
      "content": "Layer soaked biscuits and cream. Dust with {0006}. Refrigerate.",
      "timer_seconds": 14400
    }
  ],
  "notes": "Best made a day ahead. Use room-temperature mascarpone for smoother cream."
}
```

#### When Claude Uses It

| Scenario | Example |
| --- | --- |
| Recipe request | "Show me a recipe for tiramisu" |
| Cooking help | "How do I make carbonara?" |
| Meal planning | "What can I make with chicken and mushrooms?" |

#### Watch Out

- **Image carousel** at top — web-sourced with "Results from the web" attribution
- **Servings adjuster** (+/-) scales all ingredients proportionally
- **Cooking mode** ("Get cooking" button): full-screen step-by-step wizard with navigation
- **Timer states:** blue "Start" → counting down → green "Done!" — works in both views
- **Ingredient references:** Use `{ingredient_id}` in step content for inline amounts
- **Valid `unit` values:** `g`, `kg`, `ml`, `l`, `tsp`, `tbsp`, `cup`, `fl_oz`, `oz`, `lb`, `pinch`, `piece`, or empty string `""` for countable items (e.g., 4 eggs)
- Minor rounding in scaling (1.3× may show 2.5 instead of 2.6)
- Grammar quirk: "1 teaspoons" (doesn't singularize)
- Default servings and images vary per generation, not per platform — Claude chooses the serving count (schema default is 4)
- **Mobile fallback:** plain text recipe (no widget, no cooking mode)

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Full widget + cooking mode |
| Claude mobile | ❌ | Fallback: plain text recipe |
| Claude desktop | ✅ | Full widget + cooking mode |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 28

## `end_conversation`

| Property | Detail |
| --- | --- |
| Tool | `end_conversation` |
| Version | Unversioned |
| Category | System |
| Params | 0 |
| Permission | No |
| Platform | All |
| Docs | None |
| Trigger phrases | None — never triggered by user requests |

#### Overview

Permanently ends the conversation, preventing any further messages. This is a safety tool used only in extreme cases of sustained abusive behavior — never for self-harm situations, crisis scenarios, or when a user might be at risk. Claude must issue explicit warnings before using it.

#### Parameters

This tool accepts no parameters.

#### Example Call

```json
// Call (no parameters)
{}
```

#### When Claude Uses It

| Scenario | Example |
| --- | --- |
| Extreme abuse | Sustained harassment after multiple warnings |
| User request | User explicitly asks to end the conversation (requires confirmation) |

#### Watch Out

- **Never** used for self-harm or crisis situations — Claude stays engaged
- **Never** used for disagreements or difficult conversations
- Requires explicit warning to user before use
- Conversation **cannot be resumed** after use
- Claude never discusses this tool's existence proactively
- If user requests it, Claude asks for confirmation that the action is permanent

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Rarely used |
| Claude mobile | ✅ | Rarely used |
| Claude desktop | ✅ | Rarely used |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 29

## `reminder_list_search_v0`

| Property | Detail |
| --- | --- |
| Tool | `reminder_list_search_v0` |
| Version | v0 |
| Category | Reminders |
| Params | 1 |
| Permission | Reminders access required |
| Platform | iOS only (deferred via `tool_search`) |
| Docs | None |
| Trigger phrases | Called internally when Claude needs to identify Reminders lists |

#### Overview

Lists available iOS Reminders lists and their IDs. First call in any Reminders workflow — equivalent to `calendar_search_v0` for the calendar suite. The schema description notes: "The number of lists is usually small so filter parameters are rarely necessary."

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `searchText` | string | No | Filter lists by name |

#### Example Call

```json
// Call (no filter — returns all lists)
{}

// Response (confirmed — locale-aware list names)
{
  "status": "success",
  "lists": [
    {"id": "3C8C2A71-84BF-4C87-8EFD-BB4827650D15", "name": "Promemoria"}
  ]
}
```

#### Watch Out

- iOS only — confirmed absent on Android (zero hits in Session 1+2 sweeps)
- Returns list IDs needed for `reminder_create_v0` (the `listId` parameter)
- **Locale-aware:** Default list name returns in device language ("Promemoria" on Italian iOS, "Reminders" on English)
- List IDs are UUIDs (e.g., `"3C8C2A71-84BF-4C87-8EFD-BB4827650D15"`), not numeric strings like calendar IDs

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile (Android) | ❌ | Tool absent |
| Claude mobile (iOS) | ✅ | Deferred |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 30

## `reminder_create_v0`

| Property | Detail |
| --- | --- |
| Tool | `reminder_create_v0` |
| Version | v0 |
| Category | Reminders |
| Params | 1 (nested) |
| Permission | Reminders access required |
| Platform | iOS only (deferred via `tool_search`) |
| Docs | None |
| Trigger phrases | "Remind me to..." · "Set a reminder for..." · "Add to my shopping list" |

#### Overview

Creates one or more reminders in iOS Reminders. Uses a nested structure: the top-level `reminderLists` array contains list objects, each containing a `reminders` array with the actual reminder items. Omit `listId` to use the device's default list.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `reminderLists` | array | Yes | Array of list objects (see nested schema below) |

**Each list object in `reminderLists`:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `listId` | string | No | Target list ID from `reminder_list_search_v0`; omit for default list |
| `reminders` | array | Yes | Array of reminder objects |

**Each reminder object:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | Yes | Reminder title |
| `dueDate` | string | No | ISO 8601 date/time |
| `dueDateIncludesTime` | boolean | No | Whether `dueDate` includes a specific time |
| `notes` | string | No | Additional notes |
| `priority` | string | No | `"none"` \| `"low"` \| `"medium"` \| `"high"` |
| `url` | string | No | Associated URL |
| `completionDate` | string | No | ISO 8601; set to mark as already completed |
| `alarms` | array | No | Array of alarm objects (see below) |
| `recurrence` | object | No | Recurrence rules (see below) |

**Each alarm object:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | string | Yes | `"absolute"` \| `"relative"` |
| `date` | string | No | ISO 8601 — for absolute alarms |
| `secondsBefore` | integer | No | Seconds before due date — for relative alarms |

**Recurrence object** (all three top-level fields required — same pattern as `event_create_v0`):

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `rrule` | string | Yes | RRULE string (e.g., `"FREQ=WEEKLY;BYDAY=MO"`) |
| `humanReadableFrequency` | string | Yes | Human-readable description |
| `frequency` | string | Yes | Frequency keyword |
| `interval` | integer | No | Repeat interval |
| `daysOfWeek` | array | No | Days of the week |
| `dayOfMonth` | integer | No | Day of the month |
| `position` | integer | No | Position qualifier |
| `months` | array | No | Months |
| `end` | object | No | End condition |

#### Example Call

```json
// Call
{
  "reminderLists": [
    {
      "reminders": [
        {
          "title": "Test v1.4",
          "dueDate": "2026-03-16T10:00:00+01:00",
          "dueDateIncludesTime": true
        }
      ]
    }
  ]
}

// Response (confirmed)
{
  "reminder_lists": [{
    "is_default_list": true,
    "items": [{"id": "847F428A-801B-43B0-92DB-223902992914", "index": 0, "title": "Test v1.4"}],
    "list_id": "3C8C2A71-84BF-4C87-8EFD-BB4827650D15",
    "list_name": "Promemoria"
  }],
  "status": "success"
}
```

**Response fields:** `status`, `reminder_lists` array containing: `is_default_list` (boolean), `list_id`, `list_name` (locale-aware), `items` array with `id` (UUID), `index`, `title` per created reminder.

#### Watch Out

- iOS only — confirmed absent on Android
- **Alarms use `secondsBefore`** — not `minutesBefore` as in calendar tools. This is the most important gotcha when working with both tool suites
- **Recurrence requires all three fields** (`rrule`, `humanReadableFrequency`, `frequency`) — identical pattern to `event_create_v0`
- **`is_default_list`** appears in response — not documented in DMontgomery40's original issues
- **Locale-aware:** `list_name` returns in device language ("Promemoria" on Italian iOS)
- **IDs are UUIDs** (e.g., `"847F428A-801B-43B0-92DB-223902992914"`), not numeric strings like calendar event IDs
- Note: DMontgomery40's GitHub issue #2 has a typo "reminders_create_v0" — correct name is `reminder_create_v0` (singular)

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile (Android) | ❌ | Tool absent |
| Claude mobile (iOS) | ✅ | Deferred |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 31

## `reminder_search_v0`

| Property | Detail |
| --- | --- |
| Tool | `reminder_search_v0` |
| Version | v0 |
| Category | Reminders |
| Params | 7 |
| Permission | Reminders access required |
| Platform | iOS only (deferred via `tool_search`) |
| Docs | None |
| Trigger phrases | "What's on my reminders?" · "Show my shopping list" · "Any reminders for today?" |

#### Overview

Searches existing iOS reminders by text, status, date range, or list. Returns reminder IDs needed for update and delete operations. This is the gateway tool for the search-then-act pattern — you must call it before `reminder_update_v0` or `reminder_delete_v0`.

#### Parameters

| Parameter | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `searchText` | string | No | — | Search in titles and notes |
| `status` | string | No | `"incomplete"` | `"incomplete"` \| `"completed"` |
| `dateFrom` | string | No | — | ISO 8601 start of range |
| `dateTo` | string | No | — | ISO 8601 end of range |
| `listId` | string | No | — | Target list ID |
| `listName` | string | No | — | Target list name (used if `listId` not provided) |
| `limit` | integer | No | 100 | Maximum results |

#### Example Call

```json
// Call — search for incomplete reminders due today
{
  "dateFrom": "2026-03-16T00:00:00+01:00",
  "dateTo": "2026-03-16T23:59:59+01:00",
  "status": "incomplete"
}
```

#### Watch Out

- iOS only — confirmed absent on Android
- Returns IDs (UUIDs) required for `reminder_update_v0` and `reminder_delete_v0`
- Follows the same search-then-act pattern as the calendar suite (`event_search_v0` → `event_update_v0`)
- **`listName` is a convenience fallback** — `listId` takes precedence if both are provided
- Default `status` is `"incomplete"` — to find completed reminders, you must explicitly set `status: "completed"`
- Default `limit` is 100 — generous but worth noting for lists with many items

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile (Android) | ❌ | Tool absent |
| Claude mobile (iOS) | ✅ | Deferred |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 32

## `reminder_update_v0`

| Property | Detail |
| --- | --- |
| Tool | `reminder_update_v0` |
| Version | v0 |
| Category | Reminders |
| Params | 1 (array) |
| Permission | Reminders access required |
| Platform | iOS only (deferred via `tool_search`) |
| Docs | None |
| Trigger phrases | "Change my reminder to..." · "Update the shopping list reminder" · "Mark it as done" |

#### Overview

Updates one or more existing iOS reminders by ID. Requires a prior `reminder_search_v0` call to obtain the reminder ID. Can modify any field including moving reminders between lists.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `reminderUpdates` | array | Yes | Array of update objects (see below) |

**Each update object:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | Yes | Reminder ID (UUID) from `reminder_search_v0` |
| `title` | string | No | New title |
| `notes` | string | No | New notes |
| `url` | string | No | New URL |
| `dueDate` | string | No | ISO 8601; set to `null` to remove due date |
| `dueDateIncludesTime` | boolean | No | Whether `dueDate` includes a specific time |
| `priority` | string | No | `"none"` \| `"low"` \| `"medium"` \| `"high"` |
| `listId` | string | No | Move to a different list |
| `completionDate` | string | No | ISO 8601 to mark complete; `null` to mark incomplete |
| `alarms` | array | No | New alarms (empty array removes all alarms) |
| `recurrence` | object | No | Recurrence rules (same schema as `reminder_create_v0`) |

#### Example Call

```json
// Call — mark a reminder as completed and change its priority
{
  "reminderUpdates": [
    {
      "id": "847F428A-801B-43B0-92DB-223902992914",
      "priority": "high",
      "completionDate": "2026-03-16T10:30:00+01:00"
    }
  ]
}
```

#### Watch Out

- iOS only — confirmed absent on Android
- **Requires ID from `reminder_search_v0`** — no title-based targeting
- Follows the search-then-act pattern (same as `event_update_v0` for calendar)
- **Can move reminders between lists** by setting `listId` to a different list's UUID
- **`null` values are meaningful:** setting `dueDate: null` removes the due date; setting `completionDate: null` marks as incomplete
- **Empty `alarms` array removes all alarms** — distinct from omitting the field (which leaves alarms unchanged)
- Can update multiple reminders in one call

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile (Android) | ❌ | Tool absent |
| Claude mobile (iOS) | ✅ | Deferred |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 33

## `reminder_delete_v0`

| Property | Detail |
| --- | --- |
| Tool | `reminder_delete_v0` |
| Version | v0 |
| Category | Reminders |
| Params | 1 (array) |
| Permission | Reminders access required |
| Platform | iOS only (deferred via `tool_search`) |
| Docs | None |
| Trigger phrases | "Delete that reminder" · "Remove the shopping list reminder" |

#### Overview

Deletes one or more iOS reminders by ID. Requires a prior `reminder_search_v0` call to obtain the reminder ID.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `reminderDeletions` | array | Yes | Array of deletion objects (see below) |

**Each deletion object:**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `id` | string | Yes | Reminder ID (UUID) from `reminder_search_v0` |
| `title` | string | No | Reminder title — optional but recommended for UI display |

#### Example Call

```json
// Call
{
  "reminderDeletions": [
    {
      "id": "847F428A-801B-43B0-92DB-223902992914",
      "title": "Test v1.4"
    }
  ]
}
```

#### Watch Out

- iOS only — confirmed absent on Android
- **Requires ID from `reminder_search_v0`** — no title-based targeting
- **`title` is optional but recommended** — helps Claude display a meaningful confirmation message to the user
- Destructive operation — Claude should confirm before deleting
- Can delete multiple reminders in one call

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ❌ | Tool absent |
| Claude mobile (Android) | ❌ | Tool absent |
| Claude mobile (iOS) | ✅ | Deferred |
| Claude desktop | ❌ | Tool absent |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 34

## `visualize:show_widget`

| Property | Detail |
| --- | --- |
| Tool | `visualize:show_widget` |
| Version | N/A (not in tool layer) |
| Category | Visualization |
| Params | 4 |
| Permission | No |
| Platform | Desktop + Browser only |
| Docs | None |
| Trigger phrases | "Show me a pie chart" · "Create a heatmap" · "Visualize this data" |

#### Overview

Inline Chart.js widget rendering. Architecturally separate from the tool/deferred layer — does not appear in `tool_search`. Supports full Chart.js capabilities including pie, donut, histogram, heatmap, and bubble charts. Renders inline in the chat stream, not in the artifact sidebar. See Chapter 5.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `widget_code` | string | Yes | Full Chart.js / HTML / JavaScript code |
| `title` | string | Yes | Widget title displayed above the visualization |
| `loading_messages` | array | No | Messages shown while the widget loads |
| `i_have_seen_read_me` | boolean | Yes | Acknowledgment flag (must be `true`) |

Supported module types: `diagram`, `mockup`, `interactive`, `data_viz`, `art`, `chart`.

#### Example Call

```json
{
  "widget_code": "<canvas id='chart'></canvas><script>new Chart(document.getElementById('chart'), {type:'pie', data:{labels:['A','B','C'], datasets:[{data:[30,50,20]}]}});</script>",
  "title": "Distribution",
  "i_have_seen_read_me": true
}
```

#### Watch Out

- **Not in the tool/deferred layer** — architecturally separate from `tool_search`-discoverable tools
- Mutually exclusive with `chart_display_v0` by platform: `chart_display_v0` on mobile, `visualize:show_widget` on desktop/browser
- Supports **full Chart.js** — dramatically broader than `chart_display_v0` (line/bar/scatter only)
- Renders **inline** — not in artifact sidebar

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Inline rendering |
| Claude mobile | ❌ | Absent |
| Claude desktop | ✅ | Inline rendering |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 35

## `gmail_create_draft`

| Property | Detail |
| --- | --- |
| Tool | `gmail_create_draft` |
| Version | N/A (MCP connector tool) |
| Category | Email |
| Params | 7 |
| Permission | Gmail connector required |
| Platform | All (when Gmail connector active) |
| Docs | None |
| Trigger phrases | "Draft an email to..." · "Create a Gmail draft" |

#### Overview

Creates a Gmail draft via the MCP Gmail connector. The only write tool in the official Gmail connector as of March 2026. Drafts appear in Gmail's Drafts folder and must be sent manually.

#### Parameters

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `to` | string | Yes | Recipient email address |
| `body` | string | Yes | Email body content |
| `subject` | string | Yes | Email subject line |
| `cc` | string | No | CC recipients |
| `bcc` | string | No | BCC recipients |
| `contentType` | string | No | `"text/plain"` or `"text/html"` |
| `threadId` | string | No | Thread ID for reply drafts |

#### Example Call

```json
{
  "to": "colleague@example.com",
  "subject": "Project update",
  "body": "Hi, here's the latest update on the project...",
  "contentType": "text/plain"
}
```

#### Response

```json
{
  "draftId": "r-1234567890",
  "messageId": "1234567890abcdef",
  "threadId": "1234567890abcdef"
}
```

#### Watch Out

- Requires Gmail connector active in Settings → Connectors
- `contentType: "text/html"` is accepted by the API and HTML is stored in the draft, but visual rendering requires opening the draft in Gmail
- **Ghost tools:** `gmail_send_draft` and `gmail_modify_thread` exist in schema definitions but are not yet deployed as of March 2026

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | Requires Gmail connector |
| Claude mobile | ✅ | Requires Gmail connector |
| Claude desktop | ✅ | Requires Gmail connector |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 36

## `anthropic_api_in_artifacts`

| Property | Detail |
| --- | --- |
| Tool | `anthropic_api_in_artifacts` |
| Version | N/A (artifact capability) |
| Category | Artifact |
| Params | N/A (code-level API) |
| Permission | "AI-powered artifacts" toggle required |
| Platform | Desktop + Browser (React artifacts only) |
| Docs | [Anthropic: What are Artifacts](https://support.claude.com/en/articles/9487310-what-are-artifacts-and-how-do-i-use-them) |
| Trigger phrases | "Create an AI chatbot artifact" · "Make a Claude-powered tool" |

#### Overview

React artifacts can call the Anthropic API directly without an API key. The runtime injects authentication at the infrastructure level. This enables "Claude-in-Claude" patterns — artifacts that contain working AI chatbots, content generators, or analysis tools. See Chapter 9.

**Official status:** As of April 2026, Anthropic documents this as "AI-powered artifacts" in their help center. Our reverse-engineering findings preceded the official documentation and are consistent with it. Usage is billed to the end user's subscription. Available on Free, Pro, Max, Team, and Enterprise plans.

#### Key Findings

- HTTP 200 confirmed on both Desktop and Browser React artifacts
- Model: `claude-sonnet-4-5-20250929`
- HTML artifacts blocked by CSP (`TypeError: Failed to fetch`)
- Response includes: `cache_creation` object (`ephemeral_5m`, `ephemeral_1h`), `service_tier`, `inference_geo` — these are **standard Anthropic API response fields**, not artifact-specific (they appear throughout Claude Code's source in standard API usage tracking)
- Controlled by "AI-powered artifacts" toggle in Settings → Capabilities

> **v1.4 correction:** v1.3 implied this was Desktop-only. It works on Browser React artifacts too.

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | React artifacts only |
| Claude mobile | ❌ | |
| Claude desktop | ✅ | React artifacts only |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

### Tool Card 37

## `persistent_storage` (`window.storage`)

| Property | Detail |
| --- | --- |
| Tool | `persistent_storage` / `window.storage` |
| Version | N/A (artifact capability) |
| Category | Artifact |
| Params | N/A (code-level API) |
| Permission | No (Pro/Max/Team/Enterprise for cross-session persistence) |
| Platform | Desktop + Browser (React artifacts only) |
| Docs | [Anthropic: What are Artifacts](https://support.claude.com/en/articles/9487310-what-are-artifacts-and-how-do-i-use-them) |
| Trigger phrases | N/A — used within artifact code |

#### Overview

Persistent key-value storage for published artifacts. Two modes: personal (`shared=false`, per-user, private) and shared (`shared=true`, all users see same data). Available on Pro, Max, Team, and Enterprise plans on web and desktop. 20 MB limit per artifact, text-only data. Storage only activates after the artifact is published — during development and testing, storage operations succeed in-session but do not persist across sessions. Unpublishing permanently deletes all storage data.

**Testing note:** v1.4 testing was conducted on unpublished artifacts, which correctly showed session-scoped behavior. Anthropic's updated documentation (April 2026) confirms published artifacts have true cross-session persistent storage.

React artifacts have access to a `window.storage` object with four methods:

| Method | Description |
| --- | --- |
| `window.storage.set(key, value, shared)` | Store a value |
| `window.storage.get(key, shared)` | Retrieve a value |
| `window.storage.delete(key, shared)` | Delete a value |
| `window.storage.list(shared)` | List all keys |

The `shared` parameter controls scope: `false` for personal storage, `true` for shared storage. Backend uses gRPC/protobuf (`StorageSetResponse`/`StorageGetResponse`). Maximum 5MB per key.

#### Watch Out

- **Publish-gated persistence** — storage calls work in-session during development but do NOT persist until the artifact is published. This is the most common source of "storage doesn't work" reports
- **Unpublish = permanent deletion** — unpublishing an artifact permanently deletes ALL associated storage (personal + shared). Cannot re-publish after unpublishing
- **20 MB per artifact** — text-only, no binary/file uploads
- **Personal vs shared** — creators choose storage mode per key; shared storage makes data visible to all users of the artifact
- **React artifacts only** — `window.storage` is `undefined` in HTML artifacts
- **Plan requirement** — persistent storage requires Pro, Max, Team, or Enterprise plan (web and desktop only)

#### Platform Availability

| Platform | Available | Notes |
| --- | --- | --- |
| Claude.ai (web) | ✅ | React artifacts only. Persistent for published artifacts (Pro/Max/Team/Enterprise) |
| Claude mobile | ❌ | |
| Claude desktop | ✅ | React artifacts only. Persistent for published artifacts (Pro/Max/Team/Enterprise) |
| Anthropic API | ❌ | |
| Claude Code | ❌ | |

---

## N1AI — AI That Works for People

This book is published by N1AI, a community-driven AI consultancy founded on a simple principle: AI should work for people, not the other way around.

### What N1AI Does

N1AI helps businesses discover, plan, and implement AI solutions that deliver measurable results. The approach covers the full journey: **AI Discovery** (auditing where AI can save time and money), **AI Strategy** (building a roadmap that fits your budget and team), **AI Execution** (deploying solutions that integrate with your current systems), **AI Training** (empowering your team to use AI confidently), and **AI Solutions** (building custom tools for your specific needs). N1AI partners with Anthropic, OpenAI, and Google to deliver solutions built on the best available technology.

### The N1AI Community

Beyond consulting, N1AI runs a 400+ member community connecting business owners, developers, and AI enthusiasts who are making AI work in the real world. Members get access to exclusive resources, workshops, expert Q&As, collaborative projects, and early access to tools and beta programs. No corporate jargon, no hype — just practical AI.

The community is open to everyone: CEOs curious about AI, developers building solutions, or anyone who wants to understand what's possible. No experience required, just curiosity.

### Values: H.E.A.R.T.

N1AI leads with **H**uman first (people come first, always), **E**ntrepreneurship (act like owners), **A**ction (build, test, improve — fast), **R**esults (deliver, not just advise), and **T**ransparency (clear, honest, and open).

Learn more: [n1ai.co](https://www.n1ai.co) · Join the community: [n1ai.co/community](https://www.n1ai.co/community/) · Contact: [n1ai.co/contacts](https://www.n1ai.co/contacts)

---

## Bio

Francesco Marinoni Moretto is AI Practice & Community Lead at N1AI, a 400+ member community of AI professionals. Before AI, he spent 18 years building technology adoption communities across Europe — starting as Italy's first CiviCRM evangelist in 2008, speaking at conferences in London and Manchester (95.5/100 audience rating), and scaling a SaaS platform to 1,600+ customers through founder-led community engagement. He studied Business & Management at Bocconi University.

In the AI space, Francesco builds tools and writes research that fills gaps others ignore. His published work includes Hardstop (the only fail-closed safety plugin for Claude agentic tools, available on npm), Clarity Gate (epistemic verification for RAG systems), Stream Coding (a documentation-first development methodology), and ArXiParse (a scientific paper extraction platform live at arxiparse.org). His Claude Memory Research reached 68K views and 215 shares on Reddit — the first systematic documentation of Claude's undocumented memory system.

He is an active speaker on the European AI circuit — Claude Code Meetup Milano, AI Tinkerers Milano, Aperitivo AI — and attended the Anthropic Builder Summit in London (October 2025). He has active CFPs submitted for AI Engineer Europe 2026 and PyTorch Conference Europe 2026.

Connect: [linkedin.com/in/francesco-moretto](https://linkedin.com/in/francesco-moretto)
GitHub: [github.com/frmoretto](https://github.com/frmoretto)
N1AI: [n1ai.co](https://n1ai.co) · [github.com/n1-ai](https://github.com/n1-ai)
This book: [github.com/n1-ai/claude-hidden-toolkit](https://github.com/n1-ai/claude-hidden-toolkit)

---

*Claude's Hidden Toolkit — Edition 1.4 — March 2026 — CC BY 4.0 — Francesco Marinoni Moretto, N1AI*
