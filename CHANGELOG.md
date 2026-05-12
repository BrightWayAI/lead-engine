# Changelog

All notable changes to lead-engine are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versions match `plugin.json`.

## [0.2.4] — contact-researcher persists dossier as cortex person page (2026-05-12)

### Added
- **`contact-researcher` agent now writes a cortex person page** alongside returning the dossier (graduation trigger #1 from cortex's CLAUDE.md schema). First run creates `<config-root>/memory/person/<slug>.md` using cortex's person-page schema, pre-filled from the dossier — Identity, Relationship temperature inferred from history, first Recent interactions line, Notes seeded from talking points and suggested next step. Subsequent runs update Identity fields where clearly fresher, append a new Recent-interactions line, and additively grow Notes — never overwriting prior content.
- **Name-collision disambiguation.** If two people would slug to the same name (different company / email), the agent surfaces the conflict and recommends appending a company hint (`<slug>-<company-slug>.md`).
- **Graceful degradation.** Skips silently if cortex isn't installed. The dossier still returns as the primary output; cortex persistence is a side-effect, not a precondition.

### Why this matters
Phase 3 of SECOND-BRAIN-V2-SPEC. Repeating contact-research on the same person was previously a from-scratch synthesis every time. Now the second and subsequent runs update an existing canonical page, making the agent faster and the person's history queryable from cortex without going back through this plugin.

## [0.2.3] — Platform-agnostic Step 0 (2026-05-12)

### Changed
- **Setup command Step 0 now platform-agnostic.** Every `request_cowork_directory(...)` call is conditional: "In Cowork, call `request_cowork_directory(...)`. In Claude Code (or any environment with direct filesystem access), no mount is needed." Same plugin source works in both runtimes.

### Why this matters
Phase 0 of SECOND-BRAIN-V2-SPEC. Removes the implicit Cowork-only assumption so Claude Code users do not hit unsupported tool calls during setup.

## [0.2.0] — Config-root refactor + contact-researcher

### Changed (config-root refactor)
- **All writable plugin data moved to a user-chosen folder.** User-context, pipeline, sent-log, and pre-call briefs were previously stored under the plugin's source folder (which Cowork mounts read-only). Writes failed silently. They now live under `<config-root>/plugins/lead-engine.*`, where `<config-root>` is the folder the user chooses on first plugin setup (recorded at `~/Documents/.claude-plugin-config-root`):
  - `<config-root>/plugins/lead-engine.user-context.md` (was `references/user-context.md`)
  - `<config-root>/plugins/lead-engine.pipeline.md` (was `references/pipeline.md`)
  - `<config-root>/plugins/lead-engine.sent-log.md` (was `references/sent-log.md`)
  - `<config-root>/plugins/lead-engine.briefs/SIG-*.md` (was `references/briefs/`)
- **Read-only plugin reference files stay where they are** — `references/seven-signals.md`, `references/voice-rules.md`, `references/rollout-plan.md` continue to load from the plugin's source folder (they don't need to be writable).
- **`/lead-setup` gets Step 0** — resolves the config root via the pointer; prompts for it on first run; reads shared identity (`<config-root>/identity.md`) and voice (`<config-root>/voice.md`); offers to migrate legacy `~/Documents/Claude/identity.md`/`voice.md` and any pre-staged plugin configs.
- **All `/lead-*` commands updated** to read/write the new paths.
- **User-facing prompts and skill descriptions debranded** for fork-friendliness.

### Added (previously in-flight)
- `contact-researcher` subagent — single-contact deep dive across CRM, email, calendar, web. Returns a structured dossier (Contact Snapshot / Relationship History / Recent Public Signals / Three Talking Points / Suggested Next Step / Confidence). Used internally by `/lead-brief` and `/lead-pull`, exposed for other plugins to delegate to.
- `/lead-brief` and `/lead-pull` now delegate to `contact-researcher` with confidence-aware gating — pause on Low confidence and ask the user for context before proceeding.

## [0.1.0] — Initial release

### Added
- Intent-based LinkedIn outbound system with seven slash commands: `/lead-setup`, `/lead-pull`, `/lead-capture`, `/lead-warm`, `/lead-connect`, `/lead-draft`, `/lead-pipeline`, `/lead-log`, `/lead-brief`.
- The 7-signal taxonomy (engagement, job change, funding, hiring, growth/expansion, tech-stack change, direct intent) for classifying buying signals.
- The 27-word opener pattern for first-touch DMs.
- Apollo MCP integration for pulling job-change / funding / hiring signals at ICP scale.
- HubSpot (or other CRM) integration via `/lead-log` for pipeline + lifecycle stage tracking.
- Voice-faithful drafting using user-context-captured banned phrases and 3-touch cadence logic.
- Reference files: `seven-signals.md`, `voice-rules.md`, `pipeline.md`, `sent-log.md`, `rollout-plan.md`.
