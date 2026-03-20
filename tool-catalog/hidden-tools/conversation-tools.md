# Conversation Tools (1 tool)

**Status:** Internal only — not callable by users or the assistant. This tool is constructed dynamically by the compaction service and sent to the Anthropic API as part of a separate API call. It is never registered as an MCP tool or deferred tool. This is correct behavior — it is not intended to be user-callable.

**Verified:** 2026-03-19. Not callable from Dispatch, Cowork tasks, code tasks, or Claude Code CLI.

---

## summarize_conversation

Used by the compaction service to create structured summaries when a conversation exceeds context limits.

### Parameters

| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `summary` | string | **yes** | The conversation summary text |
| `entities` | array of strings | no | Key entities mentioned in the conversation |
| `next_action` | string | no | Description of the next action to take |

### How it works

This tool is not called by the user or the assistant directly. It is used internally:

1. The compaction service detects when messages exceed a threshold
2. It calls the Anthropic API with the conversation history
3. The API uses this tool to return a structured summary
4. The summary replaces the old messages, preserving context

### Configuration

The tool's descriptions are configured server-side via a config object with fields:
- `toolDescription` — the tool's own description
- `summaryDescription` / `summary_description`
- `entitiesDescription` / `entities_description`
- `nextActionDescription` / `next_action_description`
- `userPromptTemplate` / `user_prompt_template`

### Notes
- Uses `anthropic-beta` header when calling the API
- Requires an active API token (from OAuth flow)
- The tool definition is constructed dynamically from server config, not hardcoded
- There is an internal counter `WV` (starts at 0) that may track compaction count
- Will silently return null if: messages are empty/too few, no API token, or config is missing
- Uses `maxRetries: 2` for the API call
