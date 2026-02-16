# Security Policy

## Scope

This project documents Claude.ai's internal tools through reverse-engineering. While the book itself is documentation (not executable code), the subject matter touches on AI system internals, and discoveries may occasionally surface behaviors with security implications.

## Reporting a Vulnerability

If you discover a tool behavior that could be exploited to:

- Bypass Claude's safety guardrails
- Access other users' data through shared storage or memory tools
- Exfiltrate sensitive information via tool parameters or responses
- Manipulate tool behavior through prompt injection in tool inputs
- Escalate permissions beyond intended scope

**Please report it responsibly:**

1. **Do NOT open a public Issue.** Security-relevant findings should not be disclosed publicly before they can be addressed.
2. **Email:** Contact the maintainer directly at the email listed on the [author's GitHub profile](https://github.com/frmoretto).
3. **Include:**
   - Description of the behavior
   - Steps to reproduce
   - Platform(s) affected
   - Potential impact assessment
4. **Also consider reporting to Anthropic directly** via their [responsible disclosure process](https://www.anthropic.com/responsible-disclosure-policy) — they are best positioned to fix tool-level vulnerabilities.

## What This Project Will Do

- Acknowledge receipt within 48 hours
- Assess whether the finding should be withheld from publication until Anthropic addresses it
- Coordinate with Anthropic if appropriate
- Credit the reporter (unless anonymity is requested) when the finding is eventually published

## What This Project Will NOT Do

- Publish exploit details before Anthropic has had reasonable time to respond
- Document tools or behaviors specifically designed to facilitate harm
- Provide instructions for bypassing safety systems

## Supported Versions

| Version | Supported |
|---------|-----------|
| 1.3     | Yes       |
| < 1.3   | No — please reference the latest edition |
