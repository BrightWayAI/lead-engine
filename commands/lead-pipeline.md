---
name: lead-pipeline
description: >
  Show the active Lead Engine pipeline. Lists all signals grouped by status, flags what's overdue for follow-up based on the cadence and sent-log, and recommends what to send today (capped at the user's daily DM budget). Use to plan a session — "who should I message right now?"
---

# /lead-pipeline — what to do today

You are giving the user a short, scannable read of their pipeline state and a prioritized list of what to send today.

## Step 0: Preflight

Read:

- `<config-root>/plugins/lead-engine.user-context.md` (for daily DM budget + cadence)
- `<config-root>/plugins/lead-engine.pipeline.md`
- `<config-root>/plugins/lead-engine.sent-log.md`

If `user-context.md` is missing/placeholder: stop, tell user to run `/lead-setup`.
If ``<config-root>/plugins/lead-engine.pipeline.md` has no entries: tell the user "Pipeline is empty. Run `/lead-pull` (Apollo) or `/lead-capture` (manual) to add a signal." and stop.

## Step 1: Compute today's status

Today's date: use the current date.

For each signal in the pipeline, compute:

- **Status** — already in the entry (`new` / `drafted` / `sent` / `replied` / `booked` / `dead`).
- **Last touch date** — most recent entry in `sent-log.md` for this SIG-ID, or `none`.
- **Days since last touch** — today − last touch date.
- **Next touch due** — based on the user's cadence (e.g., "3-7-14"):
  - After Touch 1: due at last_touch + 3 days (or whatever the user set)
  - After Touch 2: due at last_touch + 7 days
  - After Touch 3: cadence done — no next touch
- **Overdue?** — yes if `days since last touch` > the cadence interval AND status is `sent` (not `replied`/`booked`/`dead`).

## Step 2: Group + prioritize

Output in this order. **Skip any group that has zero entries** — don't print empty sections.

### 🔥 Reply-ready (top priority)
Status = `replied` but no follow-up sent yet. The user owes them a response. List these first.

### 📬 Send today
Combine these into a single ranked list:
- New signals (status = `new`) with priority `High`
- Drafted signals (status = `drafted`) ready for Touch 1
- Sent signals where Touch 2 or Touch 3 is due (status = `sent`, overdue per cadence)

Cap at the user's daily DM budget from `user-context.md`. If the list exceeds the cap, truncate and note "[N more queued — not shown to keep within your daily cap]."

Within the cap, sort by:
1. Priority High → Medium → Low
2. Then by signal recency (newer signals first)
3. Then by signal type strength (Direct intent > Engagement > Job change > Funding > Hiring > Stack change > Expansion)

### 🟡 Active — waiting
Sent recently, not yet due for follow-up. Status = `sent`, not overdue. Show count + 3 most recent for visibility, don't list all.

### 📥 New — not yet drafted
Status = `new`, priority Medium or Low (Highs already in "Send today"). Show count.

### 🪦 Recently closed
Status = `booked` or `dead` from the last 7 days. Show count + names.
For booked entries with no brief generated yet (no file at `references/briefs/SIG-[id].md`), prompt: "Brief not yet generated — `/lead-brief SIG-[id]`."

### 🌡️ Warming / awaiting connection
Signals that have a `**Warming:**` or `**Connection request:**` field on the entry but the DM hasn't been sent yet. Include here so the user remembers to follow through.
- "Warmed [N days ago] — DM ready: `/lead-draft SIG-[id]`"
- "Connection request sent [N days ago] — check accept status, then warm or draft."

## Step 3: Output format

Use this layout. Be tight — this is a dashboard, not a report:

```
# Intent pipeline — [today's date]

🔥 Reply-ready ([N])
  • SIG-[id] · [name] @ [company] — [one-line: what they replied with]
    → /lead-draft SIG-[id]   (or just respond manually and /lead-log when sent)

📬 Send today ([N] of [budget] daily slots)

  Touch 1 — openers ([N]):
  • SIG-[id] · [name] @ [company] · [signal type] · [priority]
      "[1-line drafting angle from pipeline entry]"
      → /lead-draft SIG-[id]

  Touch 2 — follow-ups ([N]):
  • SIG-[id] · [name] @ [company] · sent [N] days ago · [priority]
      → /lead-draft SIG-[id]

  Touch 3 — breakups ([N]):
  • SIG-[id] · [name] @ [company] · sent [N] days ago
      → /lead-draft SIG-[id]

[If truncated:] [+N more queued — surfaced tomorrow]

🟡 Active — waiting ([N])
  Most recent: [name], [name], [name]

📥 New — not yet drafted ([N])
  [Med/Low priority — promote anytime by editing pipeline.md status, or wait for them to age]

🪦 Closed last 7 days ([N booked], [N dead])
  Booked: [names]

— — —

Today's call:
[1-2 sentence recommendation. E.g., "You've got 3 reply-ready and 2 hot openers. Hit replies first — those are the warmest leads in the pipeline. Then the openers."]
```

## Step 4: If pipeline is empty for "Send today"

If both `Reply-ready` and `Send today` are empty, end with:

"Nothing queued for today. Want to pull fresh signals? `/lead-pull` (Apollo) or `/lead-capture` (manual)."

Don't fluff with extra sections.

## Edge cases

- **Cadence done, no reply:** if a signal has 3 touches sent and `days since last touch` > 21, recommend marking it `dead` so it stops cluttering the pipeline. Suggest: "Run `/lead-log SIG-[id] dead` to close it out."
- **Replied but no follow-up logged in 5+ days:** flag in 🔥 with extra emphasis — "stale reply, may need to triage."
- **`drafted` for 3+ days without being sent:** nudge — "Drafted but not sent — drafts go cold. Send today or rewrite."
