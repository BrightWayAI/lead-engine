# Security Policy

## What this plugin does with your data

Lead Engine drives intent-based LinkedIn outbound. It reads from connectors you've authorized in Cowork (or Claude Code) and writes to local plugin reference files plus your CRM.

**Reads:**
- **CRM** (HubSpot / Salesforce / Pipedrive / etc., per `/lead-setup`) — contact records, deals, owners, custom properties.
- **Apollo MCP** (if connected, per `/lead-setup`) — job-change / funding / hiring signals matching your ICP.
- **Gmail** (if connected) — recent threads with a contact, used for prior-touch context during `/lead-capture` and `/lead-brief`.
- **Web** (`WebSearch` + web fetch) — public signals (LinkedIn activity, press, funding) during `/lead-brief`.
- **Local plugin references** — `references/user-context.md`, `pipeline.md`, `sent-log.md`, `seven-signals.md`, `voice-rules.md`.
- **Shared user-level config** — `~/Documents/Claude/identity.md` and `~/Documents/Claude/voice.md` (read-only).

**Writes:**
- **Plugin references** — `pipeline.md` (signal entries), `sent-log.md` (touch history), `references/briefs/SIG-*.md` (pre-call briefs), `references/user-context.md` (after `/lead-setup`).
- **CRM** — when `/lead-log` is invoked, creates contact + engagement records, updates lifecycle stage. Always with explicit user invocation; never silently.

**Does not:**
- **Send DMs, comments, or connection requests on LinkedIn** — LinkedIn restricts automation; this plugin produces copy-paste-ready text only. The user pastes into LinkedIn manually.
- **Send emails on your behalf** — drafts only; user reviews and sends.
- **Modify CRM contacts unprompted** — only `/lead-log` writes to the CRM, and only on explicit invocation.
- **Send data to any server outside your authorized connectors** — every read/write goes through Cowork/Claude Code's connector framework.

## Where data lives

- Plugin reference files inside the installed plugin directory (Cowork manages the location).
- Briefs at `references/briefs/SIG-*.md`.
- Shared identity/voice (read-only) at `~/Documents/Claude/`.
- CRM data lives in your CRM (this plugin does not duplicate it).

## What gets sent off your machine

- Whatever your authorized CRM, Apollo, Gmail, and WebSearch connectors send when invoked. Those services have their own privacy policies. This plugin does not introduce additional outbound traffic.

## Supported versions

| Version | Supported |
|---------|-----------|
| 0.1.x   | Yes       |

## Reporting a vulnerability

If you discover a security issue with this plugin (e.g., a code path that could exfiltrate data, write outside expected reference files, or misuse a connector beyond its documented scope), please report it **privately** via GitHub Security Advisories:

https://github.com/BrightWayAI/lead-engine/security/advisories/new

Do **not** open a public issue for security concerns. We aim to respond within 5 business days.
