# Lead Engine

Stop cold spray-and-pray. Turn your LinkedIn network into a *warm-lead engine*: catch buying signals, warm prospects through comments and connection requests, send DMs that reference what they actually just did, and run the follow-up cadence on rails.

Cold lists average 1–2% reply rates and ~100 messages per booked meeting. Signal-driven outreach hits 25–40% reply rates at ~10 messages per meeting. The unlock isn't the message — it's the *timing*.

This plugin runs the workflow:

```
spot a signal  →  warm them  →  send the connection request
              →  send the DM  →  follow up  →  reply / book  →  log to CRM
```

## Install

Drop the plugin folder into your Claude Code plugins directory (or install via your marketplace). Then run:

```
/lead-setup
```

That kicks off a one-time interview — your company, ICP, voice, value-adds, signal preferences, and CRM/Apollo wiring. Your answers are saved into the plugin's reference files so every future draft is personalized.

## Commands

| Command            | What it does                                                                                                       |
|--------------------|---------------------------------------------------------------------------------------------------------------------|
| `/lead-setup`      | One-time interview. Captures your profile, ICP, voice, value-adds, banned phrases, CRM/Apollo wiring, cadence prefs. |
| `/lead-pull`       | Pull fresh signals from Apollo (job changes, funding, hiring) for your ICP. Adds them to your pipeline.              |
| `/lead-capture`    | Manually log a signal you spotted on LinkedIn (post, comment, job change, etc.). Classifies + scores it.             |
| `/lead-warm`       | Pre-DM warming sequence: like 2 of their posts, draft a substantive comment for one, queue the DM for 24h later.     |
| `/lead-connect`    | Draft the LinkedIn connection-request copy (lighter than the DM — peer-level hello).                                 |
| `/lead-draft`      | Draft the 3-touch DM sequence (opener + 2 follow-ups) for a captured signal, in your voice.                          |
| `/lead-pipeline`   | Show what's active, what's overdue, what to send today.                                                              |
| `/lead-log`        | Record that you sent / they replied / they booked / it's dead. Updates pipeline + pushes to CRM.                     |
| `/lead-brief`      | Generate a pre-call brief once a meeting books: contact research, talking points, likely objections.                 |

## What it depends on

- **Required:** nothing besides Claude Code and this plugin. You can run the manual workflow with zero connectors.
- **Recommended:**
  - **Apollo MCP** — feeds `/lead-pull` with job-change / funding / hiring signals for your ICP. Without it, you capture signals manually.
  - **HubSpot (or other CRM) MCP** — `/lead-log` writes contacts and engagements to your CRM. Without it, the plugin keeps a local pipeline only.
  - **Gmail MCP** — used during `/lead-capture` and `/lead-brief` to check for prior correspondence.
  - **Web search** — `/lead-brief` uses WebSearch + WebFetch for pre-call research.

The setup command checks which of these you have and adapts.

## The contact-researcher subagent

Lead Engine ships with a subagent called `contact-researcher` that does single-contact deep dives across CRM, email, calendar, and the public web. It's used internally by `/lead-brief` (for the dossier section) and `/lead-pull` (for top-N enrichment after Apollo), and it's exposed for other plugins in the [BrightWay AI marketplace](https://github.com/BrightWayAI/claude-plugins) to delegate to:

- **bizdev-outreach** uses it for Phase 1 research before drafting.
- **weekly-outreach** uses it to deepen the top 3–5 highest-priority contacts each week.
- Any custom skill or command can invoke it via the Task tool with `subagent_type="contact-researcher"`.

The agent returns a structured dossier (Contact Snapshot / Relationship History / Recent Public Signals / Three Talking Points / Suggested Next Step / Confidence) — consistent shape regardless of caller. See [`agents/contact-researcher.md`](agents/contact-researcher.md) for the full spec.

## Companion plugins

Pairs naturally with:

- **[claude-cortex](https://github.com/BrightWayAI/claude-cortex)** — captures memory of past signals and replies; surfaces relevant context when you mention a contact mid-conversation.
- **[bizdev-outreach](https://github.com/BrightWayAI/Biz-Dev)** — for ad-hoc per-contact drafting outside the LinkedIn signal pipeline.
- **[weekly-outreach](https://github.com/BrightWayAI/weekly-outreach)** — for non-LinkedIn-channel weekly BD prep using the same `contact-researcher` subagent.
- **[core-ops](https://github.com/BrightWayAI/core-ops)** — provides `pipeline-analyst` for ranking pipeline contacts by recency × signal strength.

## What it does NOT do

LinkedIn actively blocks scraping and DM automation. **This plugin does not click-send DMs, comments, or connection requests for you.** It produces copy-paste-ready messages and the timing logic. You paste them into LinkedIn yourself. The "engine" is the *signal sourcing + warming + drafting + cadence*, not the act of sending.

If you want hands-off lead pulling at LinkedIn-Sales-Navigator scale, you need a third-party tool (Clay, PhantomBuster, etc.) feeding signals in. Out of scope here.

## The 7 signals

The plugin tracks seven categories of buying intent:

1. **Engagement** — they liked or commented on a post about a problem you solve
2. **Job change** — new role at an ICP company in the last 60 days
3. **Funding** — their company raised in the last 90 days
4. **Hiring** — they're posting roles that imply your problem space
5. **Growth/expansion** — opening a new office, market, or product line
6. **Tech-stack change** — switching vendors, adopting/dropping tools
7. **Direct intent** — asking for recommendations, posting about a problem

Each captured signal gets classified, scored against your ICP, and queued.

## The flow (what a typical signal looks like)

```
1. /lead-pull or /lead-capture                   →  SIG-X created in pipeline
2. /lead-warm SIG-X                              →  comment + 2 likes drafted, DM queued for tomorrow
3. /lead-connect SIG-X (if not connected yet)    →  connection-request copy drafted
4. /lead-draft SIG-X                             →  opener + 2 follow-ups drafted in your voice
5. /lead-log SIG-X sent 1                        →  Touch 1 logged, Touch 2 target date set
6. /lead-log SIG-X reply                         →  reply logged, response drafted
7. /lead-log SIG-X booked                        →  meeting captured, lifecycle stage updated in CRM
8. /lead-brief SIG-X                             →  pre-call brief generated
```

Not every signal goes through every step — warm contacts can skip warming, and existing connections skip the connection-request step.

## Rollout

The plugin includes a 4-week ramp plan in `skills/lead-engine/references/rollout-plan.md`. Read it before you start — it's the difference between "shipped 50 DMs and got nothing" and "shipped 50 DMs and booked 8 calls."

## Update

Run `/lead-setup` again any time. It re-uses your existing answers as defaults and only writes what changed.
