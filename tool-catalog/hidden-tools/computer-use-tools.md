# Computer Use Tools (`mcp__computer-use__*`)

**Status:** The `computer` tool exists under `mcp__Claude_in_Chrome__computer` in Dispatch sessions (verified via ToolSearch and Chrome tab context). The standalone `mcp__computer-use__*` namespace was not found in deferred tools — it requires an explicit computer-use MCP connection. The Chrome-based `computer` tool includes all 13 actions documented below.

**Verified:** 2026-03-19. `mcp__Claude_in_Chrome__computer` screenshot action tested and working in Dispatch.

Full desktop automation via screenshot-then-act loop. Registered as a dynamic MCP server — tools appear as `mcp__computer-use__*` in the deferred tools list only when the computer-use MCP is connected.

**Priority order (from system prompt):**
1. Dedicated MCP for the app (Slack, Gmail, etc.) — fastest, most precise
2. Chrome MCP (`mcp__Claude in Chrome__*`) — DOM-aware, faster than pixels
3. Computer use — only when neither above fits (native desktop apps, cross-app workflows)

---

## computer

The single tool that handles all desktop interactions through an `action` parameter.

### Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `action` | string (enum) | **yes** | One of 13 actions (see below) |
| `coordinate` | [number, number] | varies | (x, y) pixel coordinates from top-left. Required for click/scroll actions. For `left_click_drag`, this is the **end** position. |
| `text` | string | varies | Text to type (for `type`) or key(s) to press (for `key`). For `key`: space-separated, supports modifier combos like "cmd+a" or "ctrl+a". |
| `duration` | number | for `wait` | Seconds to wait. Max 5 seconds. |
| `scroll_direction` | string (enum) | for `scroll` | "up", "down", "left", "right" |
| `scroll_amount` | number | no | Scroll wheel ticks (1-10). Default: 3. |
| `start_coordinate` | [number, number] | for `left_click_drag` | Starting position for drag. |
| `region` | [number, number, number, number] | for `zoom` | (x0, y0, x1, y1) rectangle to capture. |
| `repeat` | number | no | Times to repeat key sequence (1-100). Default: 1. Only for `key` action. |
| `ref` | string | for `scroll_to` | Element reference ID from `read_page` or `find` tools (e.g., "ref_1"). Can substitute for `coordinate` in click actions. |
| `modifiers` | string | no | Modifier keys for clicks: "ctrl", "shift", "alt", "cmd"/"meta", "win"/"windows". Combine with "+" (e.g., "ctrl+shift"). |
| `tabId` | number | **yes** | Tab ID — must be in current group. Use `tabs_context_mcp` first. |
| `save_to_disk` | boolean | no | For screenshot/zoom: save image to disk so it can be attached to a message. Returns saved path. |

### Actions

| Action | Description | Required Params |
|--------|-------------|-----------------|
| `left_click` | Left mouse click | coordinate or ref, tabId |
| `right_click` | Right mouse click (context menus) | coordinate or ref, tabId |
| `double_click` | Double left click | coordinate or ref, tabId |
| `triple_click` | Triple left click | coordinate or ref, tabId |
| `type` | Type text string | text, tabId |
| `screenshot` | Take screenshot of the screen | tabId |
| `wait` | Wait N seconds | duration, tabId |
| `scroll` | Scroll at coordinates | coordinate, scroll_direction, tabId |
| `key` | Press keyboard key(s) | text, tabId |
| `left_click_drag` | Drag from start to end | start_coordinate, coordinate, tabId |
| `zoom` | Capture region for close inspection | region, tabId |
| `scroll_to` | Scroll element into view by ref ID | ref, tabId |
| `hover` | Move cursor without clicking (tooltips, dropdowns, hover states) | coordinate or ref, tabId |

### Notes
- Every action requires `tabId` — call `tabs_context_mcp` first
- Key combos use platform modifier: "cmd" on Mac, "ctrl" on Windows/Linux
- `save_to_disk` returns a file path — only use when sharing the screenshot with the user
- Click positioning tip from system prompt: "cursor tip in the center of the element, don't click edges"

---

## Access Flow: request_access

Before any computer-use action, `request_access` must be called with the list of applications needed. This is a permission handler invoked as `computer:request_access` — it functions like a tool call but goes through the permission system.

### Input

| Param | Type | Description |
|-------|------|-------------|
| `apps` | array | List of applications to request access to |

### Tiered App Access

| App Category | Tier | What's Allowed |
|-------------|------|----------------|
| **Browsers** (Safari, Chrome, Firefox, Edge, Arc, etc.) | `read` | Visible in screenshots, but clicks and typing blocked. Use Chrome MCP instead. |
| **Terminals & IDEs** (Terminal, iTerm, VS Code, JetBrains, etc.) | `click` | Visible and left-clickable, but typing/key presses/right-click restricted. |
| **All other apps** | `full` | Full access — screenshots, clicks, typing, all interactions. |

### Returns

| Field | Type | Description |
|-------|------|-------------|
| `granted` | array | Apps that were approved with their bundleId and tier |
| `denied` | array | Apps that were denied with bundleId and reason ("user_denied") |
| `flags` | object | Permission flags |

### Notes
- User approves each application explicitly via a dialog
- May need to call again mid-task if another app is needed
- Browser tier (`read`) is a hard restriction — use Chrome MCP for browser interaction
- Terminal tier (`click`) blocks typing — use bash tools for terminal interaction

---

## Teach Mode: request_teach_access + teach_step

An interactive walkthrough system for showing users how to do something on their screen. This is the system behind the `chicago` codename (see [feature-flags-and-codenames.md](feature-flags-and-codenames.md)).

**Trigger:** When a user wants to be "walked through" or "shown how" to do something, the model offers a choice between (1) interactive walkthrough and (2) text explanation.

### Flow
1. Call `request_teach_access` — activates teach mode for the session
2. Call `teach_step` repeatedly — each step shows the user what to do
3. The user follows along; teach mode tracks their progress
4. Session ends when user exits or all steps complete

### Internal state
- `teachModeActive` (boolean) — whether teach mode is running
- `teachModeEnteredAt` (timestamp) — when teach mode started
- `cuLockAcquiredAt` (timestamp) — when computer use lock was acquired

### Notes
- These are not standard tool registrations — they're handled by the teach mode subsystem
- `teach_step` returns a Promise that resolves when the user takes action (`{action: "exit"}` etc.)
- Only one pending teach step at a time — a new one auto-resolves the previous as "exit"
- The `isChicagoEnabled` feature flag forces teach mode enabled regardless of GrowthBook config
- `chicagoAutoUnhide` (default: true) and `chicagoUserDeniedBundleIds` (default: []) control teach mode UX

---

## Computer Use Lock System

Computer use has a session-level lock to prevent multiple CU sessions from conflicting:

- Only one session can hold the CU lock at a time
- Lock tracks duration held and whether teach mode was active
- Release triggers include: session end, explicit release, teach mode exit
