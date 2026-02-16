# Claude's Hidden Toolkit

**The Definitive Guide to Claude.ai's Undocumented Internal Tools**

*28 tools. Zero official documentation. Fully reverse-engineered.*

---

## What Is This?

Claude.ai ships with 28+ internal tools — capabilities that let Claude check the time, search your calendar, render interactive charts, draft emails with native app integration, find nearby restaurants, display them on a map, set alarms on your phone, and more.

**Almost none of this is documented.**

Anthropic's official docs cover the API. But the consumer product — claude.ai — has a rich, evolving toolkit that exists in a documentation void. This book fills that gap through systematic reverse-engineering across three platforms (browser, desktop app, mobile app).

## Key Discoveries

**Platform fragmentation is real.** Claude is not one product — it's three different tool sets:

| Platform | Always-Loaded | tool_search | Deferred Tools |
|----------|:---:|:---:|:---:|
| Browser (claude.ai) | 21 | ❌ | None |
| Desktop App | 22 | MCP only | 32 (Chrome + Filesystem) |
| Mobile App | 20 | Consumer | 11 (alarm, timer, calendar, etc.) |

**Every response format has been empirically confirmed** — no inferred schemas remain in Edition 1.3.

## What's Inside

| Chapter | Coverage |
|---------|----------|
| 1–2 | Architecture: two-tier loading, platform inventory, versioning |
| 3 | Context tools: `user_time_v0`, `user_location_v0` |
| 4 | Interaction widgets: `ask_user_input_v0`, `message_compose_v1` |
| 5 | Inline charts: `chart_display_v0` |
| 6 | Calendar & device: 6 calendar tools + alarms + timers |
| 7 | Search & data: web, images, places, maps, sports, Drive, weather, recipes |
| 8 | Memory: `memory_user_edits`, `conversation_search`, `recent_chats` |
| 9 | Computer use: bash, files, artifacts, the skill system |
| 10 | The meta-tool: `tool_search` and enumeration methodology |
| 11 | Complete tool reference index |
| 12 | MCP convergence and what comes next |
| Annex | 28 detailed tool cards with confirmed JSON schemas |

## Read the Book

- **[Claude_Hidden_Toolkit.md](Claude_Hidden_Toolkit.md)** — Full book, Markdown (~21,600 words)

## Edition History

| Edition | Date | Changes |
|---------|------|---------|
| 1.3 | Feb 2026 | Platform architecture chapter, 3 new tool cards (weather, recipe, end_conversation), cross-platform verification of all 28 cards, settings/connectors appendix, all response formats confirmed |
| 1.2 | Feb 2026 | 25 tool cards, discovery methodology, initial availability matrix |
| 1.0–1.1 | Feb 2026 | Initial discovery and documentation |

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
