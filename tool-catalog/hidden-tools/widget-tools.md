# Widget Tools

**Status:** CONFIRMED in Claude Desktop main chat and claude.ai web chat. Not exposed via `tool_search` in any agentic context — these are Electron renderer-only tools injected into the chat UI layer by `@ant/imagine-server`, not registered through the MCP tool system.

**Total:** 2 tools

**Verified:** 2026-03-19. Tested in Dispatch orchestrator, Cowork task session, code task session, Claude Code CLI (not found), and claude.ai web chat (confirmed working).

![Widget rendering in claude.ai web chat](./widget-screenshot.png)
*Screenshot: Interactive architecture diagram rendered inline via `show_widget`. Captured 2026-03-19 in claude.ai web chat.*

---

## read_me

Returns required context for `show_widget` (CSS variables, colors, typography, layout rules, examples). Must be called before the first `show_widget` call.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| modules | string[] | no | Which module(s) to load. Options: "diagram", "mockup", "interactive", "data_viz", "art", "chart" |

**Notes:** The tool description instructs the model: "Call before your first show_widget call. Do NOT mention or narrate this call to the user — it is an internal setup step. Call it silently and proceed directly to the visualization."

**Returns:** CSS variables, color palettes, typography rules, layout rules, and examples for the requested modules. The `diagram` module alone returns ~300 lines of design system docs including SVG setup rules, color palette tables, font calibration, and full worked examples.

**Behavior notes:**
- Must be called before any `show_widget` call — `show_widget` depends on the context this provides
- Can be called again later to load a different module set

---

## show_widget (registered as "visualize widget")

Renders SVG or HTML content progressively as it streams, displayed inline in the chat.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| title | string | yes | Snake_case identifier for the widget. Used as download filename. |
| loading_messages | string[] | yes | 1-4 loading messages shown while rendering. ~5 words each. Match user's language. Use 1 for simple, more for complex. |
| widget_code | string | yes | Raw SVG or HTML fragment to render. |

**Behavior:**
- Auto-detects content type: starts with `<svg` → SVG mode (wrapped in a card), otherwise HTML mode
- A global `sendPrompt(text)` function is available in the rendered content — sends a message to chat as if the user typed it, enabling interactive chat-loop widgets
- The registered tool name is "visualize widget" but the handler matches on "show_widget"
- Returns: "Content rendered and shown to the user. Please do not duplicate the shown content in text because it's already visually represented."
- Content is streamed as `mimeType: "text/html;profile=mcp-app"`

**Constraints:**
- No `DOCTYPE`, `<html>`, `<head>`, or `<body>` tags in HTML mode — content fragments only
- External resources restricted to `cdnjs.cloudflare.com`, `esm.sh`, `cdn.jsdelivr.net`, `unpkg.com` (CSP-enforced)
- `localStorage` / `sessionStorage` not available — all state must live in React/JS memory
- For serious topics (illness, death, grief, war, etc.) loading messages should be subdued

**Use cases:** Flowcharts, architecture diagrams, dashboards, forms, calculators, data tables, games, illustrations, or any visual content that benefits from inline rendering.

---

## Rendering Modes

Two rendering modes are referenced in the system prompt documentation:

### `imagine_html`
For interactive content: sliders, buttons, live state displays, charts. The system prompt instructs keeping prose explanations in the normal response text, not embedded in HTML. No card wrapper — whitespace is the container.

### `imagine_svg`
For diagrams. The widget automatically wraps SVG output in a card. The system prompt includes guidance on choosing diagram type based on intent (reference diagram vs. conceptual illustration).

These are NOT separate tools — they are conceptual rendering modes within `show_widget`. Content type is detected automatically based on whether the content starts with `<svg`.

### SVG Text Gap System
An internal SVG post-processing system (`imagine-text-gaps-*`) creates masks to prevent text overlap in SVG diagrams. It auto-detects intersecting text elements and applies SVG masks. This is handled automatically with no user/model input needed.

---

## Design System (from read_me)

The design system loaded by `read_me` enforces the following rules:

- **680px viewBox width** — load-bearing, do not change
- **Two font sizes only:** 14px (`class="t"` or `"th"`) and 12px (`class="ts"`)
- **Pre-built classes:** `t`, `ts`, `th` (text), `box` (neutral rect), `node` (clickable group), `arr` (arrow), `c-{ramp}` (9 color ramps: purple, teal, coral, pink, gray, blue, green, amber, red)
- **CSS variables** for all colors — mandatory dark mode support
- **No gradients, shadows, or blur** (one linearGradient allowed in illustrative diagrams only)
- **Arrow marker defs** must be included in every SVG
- **Diagram types:** flowchart, structural, illustrative — chosen by intent, not subject matter

---

## Dependencies

The widget system is backed by `@ant/imagine-server` (listed as a dependency in `package.json`). This is the service that handles widget rendering and content delivery.
