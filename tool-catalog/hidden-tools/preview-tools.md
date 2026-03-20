# Preview Tools

**Status:** CONFIRMED in Claude Desktop **code task sessions** (via `start_code_task` from Dispatch). Registered under `mcp__Claude_Preview__` namespace. Not available in Cowork task sessions, Dispatch orchestrator, or Claude Code CLI.

**Total:** 13 tools

**Verified:** 2026-03-19. `preview_start` successfully launched a Vite dev server (port 9322). `preview_list` returned `[]` when no servers running, then showed the running server. `preview_screenshot`, `preview_snapshot`, `preview_eval`, `preview_console_logs` all returned valid responses. Screenshot showed blank page due to misconfigured `launch.json` (not a tool issue).

---

## preview_start

Start a dev server by name from `.claude/launch.json`. Reuses the server if already running.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| name | string | yes | Server name from .claude/launch.json |

**Notes:** Tool description says "ALWAYS use this instead of Bash for running servers." Requires a `.claude/launch.json` config file in the project.

---

## preview_stop

Stop a server started with `preview_start`.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID to stop |

---

## preview_list

List servers started with `preview_start`. Returns `serverId` values for use with other `preview_*` tools.

No parameters.

---

## preview_logs

Get server stdout/stderr output.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| level | enum | no | "all" (default) or "error" (lines containing error/exception/failed/fatal) |
| lines | number | no | Max lines to return (default 50) |
| search | string | no | Filter for specific text |

---

## preview_console_logs

Get browser console output (log, info, warn, error, debug).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| level | enum | no | "all" (default), "error" (errors only), "warn" (warnings + errors) |
| lines | number | no | Max lines to return (default 50, max 200) |

---

## preview_screenshot

Take a screenshot of the page. Returns compressed JPEG.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |

**Notes:** Tool description warns: "DO NOT rely on it for verifying colors, font sizes, or precise styles — use `preview_inspect` with specific CSS properties instead."

---

## preview_snapshot

Get an accessibility tree snapshot of the page. Returns text content, roles, and element UIDs.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |

**Notes:** "PREFERRED over screenshot for verifying text, element presence, and page structure." UIDs can be used with `preview_click` / `preview_fill`.

---

## preview_inspect

Inspect a DOM element by CSS selector. Returns text content, className, tagName, id, computed styles, and bounding box.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| selector | string | yes | CSS selector (e.g. '.button', '#header') |
| styles | string[] | no | CSS properties to return (e.g. ['padding', 'color']). Defaults to common properties. |

**Notes:** "BEST tool for verifying visual properties like colors, fonts, spacing, and dimensions — more accurate than screenshots."

---

## preview_click

Click an element by CSS selector.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| selector | string | yes | CSS selector (e.g. 'button.primary', '#submit', '[data-testid="btn"]') |
| doubleClick | boolean | no | Perform a double-click |

---

## preview_fill

Fill an input, textarea, or select element with a value.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| selector | string | yes | CSS selector for the input element |
| value | string | yes | Value to fill. For select elements, matches by value or text. |

---

## preview_eval

Execute JavaScript in the preview page. For debugging and inspection only.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| expression | string | yes | JavaScript expression to evaluate in page context. Return value is sent back. |

**Notes:** "Do NOT use this to implement UI changes the user requests — edit the source code instead. Any DOM modifications via eval are temporary and lost on reload. Wrap multi-step logic in an IIFE."

---

## preview_network

List network requests or inspect a specific response body.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| filter | enum | no | "all" (default) or "failed" (4xx/5xx and network errors). Ignored when `requestId` provided. |
| requestId | string | no | If provided, returns full response body for that request. |

**Notes:** Without `requestId`, lists all requests with URL, method, status, and `requestId`. With `requestId`, returns the full response body.

---

## preview_resize

Resize the preview viewport for responsive layout testing.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| serverId | string | yes | Server ID |
| preset | enum | no | "mobile" (375x812), "tablet" (768x1024), "desktop" (1280x800). Overrides width/height. |
| width | number | no | Viewport width in pixels |
| height | number | no | Viewport height in pixels |
| colorScheme | string | no | Color scheme emulation for dark mode testing |
