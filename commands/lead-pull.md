---
name: lead-pull
description: >
  Pull fresh buying signals from Apollo for the user's ICP. Fetches job changes, funding events, and hiring signals (per the user's setup preferences), filters against the ICP, scores priority, and adds them to the pipeline. Use to refresh the pipeline at the start of a session. Requires the Apollo MCP to be connected — falls back to a clear message if not.
---

# /lead-pull — fetch signals from Apollo

You are pulling fresh signals from Apollo, filtering against the user's ICP, and adding the qualifying ones to the pipeline.

## Step 0: Preflight

Read:
- `${CLAUDE_PLUGIN_ROOT}/skills/lead-engine/references/user-context.md`
- `${CLAUDE_PLUGIN_ROOT}/skills/lead-engine/references/seven-signals.md`
- `${CLAUDE_PLUGIN_ROOT}/skills/lead-engine/references/pipeline.md` (so we don't re-add duplicates)

If `user-context.md` is missing/placeholder: stop, tell user to run `/lead-setup`.

Check available Apollo tools (look for `apollo_*`). If none are available, stop and tell the user:

> "I don't see the Apollo MCP connected, so I can't pull signals automatically. Either connect Apollo (then rerun setup with `/lead-setup`) or capture signals manually with `/lead-capture`."

## Step 1: Read pull preferences from user-context

From `user-context.md`'s Tools / Apollo entry, find which signal sources are enabled:
- Job changes only, OR
- Job changes + funding, OR
- All three (job changes + funding + hiring)

If the file says Apollo is connected but no preference was captured, default to **job changes + funding** and proceed.

## Step 2: Pull each enabled source

### Job changes (when enabled)

Use `apollo_mixed_people_api_search` with filters:
- Roles matching the user's ICP target roles (from `user-context.md`).
- Industry matching ICP industries.
- Company headcount within ICP company size range.
- Filter for "started new role in last 60 days" (Apollo supports this via job-change-recency filters).

Limit: 25 results per pull. We're going for *quality* signals, not a list.

### Funding (when enabled)

Use `apollo_mixed_companies_search` (or `apollo_organizations_enrich` if you have a starter list) with filters:
- Industry matching ICP.
- Headcount within range.
- Funding event in last 90 days.

For each company, then pull the relevant champion contact (matching ICP roles) — usually 1 per company.

Limit: 15 companies per pull.

### Hiring (when enabled)

Use `apollo_organizations_job_postings` filtered to:
- ICP industries + size.
- Job postings that match keywords from the user's offer (you'll need to derive keywords from `user-context.md`'s positioning + offer descriptions). Examples for a CRO consultancy: "Head of CRO", "Conversion Rate Optimization", "Growth Manager".

For each company with a relevant job post, pull a senior champion contact at that company.

Limit: 15 companies per pull.

## Step 3: Dedupe + filter

For each candidate signal:

1. **Dedupe** — check `pipeline.md`. If the same contact already has an active SIG (not `dead` / `booked`), skip. If a SIG exists but is closed, you can re-add only if the new signal is materially different from the closed one.
2. **Disqualify** — apply the disqualifier rules from `user-context.md`. Drop anything that hits a disqualifier.
3. **Score** — assign High / Medium / Low using the rubric in `seven-signals.md` and the user's signal-priority weighting from `user-context.md`. A signal type the user marked "high priority" should bias scoring up.

## Step 4: Append to pipeline

For each surviving signal, append a SIG entry to `pipeline.md` using the same format as `/lead-capture`:

```markdown
---

## SIG-[YYYYMMDD]-[short-slug]

**Captured:** [today, HH:MM]
**Status:** new
**Priority:** [High / Medium / Low]
**Signal type:** [Job change / Funding / Hiring]
**Signal source:** Apollo — [specific detail, e.g., "Joined Acme Co as VP Growth on 2026-04-15"]

**Contact:**
- **Name:** [from Apollo]
- **Title:** [from Apollo]
- **Company:** [from Apollo]
- **LinkedIn:** [from Apollo if available]
- **Email:** [from Apollo if available]

**Signal context:**
[2-3 sentences describing the specific signal. For job changes: "Joined [company] as [role] on [date]. Came from [previous company / role] where they [tenure]." For funding: "[Company] raised $XM Series [X] on [date]. Lead investor: [investor]. Stated use of funds: [if available]." For hiring: "[Company] is hiring [role] (posted [date]). Job description emphasizes [relevant keyword]."]

**Prior history:**
[Skip — Apollo pulls are net-new contacts. If the user wants Gmail/CRM cross-check, they can run `/lead-capture` workflow on the SIG manually.]

**Drafting angle:**
[1-2 sentences. The hook for `/lead-draft` to use.]

**Cadence:**
- Touch 1: [pending] — target send date: [today or tomorrow]
- Touch 2: [pending] — target: +[N] days
- Touch 3: [pending] — target: +[N] days

**Notes:**
Pulled by /lead-pull on [date].
```

Cadence intervals come from `user-context.md` — don't invent them.

## Step 5: Output summary

Don't dump every signal — give a tight summary:

```
✅ Apollo pull complete

  Sources pulled: [job changes, funding, hiring — whichever are enabled]
  Candidates fetched: [N]
  Disqualified: [N]  (e.g., "8 dropped — agency disqualifier")
  Duplicates skipped: [N]
  Added to pipeline: [N]

Priority breakdown:
  🔥 High: [N]   ([list 2-3 names with signal type])
  🟡 Medium: [N]
  🔵 Low: [N]

Top signal of the pull: [SIG-id, name @ company, one-line why]

Next:
  /lead-pipeline                            — see what's queued for today
  /lead-connect SIG-[top high-priority id]  — connection request for the hottest signal
  /lead-warm SIG-[top high-priority id]     — warm before DMing (if already connected)
  /lead-draft SIG-[top high-priority id]    — draft DM directly (skip warming for hot direct-intent signals)
```

For Apollo-pulled signals, almost all contacts will be net-new (not yet connected). Default flow: `/lead-connect` first, then `/lead-warm` after they accept, then `/lead-draft` 24h after warming.

## Edge cases

- **Apollo returns 0 results:** don't fail silently. Tell the user "0 signals matched your filters — try broadening ICP company size, or adding a different industry. Or capture manually with `/lead-capture`."
- **Apollo returns 100+:** signal sourcing is too loose. Tell the user "Apollo returned [N] candidates — way more than expected. Consider tightening ICP filters in `/lead-setup`. I capped at 25 per source for this pull."
- **Apollo MCP throws an error:** show the error verbatim, suggest checking the MCP connection, and offer manual capture as the fallback.

## What this command does NOT do

- Doesn't send DMs.
- Doesn't auto-draft. Drafting is a separate, deliberate step (`/lead-draft`) — keeps the user in control of voice.
- Doesn't push to CRM yet — that happens at `/lead-log sent`. We don't want CRM cluttered with contacts the user might never actually message.
