---
name: lead-log
description: >
  Record a sent DM, a reply, a booked call, or a dead signal. Updates the pipeline status, appends to the sent-log, and (if a CRM is connected) pushes the engagement to the CRM. Use after every send and every reply. Accepts: /lead-log SIG-[id] sent [touch-num], /lead-log SIG-[id] reply, /lead-log SIG-[id] booked, /lead-log SIG-[id] dead.
---

# /lead-log — record what happened

You are updating pipeline state and (optionally) CRM after the user takes an action on a signal.

## Step 0: Preflight

Read:
- `<config-root>/plugins/lead-engine.user-context.md` (for CRM wiring + auto-log preference)
- `<config-root>/plugins/lead-engine.pipeline.md`
- `<config-root>/plugins/lead-engine.sent-log.md`

## Step 1: Parse the command

Expected forms:

- `/lead-log SIG-[id] sent [1|2|3]` — user sent touch N
- `/lead-log SIG-[id] reply` — contact replied
- `/lead-log SIG-[id] booked` — call/meeting booked
- `/lead-log SIG-[id] dead` — close the signal, no further outreach

If the form is incomplete or ambiguous, use AskUserQuestion to clarify. If no SIG-ID is given, list `sent` and `replied` signals from the pipeline and ask which one.

If the SIG-ID doesn't exist in the pipeline, say so and stop.

## Step 2: Branch by action

### Action: `sent [touch-num]`

1. Ask the user (AskUserQuestion freeform): "Paste the actual message you sent. I'll log it verbatim so we can refine your voice over time."
2. Append to `sent-log.md`:

```markdown
---

## [YYYY-MM-DD HH:MM] — SIG-[id] — Touch [N]

**Contact:** [name from pipeline]
**Channel:** [LinkedIn DM / email / other — ask if not obvious]

**Sent:**
> [verbatim message]
```

3. Update ``<config-root>/plugins/lead-engine.pipeline.md` SIG entry:
   - Status: `drafted` → `sent`
   - Cadence section: mark Touch [N] as `sent [date]`
   - Update next-touch target date based on cadence interval from `user-context.md`
4. If CRM is connected and auto-log = yes (or user confirms when set to "ask each time"):
   - Find or create the contact in the CRM (use the email from the pipeline if known; otherwise create a contact with name + company + LinkedIn URL).
   - Log a Note or Engagement on the contact: subject "Intent outbound — Touch [N] — [signal type]", body = the verbatim message.
   - If contact is new: also set the lifecycle stage / pipeline stage from `user-context.md`.
5. Confirm:

```
✅ Logged Touch [N] for SIG-[id]
  Pipeline status: sent
  Next touch due: [date] (Touch [N+1])
  CRM: [logged to HubSpot/etc., or "skipped — not connected"]
```

### Action: `reply`

1. Ask: "Paste their reply. I'll log it and help you draft a response."
2. Append to `sent-log.md`:

```markdown
## [YYYY-MM-DD HH:MM] — SIG-[id] — Reply

**Reply received:**
> [verbatim reply]
```

3. Update ``<config-root>/plugins/lead-engine.pipeline.md` SIG status: `sent` → `replied`. Pause cadence (clear future touch target dates).
4. Read the reply and classify it:
   - **Positive / interested:** they want to chat / hear more / book time.
   - **Question:** they're asking for clarification or info.
   - **Soft no / not now:** "not the right time", "maybe Q3", "we use [competitor]".
   - **Hard no:** clear rejection.
5. For positive / question replies: draft a short context-aware response. Use the user's voice. Don't fall back to template DMs. Output it for the user to send.
6. For soft no: draft a graceful "totally fair, hit me up if [trigger]" response. Suggest setting a reminder for 60–90 days out.
7. For hard no: don't draft a response. Suggest the user run `/lead-log SIG-[id] dead` to close it.
8. If CRM is connected, log the reply as a Note on the contact + update lifecycle stage if appropriate (e.g., HubSpot "Engaged" or similar).

### Action: `booked`

1. Ask (AskUserQuestion): "When's the call? (date + time + meeting link if you have it)"
2. Update ``<config-root>/plugins/lead-engine.pipeline.md` status: → `booked`. Add the meeting details to the SIG entry's Notes.
3. Append to `sent-log.md` a "Booked" entry.
4. If CRM connected:
   - Update the contact's lifecycle / deal stage to whatever "Meeting Booked" maps to in the user's CRM (HubSpot has a built-in `Meeting Booked` stage; other CRMs vary — ask the user once and remember).
   - Optionally create a Deal record if the user's CRM supports deals and they want one. Ask: "Want me to create a Deal record in [CRM] for this? (yes / no)"
5. Confirm with a small celebration line ("That's the model working. SIG-[id] → meeting in [N] days from signal capture.") and end with: "Generate the pre-call brief whenever you're ready: `/lead-brief SIG-[id]`."

### Action: `dead`

1. Ask (AskUserQuestion freeform, optional): "Quick reason this is dead? (one line — helps refine future ICP scoring. Skip if not worth the keystrokes.)"
2. Update ``<config-root>/plugins/lead-engine.pipeline.md` status: → `dead`. Append the reason to the Notes section if provided.
3. Append a "Closed dead" entry to `sent-log.md` with the reason.
4. If CRM connected, optionally update contact lifecycle to "Disqualified" or equivalent — but only if the user's CRM has a sensible mapping. If unclear, leave the CRM alone and note it.

## Step 3: Pattern recognition (light)

After updating, do one quick check: among recent sent-log entries (last 14 days), is any pattern emerging?

- 3+ replies on a single signal type → mention it. "FYI — your last 4 replies were all from job-change signals. Worth weighting that higher in `/lead-pull`."
- 5+ sends with 0 replies → mention it. "Heads up — last 5 sends with no replies. Either signal sourcing or message is off. Want to pause and review?"

Keep this section to one line max, only when something is genuinely worth flagging. Don't manufacture insights.

## Step 4: Don't be chatty

Output should be short. Confirmation + next step. Resist the urge to summarize what you just did — the user knows.
