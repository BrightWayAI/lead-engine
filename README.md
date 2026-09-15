# lead-engine — DEPRECATED

> **This plugin has been folded into [`relationships`](https://github.com/BrightWayAI/relationships) as of 2026-09-15 (relationships v0.3.0).**

The Apollo-driven signal pipeline now lives inside the relationships plugin, as separate commands (not collapsed into one flagged command — the per-touch nuance in warming, connection requests, and drafting was worth keeping distinct):

- `/pull-signals` — was `/lead-pull`
- `/capture-signal` — was `/lead-capture`
- `/connect-signal` — was `/lead-connect`
- `/warm-signal` — was `/lead-warm`
- `/draft-signal` — was `/lead-draft`
- `/pre-call-brief` — was `/lead-brief`
- `/touchpoint SIG-[id] sent|reply|booked|dead` — was `/lead-log`
- `/setup-relationships` — absorbs `/lead-setup`'s ICP, signal-priority, and Apollo config questions natively

The `contact-researcher` subagent moved to `relationships/agents/`.

## Migration

**Existing users:** install `relationships` if you haven't already, then run `/setup-relationships` — it detects your legacy `lead-engine.user-context.md` and offers to carry the ICP/Apollo/signal config forward instead of re-asking. Your existing pipeline (`lead-engine.pipeline.md`, `lead-engine.sent-log.md`) can be copied to `<config-root>/relationships/pipeline.md` and `sent-log.md`.

**New installs:** install `relationships` directly — this repo is archived and won't receive updates.

See the [relationships CHANGELOG](https://github.com/BrightWayAI/relationships/blob/main/CHANGELOG.md) v0.3.0 entry for the full command mapping.
