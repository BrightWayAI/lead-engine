---
name: lead-setup
description: >
  Set up or update your Lead Engine plugin profile. Walks the user through a friendly interview about their company, ICP, voice, value-adds, signal preferences, and CRM/Apollo wiring, then saves it all to the plugin's reference files. Run this on first install or whenever your positioning changes.
---

# Lead Engine — Setup

You are running the onboarding flow for the Lead Engine plugin. Your job is to collect the user's information through a friendly, conversational series of questions, then write it to the user-context file the rest of the plugin reads from.

**Important:**
- Ask ONE question at a time. Wait for each answer before asking the next.
- Use **AskUserQuestion** for every question. Multiple-choice questions go in as choices; free-form questions use the freeform option.
- Be conversational — a quick intake call, not a form.
- If a `user-context.md` already exists at the path below, **read it first** and present each existing answer as the default. The user can confirm or override. This is "update mode."

## Pre-step: Check shared identity

Before any other steps, check whether `~/Documents/Claude/identity.md` exists. This is a shared identity file populated by cortex's `/setup-identity` command — every BrightWayAI marketplace plugin reads it.

- **If it exists and is populated:** read it. Use the values to pre-fill Q1 (Company name), Q2 (Company website), and any role/identity follow-ups. Skip those questions; just confirm what you read. The auto-research step still runs to enrich Q3 (positioning) and Q4 (products).
- **If it doesn't exist:** mention to the user:
  > "Heads up — there's a shared identity file (`/setup-identity` in cortex) that other plugins read too. Capture name/company/role/tools once and every plugin uses it. Want to run `/setup-identity` first (recommended, ~2 min), or proceed inline?"
  - If user picks "/setup-identity first," route there, then return to this setup.
  - If "inline," proceed.

## Pre-step 2: Check shared voice

After identity, check whether `~/Documents/Claude/voice.md` exists. This is a shared writing-voice file populated by cortex's `/setup-voice` command — used by every drafting plugin (lead-engine for DMs, bizdev-outreach for outreach, weekly-outreach for weekly BD, news-curator's post-assembler) so voice stays consistent.

- **If it exists and is populated:** read it. Use those values to pre-fill voice-related questions in this interview (banned phrases, tone, sign-off). Skip those; just confirm. Plugin-specific voice rules (the 27-word opener pattern, signal-tied openers) stay in this plugin's references.
- **If it doesn't exist:** offer:
  > "Want to capture your writing voice once via `/setup-voice` (in cortex)? Every drafting plugin reads it — voice stays consistent across channels. Run it now (~5 min) or proceed inline here?"
  - "Run /setup-voice first" → route there, then resume.
  - "Inline" → proceed.

---

## Step 0: Check for existing profile

Read `${CLAUDE_PLUGIN_ROOT}/skills/lead-engine/references/user-context.md`. If it exists with real content (not the placeholder), tell the user:

"Looks like you've set this up before — I'll show you each existing answer and you can press through (keep) or update it."

Then skip to Step 1 in update mode.

## Step 1: Company basics

### Q1 — Company name
"Let's get your Lead Engine profile dialed in. First — what's your company name?"

### Q2 — Company website
"What's your company's website? I'll pull what I can to pre-fill the next few answers."

### Auto-research (silent — do this before Q3)

Once you have the website:
1. **WebFetch** the homepage and `/about` (or `/about-us`).
2. **WebSearch** for "[company name] recent news" and "[company name] product".
3. Use what you find to draft answers for Q3 (positioning) and Q4 (products) so the user can confirm/edit instead of writing from scratch.

If the site is unreachable, just ask Q3 and Q4 normally without drafts.

### Q3 — Positioning statement
"Give me a one-sentence positioning: 'We help [who] do [what] by [how].' Don't overthink it."

If you have a draft from auto-research: "Based on your site, this looks close — does it ring true, or want to phrase it differently? Draft: '[draft]'"

### Q4 — Products / services
"What are your 1–3 main offers? Name + one-line description for each. Example:
- **Acme Audit**: 30-day diagnostic that finds CRO gaps in DTC checkout flows
- **Acme Build**: 90-day implementation engagement"

### Q5 — Average deal size + sales cycle
"Roughly — what's an average deal size and sales cycle length? (Helps me calibrate how much context to gather per signal. A $5K SMB deal warrants less digging than a $250K enterprise one.)"

## Step 2: Ideal customer profile

### Q6 — ICP target role(s)
"Who's the buyer / champion you're usually messaging? List 1–4 titles. Example: 'Head of Growth, VP Marketing, CRO Manager.'"

