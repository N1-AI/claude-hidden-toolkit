# Claude's Hidden Toolkit

**The Definitive Guide to Claude.ai's Undocumented Internal Tools**

*37 tools. Zero official documentation. Fully reverse-engineered.*

---

## What Is This?

Claude.ai ships with 37+ internal tools — capabilities that let Claude check the time, search your calendar, render interactive charts, draft emails directly into your Gmail, find nearby restaurants, display them on a map, set alarms on your phone, manage iOS Reminders, build AI-powered artifacts that call Claude from within Claude, and more.

**Almost none of this is documented.**

Anthropic's official docs cover the API. But the consumer product — claude.ai — has a rich, evolving toolkit that exists in a documentation void. This book fills that gap through systematic reverse-engineering across four platforms (browser, desktop app, Android, iOS).

## Key Discoveries

**Platform fragmentation is real.** Claude is not one product — it's three different tool sets:

| Platform | Always-Loaded | tool_search | Deferred Tools |
|----------|:---:|:---:|:---:|
| Browser (claude.ai) | 21 | MCP only (when connectors active) | None |
| Desktop App | 22 | MCP only | 32 (Chrome + Filesystem) |
| Mobile (Android) | 22 | Consumer | 11 (alarm, timer, calendar, etc.) |
| Mobile (iOS) | 22 | Consumer | 16 (Android set + 5 Reminders tools) |

**MCP connector state changes the tool inventory.** Connecting Gmail or Google Calendar adds tools to the always-loaded pool. Disconnecting removes them. This is a new architectural variable documented in v1.4.

**The artifact execution layer.** Claude artifacts can make authenticated API calls back to Claude itself — Claude-inside-Claude — without API keys. The runtime injects authentication. This, along with session-scoped storage and MCP endpoint access, means artifacts are evolving into a lightweight application platform.

**Every response format has been empirically confirmed** — no inferred schemas remain.

## What's Inside

| Chapter | Coverage |
|---------|----------|
| 1–2 | Architecture: two-tier loading, platform inventory, MCP connector state, versioning |
| 3 | Context tools: `user_time_v0`, `user_location_v0` |
| 4 | Interaction widgets: `ask_user_input_v0`, `message_compose_v1` |
| 5 | Inline charts: `chart_display_v0`, `visualize:show_widget` |
| 6 | Calendar & device: 6 calendar tools + alarms + timers |
| 7 | Search & data: web, images, places, maps, sports, Drive, weather, recipes |
| 8 | Memory: `memory_user_edits`, `conversation_search`, `recent_chats` |
| 9 | Computer use: bash, files, artifacts, the skill system, artifact execution layer |
| 10 | The meta-tool: `tool_search` and enumeration methodology |
| 11 | Complete tool reference index |
| 12 | Ghost tools, MCP convergence, and what comes next |
| Annex | 37 detailed tool cards with confirmed JSON schemas |

## Read the Book

- **[Claude_Hidden_Toolkit.md](Claude_Hidden_Toolkit.md)** — Full book, Markdown (~25,000 words)

## Edition History

| Edition | Date | Changes |
|---------|------|---------|
| 1.4 | Mar 2026 | 9 new tools (28→37), 13 corrections, 2 external contributors. New: iOS Reminders suite (5 tools), `visualize:show_widget`, `gmail_create_draft`, `anthropic_api_in_artifacts`, `persistent_storage`. Corrected: `user_time_v0`/`user_location_v0` always-loaded on mobile, `memory_user_edits` 500-char limit, `chart_display_v0` intermittent (not deterministic crash), `window.storage` session-scoped, `tool_search` on browser with MCP. New chapter sections on artifact execution layer and MCP connector state. |
| 1.3 | Feb 2026 | Platform architecture chapter, 3 new tool cards (weather, recipe, end_conversation), cross-platform verification of all 28 cards, settings/connectors appendix, all response formats confirmed |
| 1.2 | Feb 2026 | 25 tool cards, discovery methodology, initial availability matrix |
| 1.0–1.1 | Feb 2026 | Initial discovery and documentation |

## Contributors

This project is maintained by Francesco Marinoni Moretto and has benefited from external contributions:

- **An independent security researcher** (anonymous by request) — 11 findings including the artifact API discovery, session-scoped storage, MCP endpoints in artifacts, skill filesystem topology, and egress proxy JWT architecture
- **David Montgomery** ([GitHub](https://github.com/DMontgomery40) · [LinkedIn](https://www.linkedin.com/in/david-montgomery-5a07656b/)) — Documented the complete iOS Reminders CRUD suite (5 tools) via GitHub issues

## Prior Art & Credits

This work builds on contributions from: **Shlok Khemani** (memory tool schemas, Sept 2025), **Simon Willison** (system prompt analysis), **Adversa AI** (security research, named `message_compose_v1`), **Federico Viticci / MacStories** (iOS tools and `_v0` naming convention discovery).

## Who This Is For

- **Power users** — learn exactly what to say to trigger hidden capabilities
- **Developers** — see what the consumer product does that the API doesn't
- **AI researchers** — a documented case study of production AI tool architecture

## Author

**Francesco Marinoni Moretto** — AI Practice & Community Lead, [N1AI](https://www.n1ai.co)

- LinkedIn: [linkedin.com/in/francesco-moretto](https://www.linkedin.com/in/francesco-moretto)
- GitHub: [github.com/frmoretto](https://github.com/frmoretto)

## License

CC BY 4.0 — You are free to share and adapt this work with attribution.

This work is independent and not affiliated with Anthropic. Tools may change, be renamed, or be removed at any time.
