# CLAUDE.md — Instructions for Claude Code

## Project Overview

This is "Claude's Hidden Toolkit" — a living technical book documenting Claude.ai's undocumented internal tools. The book reverse-engineers 28 tools across three platforms (browser, desktop app, mobile app) with confirmed schemas, response formats, and behavioral patterns.

## Repository Structure

```
Claude_Hidden_Toolkit.md   # The book (source of truth — all edits here)
README.md                  # GitHub landing page
CLAUDE.md                  # You are here
AGENTS.md                  # General AI agent context
CHANGELOG.md               # Edition history with detailed changes
CITATION.cff               # Academic citation metadata
CONTRIBUTING.md            # Contribution guidelines
SECURITY.md                # Responsible disclosure policy
LICENSE                    # CC BY 4.0
```

## Key Rules

1. **The .md file is the source of truth.** All content edits happen here.
2. **All tool claims must be empirically verified.** No speculative schemas. If you haven't tested it, mark it as unverified.
3. **Platform matters.** Every tool claim must specify which platform(s) it applies to: browser, desktop app, mobile app, or all.
4. **Figure numbering is sequential** (fig_01 through fig_22). If adding figures, continue the sequence. Never use letter suffixes (e.g., fig_14b).
5. **Tool cards follow a strict template.** See Appendix C in the book for the template. Every card needs: quick facts table, overview, parameters table, example call with JSON, "When Claude Uses It" table, "Watch Out" section, and platform availability table.

## Writing Style

- Technical but accessible — assume the reader is a developer or power user
- No hedging without evidence — say "confirmed" or "not tested", never "probably"
- Use backtick formatting for all tool names, parameter names, and code
- Tables over bullet lists for structured comparisons
- Blockquotes (>) for figure placeholders and warnings

## Tool Naming Conventions

- Always-loaded tools: `web_search`, `memory_user_edits`, etc.
- Deferred tools (mobile only): `user_time_v0`, `alarm_create_v0`, etc.
- Version suffixes: `_v0` = initial, `_v1` = breaking change upgrade
- Unversioned tools are considered stable

## What Not To Do

- Don't add tools without cross-platform verification
- Don't change the figure numbering scheme without renumbering all references
- Don't remove the [Upcoming] tags on figures — they indicate screenshots pending capture
- Don't merge the .md living repository links with personal GitHub links — they serve different purposes