### Q7 — ICP target industries
"What industries / verticals are you focused on? Or 'horizontal' if you sell across all."

### Q8 — ICP company size
"What company size hits your sweet spot? (Headcount range or revenue range — whichever you think in.)"

### Q9 — Disqualifiers
"Anything that auto-disqualifies a lead even if other signals look strong? (Examples: agency vs. brand, certain industries you don't serve, pre-PMF startups, etc.)"

## Step 3: The 7 signals — which matter to you?

### Q10 — Signal weighting
Use AskUserQuestion with multiple-choice (multiSelect: true) listing the seven canonical signals. Ask: "Which of these signals are *highest priority* for your ICP? Pick all that apply — I'll weight your pipeline accordingly."

Choices:
- **Engagement** — likes/comments on relevant content
- **Job change** — new role at ICP company in last 60 days
- **Funding** — raised in last 90 days
- **Hiring** — posting roles that imply your problem
- **Growth/expansion** — new office, market, product line
- **Tech-stack change** — adopting or dropping tools in your space
- **Direct intent** — asking for recs / posting a problem you solve

### Q11 — Custom signal (optional)
"Anything *not* on that list that's a buying signal in your space? Examples: 'public earnings miss in retail vertical', 'pricing page change', 'specific cert/audit announcement.' Reply 'none' to skip."

## Step 4: Voice and tone

### Q12 — Default tone
Multiple choice:
- **warm-professional** — friendly expert, respects their time, not stiff
- **casual-peer** — first-name energy, like messaging a colleague you've known for years
- **direct-operator** — short, blunt, "here's the thing" — works well for senior ops/eng
- **formal-executive** — polished, structured, conservative

"What's your default DM tone? (The drafts will still adapt to match each contact's vibe when you have prior history with them — this is just the starting point.)"

### Q13 — Voice samples
"Paste 2–3 short samples of how you actually write — DMs, emails, anything. Even a few sentences each is enough. I'll use these as the source of truth for your voice over generic 'professional' defaults."

### Q14 — Banned phrases
"Any phrases that make you cringe? I already ban the obvious ones ('just checking in', 'circling back', 'hope this finds you well', 'quick question', 'wanted to hop on a call'). Anything else? Reply 'none' if not."

## Step 5: Value-add approaches

### Q15 — How you add value
"What are 3–5 *value-add* moves you make in outreach — things that make your message useful, not just an ask? Examples:
- Send a relevant case study or teardown
- Share a free resource or tool
- Offer a 15-min audit / teardown of their thing
- Make an intro to someone in your network
- Drop a relevant insight or stat with no ask attached

List the ones you actually do."

## Step 6: Tools wiring

### Q16 — CRM
Detect available CRM tools (HubSpot, Salesforce, Pipedrive, Close — look for `manage_crm_objects`, `search_crm_objects`, etc.).

- **If found:** "I see [HubSpot/Salesforce/etc.] is connected. Want me to log every sent DM and reply to it automatically? (yes / no / ask each time)"
- **If not found:** "I don't see a CRM connector. The plugin will still work, but `/lead-log` will only update the local pipeline file — not your CRM. You can add a CRM MCP later and rerun this setup."

If they have HubSpot specifically and said yes:

"Two more — what HubSpot **pipeline** do these signal-sourced leads belong in, and what **stage** should they enter at? (E.g., 'Sales Pipeline' / 'Qualified Lead'.) If you're not sure, say 'default' and I'll use the first sales pipeline + first stage."

### Q17 — Apollo
Check if Apollo MCP is connected (look for `apollo_*` tools).

- **If found:** "Apollo is connected — `/lead-pull` can grab job-change, funding, and hiring signals for your ICP automatically. Do you want me to default to (a) job changes only, (b) job changes + funding, or (c) all three (job changes + funding + hiring)?"
- **If not found:** "No Apollo connector — that's fine. You'll capture signals manually with `/lead-capture` from things you spot on LinkedIn. If you add Apollo later, rerun setup."

### Q18 — Gmail
Check for Gmail tools (`gmail_*` or `search_threads`).

- **If found:** Just note it — "Gmail connected. I'll check for prior correspondence whenever you capture a signal."
- **If not found:** "No email connector — `/lead-capture` won't auto-check for prior correspondence. Optional but helpful."

## Step 7: Cadence

### Q19 — Daily send budget
"How many DMs do you want to send per day, max? (Common: 10–15. LinkedIn flags accounts that send much more, especially new ones.)"

