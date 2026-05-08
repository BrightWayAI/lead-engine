# Changelog

All notable changes to lead-engine are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versions match `plugin.json`.

## [Unreleased]

### Added
- `contact-researcher` subagent — single-contact deep dive across CRM, email, calendar, web. Returns a structured dossier (Contact Snapshot / Relationship History / Recent Public Signals / Three Talking Points / Suggested Next Step / Confidence). Used internally by `/lead-brief` and `/lead-pull`, exposed for other plugins to delegate to.
- `/lead-brief` and `/lead-pull` now delegate to `contact-researcher` with confidence-aware gating — pause on Low confidence and ask the user for context before proceeding.
- Setup reads `~/Documents/Claude/identity.md` (cortex's `/setup-identity`) and `~/Documents/Claude/voice.md` (cortex's `/setup-voice`) when available; skips identity/voice questions in the interview.

## [0.1.0] — Initial release

### Added
- Intent-based LinkedIn outbound system with seven slash commands: `/lead-setup`, `/lead-pull`, `/lead-capture`, `/lead-warm`, `/lead-connect`, `/lead-draft`, `/lead-pipeline`, `/lead-log`, `/lead-brief`.
- The 7-signal taxonomy (engagement, job change, funding, hiring, growth/expansion, tech-stack change, direct intent) for classifying buying signals.
- The 27-word opener pattern for first-touch DMs.
- Apollo MCP integration for pulling job-change / funding / hiring signals at ICP scale.
- HubSpot (or other CRM) integration via `/lead-log` for pipeline + lifecycle stage tracking.
- Voice-faithful drafting using user-context-captured banned phrases and 3-touch cadence logic.
- Reference files: `seven-signals.md`, `voice-rules.md`, `pipeline.md`, `sent-log.md`, `rollout-plan.md`.
