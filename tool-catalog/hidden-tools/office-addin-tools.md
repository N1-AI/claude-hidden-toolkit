# Office Add-in Tools (5 tools)

**Status:** UNTESTED — requires Microsoft Office (Excel/Word/PowerPoint) with the Claude add-in connected. `tool_search` returns nothing for "office", "workbook", or "addin" in any session type, indicating these tools are only registered when the Office add-in WebSocket bridge is active.

**Verified:** 2026-03-19. Could not test (no Office installed). Schemas documented from binary extraction only.

Tools for direct Excel/Word/PowerPoint integration via the Claude add-in panel. Requires the Office add-in to be installed and connected to Claude Desktop.

**Manifest URL:** `https://pivot.claude.ai/manifest.xml`
**Add-in UUID:** `29673e3c-d826-4f00-92ee-162334a52b1a`
**Module ID:** `office-addin`
**OAuth scope (when enabled):** `user:inference user:office`

---

## open_office_file

Opens an Office file in the default application and automatically opens the Claude add-in panel.

On macOS, the add-in is opened using the Accessibility API (requires Accessibility permissions). On Windows, the add-in auto-opens via manifest configuration.

After opening, use `office_addin_run` or `office_addin_get_context` to interact with the file.

### Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `path` | string | **yes** | Absolute path to the Office file to open |

### Supported file types
- `.xlsx` → Microsoft Excel
- `.docx` → Microsoft Word
- `.pptx` → Microsoft PowerPoint

### Notes
- macOS requires Accessibility permissions for the add-in panel auto-open
- The app-to-application mapping is hardcoded
- Throws `Unknown Office extension` for any other file type
- The tool description mentions `office_addin_task` as an alternative to `office_addin_run` — this name appears in the description text but has no separate tool registration

---

## close_office_file

Closes an Office file that is currently open.

### Parameters

| Param | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `path` | string | **yes** | — | Absolute path to the Office file to close |
| `save_changes` | boolean | no | `true` | If true, saves changes before closing. If false, discards changes. |

### Notes
- Only works with `.xlsx`, `.docx`, `.pptx`
- The file must actually be open in the corresponding Office application
- Default is to save

---

## office_addin_run

Execute Office.js code directly in Excel. Changes appear instantly in the document.

The code runs inside `Excel.run()` with `context` available.

### Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `code` | string | **yes** | Office.js code to execute. Has `context` object available. Must call `await context.sync()` to apply changes. |
| `display_message` | string | no | Brief user-friendly action label shown in the add-in UI (e.g., "Calculate sum", "Create chart") |
| `workbook` | string | no | Target workbook ID (addinId). If not specified, uses the currently selected workbook. Use `list_connected_workbooks` to see available workbooks. |

### Code examples from description

Write to cell:
```javascript
context.workbook.worksheets.getActiveWorksheet().getRange("A1").values = [["Hello"]];
await context.sync();
```

Read data:
```javascript
const range = context.workbook.worksheets.getActiveWorksheet().getRange("A1:B10");
range.load("values");
await context.sync();
range.values; // Returns the data
```

### Notes
- **Must call `await context.sync()`** after making changes
- User must have the Excel add-in open and connected to Claude Desktop
- Currently only described for Excel, even though `open_office_file` supports Word and PowerPoint
- The `display_message` shows in the add-in UI as a status indicator

---

## office_addin_get_context

Fetch current spreadsheet context: selection, sheets, recent changes, and add-in conversation history.

### Parameters

| Param | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `include_changes` | boolean | no | `true` | Include user change events (edits, inserts, deletes, etc.) |
| `include_conversation` | boolean | no | `true` | Include the add-in conversation history |
| `include_selection` | boolean | no | `true` | Include current cursor/selection and its values |
| `include_sheets` | boolean | no | `true` | Include sheet metadata (names, dimensions, frozen rows/cols) |
| `max_changes` | number | no | `100` | Maximum number of change events to return |
| `max_messages` | number | no | `50` | Maximum number of conversation messages to return |
| `workbook` | string | no | — | Target workbook ID (addinId). If not specified, uses currently selected workbook. |

### Returns
- Current selection address and values
- All sheet names and metadata
- Recent user edits, inserts, deletes
- Prior conversation with the add-in Claude

### Notes
- The "add-in conversation" is a separate conversation the user may have had with the Claude embedded in Excel — this provides context on what was discussed
- All includes default to `true` — pass `false` to reduce response size

---

## list_connected_workbooks

List all Excel workbooks currently connected via the Claude add-in.

### Parameters

None.

### Returns

Connected workbooks with their IDs, document names, and connection details. Use the returned workbook IDs to target specific workbooks in `office_addin_run` and `office_addin_get_context`.

### Notes
- Returns `addinId` values used as the `workbook` parameter in other tools
- Multiple workbooks can be connected simultaneously
- Connection is via WebSocket (`wss://localhost:8766` or configured `OFFICE_ADDIN_BRIDGE_URL`)
- If no workbooks are connected, the other tools default to "currently selected" which may fail

---

## Architecture

The Office add-in system uses:
1. **WebSocket bridge** — connects Claude Desktop to the Excel add-in running inside Office
2. **OAuth authentication** — uses the user's Claude token with `user:office` scope
3. **Office.js API** — code runs in Excel's JavaScript runtime via `Excel.run()`
4. **Accessibility API** (macOS) — used to auto-open the add-in panel when opening files
5. **Profile API** — fetches user's account UUID from `{apiHost}/api/oauth/profile`