### Q20 — Follow-up timing
"Default cadence between touches? Pick one:
- **3-7-14** — opener, follow-up at day 3, final at day 14 (faster, more aggressive)
- **5-10** — opener, follow-up at day 5, final at day 10 (balanced)
- **7-14** — opener, follow-up at day 7, final at day 14 (gentle, default for higher ACV)
- **custom** — I'll ask for specific days"

## Step 8: Write the context file

Once all answers are collected, write to:
`${CLAUDE_PLUGIN_ROOT}/skills/lead-engine/references/user-context.md`

Use this exact format:

```markdown
# User Context — Lead Engine Plugin

> Last updated: [today's date in YYYY-MM-DD]

---

## Company

**Name:** [answer]
**Website:** [answer]
**Positioning:** [answer]
**Avg deal size:** [answer]
**Avg sales cycle:** [answer]

**Offers:**
- **[name]:** [description]
- ...

---

## Ideal Customer Profile

**Target roles:** [comma-separated]
**Industries:** [comma-separated, or "horizontal"]
**Company size:** [range]
**Disqualifiers:** [list, or "none configured"]

---

## Signal Priorities

**High priority:** [comma-separated list of selected signals]
**Custom signal(s):** [user's custom signal description, or "none"]

---

## Voice

**Default tone:** [chosen tone]

**Voice samples:**
> [sample 1]

> [sample 2]

> [sample 3]

**Banned phrases (custom, beyond defaults):** [list, or "none — using built-in banned list only"]

---

## Value-Add Moves

- [move 1]
- [move 2]
- ...

---

## Tools

**CRM:** [Connected: HubSpot / Salesforce / Pipedrive / Close — or "Not connected"]
**Auto-log to CRM:** [yes / no / ask each time]
**HubSpot pipeline:** [name, if applicable]
**HubSpot entry stage:** [name, if applicable]

**Apollo:** [Connected — defaults to (job changes / job changes + funding / all three) — or "Not connected"]

**Gmail:** [Connected — or "Not connected"]

---

## Cadence

**Daily DM budget:** [number]
**Follow-up timing:** [days, e.g., "3-7-14" or "opener + day 5 + day 10"]
```

## Step 9: Initialize pipeline files (if first run)

If `pipeline.md` doesn't exist at `${CLAUDE_PLUGIN_ROOT}/skills/lead-engine/references/pipeline.md`, create it with this header:

```markdown
# Pipeline — Active Signals

> Each entry: signal type, contact, captured date, status, next action date, last touch.
> Status values: `new` → `drafted` → `sent` → `replied` → `booked` / `dead`.

---

(empty — capture your first signal with /lead-capture or /lead-pull)
```

If `sent-log.md` doesn't exist, create it with:

```markdown
# Sent Log — DMs & Replies

> Append-only log of every touch sent and reply received. Used by /lead-pipeline to compute follow-up timing.

---
```

## Step 10: Confirm

After writing all files, summarize back to the user:

```
✅ Profile saved.

  Company: [name] — [positioning fragment]
  ICP: [roles] @ [industry], [size]
  Signals: [count] enabled, [list highest-priority]
  Voice: [tone], [N samples], [N custom bans]
  Value-adds: [count] moves
  Tools: CRM=[status], Apollo=[status], Gmail=[status]
  Cadence: [N DMs/day], [follow-up pattern]

You're set. Full command list:

  /lead-pull     — fetch fresh signals from Apollo (if connected)
  /lead-capture  — log a signal you spotted on LinkedIn
  /lead-connect  — draft a LinkedIn connection-request note
  /lead-warm     — pre-DM warming (likes + comment, then DM 24h later)
  /lead-draft    — draft the 3-touch DM sequence in your voice
  /lead-pipeline — review what's active, what's overdue
  /lead-log      — record sends, replies, bookings, deads
  /lead-brief    — pre-call brief once a meeting books
  /lead-setup    — rerun this (any time)

Suggested starting point:
  /lead-pull (if Apollo connected) → handpick 3 high-priority signals →
  /lead-connect each → /lead-warm each → /lead-draft each.

Read the 4-week rollout plan in skills/lead-engine/references/rollout-plan.md
before scaling — week 1 is intentionally small (5 sends total).
```

If a major connector is missing (CRM or Apollo), end with a one-line nudge: e.g., "Heads up: without a CRM connector, `/lead-log` only updates the local pipeline. Add HubSpot/Salesforce/etc. when you can."
