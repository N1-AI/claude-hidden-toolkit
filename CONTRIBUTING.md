# Contributing to Claude's Hidden Toolkit

Thanks for your interest in contributing! This project documents Claude.ai's undocumented internal tools — every contribution helps the community understand what's really happening under the hood.

Two external contributors have already shaped v1.4: an anonymous security researcher (11 findings) and DMontgomery40 (iOS Reminders suite). Your discoveries are welcome too.

## How to Contribute

### Reporting New Tools or Changes

If you've discovered a new tool, a schema change, or platform-specific behavior not covered in the book:

1. **Open an Issue** with the label `discovery`
2. Include:
   - Tool name (exact internal name if known)
   - Platform tested on (browser / desktop app / Android / iOS)
   - How you discovered it (e.g., `tool_search` query, system prompt extraction, behavioral observation)
   - Parameter schema if available (JSON)
   - Response format with a real example
   - Any gotchas or edge cases observed

### Correcting Existing Documentation

If something in the book is wrong or outdated:

1. **Open an Issue** with the label `correction`
2. Include:
   - Which tool card or chapter section is affected
   - What the book currently says
   - What you observed instead
   - Platform and date of observation

### Submitting a Pull Request

For direct content changes:

1. Fork the repo
2. Edit `Claude_Hidden_Toolkit.md` only — this is the source of truth
3. Follow the existing formatting conventions (see below)
4. Submit a PR with a clear description of what changed and how it was verified

## Formatting Conventions

- **Tool names** in backticks: `web_search`, `event_create_v0`
- **Platform availability** stated on every claim — distinguish Android from iOS where relevant
- **Tool cards** follow the template in Appendix C — don't freestyle the structure
- **Figures** are numbered sequentially. If adding figures, continue the sequence; never reuse or skip numbers
- **Response formats** marked as confirmed with real output. If you can't confirm, note it explicitly
- **Tables** for structured comparisons, not bullet lists

## Verification Standard

This project has a strict empirical standard. We don't document tools based on speculation, hearsay, or what "should" work. Every claim must be:

- **Tested** on at least one platform with a real Claude.ai session
- **Reproducible** — another user should be able to trigger the same behavior
- **Platform-tagged** — specifying exactly where it works and where it doesn't

If you've observed something but can't fully verify it, open an Issue rather than a PR. The community can help confirm.

## What We're Looking For

High-value contributions include:

- **Android-specific tools** we might be missing
- **Team/Enterprise-exclusive tools** — we have no coverage of enterprise-tier features
- **Screenshot captures** for figures currently marked [Upcoming]
- **Cross-platform verification** of existing tool cards on platforms we haven't tested
- **New deferred tools** discovered via `tool_search` sweeps
- **Schema changes** from Anthropic updates that break or modify existing tools
- **MCP Connector behavior** differences — especially new connectors beyond Gmail/Calendar/Drive
- **Ghost tool activation** — monitoring `gmail_send_draft` and `gmail_modify_thread` for deployment
- **Artifact execution layer** findings — new capabilities in `anthropic_api_in_artifacts` or `persistent_storage`

## Contributor Credits

All contributors are credited in the book and README. If you prefer to remain anonymous, let us know — we respect privacy (see our existing anonymous contributor as an example).

## Code of Conduct

Be respectful, constructive, and evidence-based. This is a technical documentation project — claims need proof, disagreements are settled by testing, and everyone's contributions are valued.

## License

By contributing, you agree that your contributions will be licensed under [CC BY 4.0](LICENSE), the same license as the rest of the project.
