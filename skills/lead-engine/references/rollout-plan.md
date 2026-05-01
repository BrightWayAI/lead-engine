# 4-Week Rollout Plan

The difference between "shipped 50 DMs and got nothing" and "shipped 50 DMs and booked 8 calls" is usually the rollout, not the messages. Don't try to do everything in week one.

---

## Week 0 (the day you install)

**Goal:** profile is set up, tools are wired, you understand the seven signals.

- [ ] Run `/lead-setup`. Be honest in answers — generic ICP / weak voice samples = generic DMs.
- [ ] Read `references/seven-signals.md` end to end. ~10 minutes.
- [ ] Read `references/voice-rules.md`. ~5 minutes.
- [ ] Confirm Apollo + CRM are connected (or you've decided to skip them — both fine, just know which mode you're in).

**Not yet:** don't pull a list, don't capture signals, don't send anything. The setup is the move.

---

## Week 1 — Manual mode + 5 sends total

**Goal:** prove the system works on small volume before scaling. Practice the *full* lifecycle on a small batch — including warming and connection requests, so you build the muscle.

- [ ] Day 1: capture 5 signals manually with `/lead-capture`. Use whatever you spot organically on LinkedIn that day. No Apollo.
- [ ] Day 1: for each signal, run `/lead-connect SIG-X` if not already connected. Send the connection requests. (1 minute per contact.)
- [ ] Day 2: for each accepted connection, run `/lead-warm SIG-X`. Like the 2 posts + paste the comment. (3 minutes per contact.)
- [ ] Day 3: draft openers for all 5 with `/lead-draft`. **Edit each draft before sending** — your job in week 1 is to teach yourself the voice the plugin should learn.
- [ ] Day 3–4: send the DMs. Log each with `/lead-log SIG-X sent 1`.
- [ ] Day 5–7: log any replies with `/lead-log SIG-X reply`. Draft Touch 2 for non-responders.

If a meeting books in week 1, run `/lead-brief SIG-X` 24h before the call.

**Success criterion for week 1:** at least 1 reply from 5 sends. If 0 replies, the message is wrong, the signal is wrong, or the ICP is wrong. Don't scale until you've fixed it.

**What "fixing it" looks like:**
- 0 replies + signals were weak (just likes, old posts) → tighten signal sourcing.
- 0 replies + signals were strong but DMs were generic → rewrite voice samples in `/lead-setup`, retry.
- 0 replies + signals were strong and DMs were good → wrong ICP. Revise ICP in `/lead-setup`.

---

## Week 2 — Apollo on (if connected) + 15 sends total

**Goal:** turn on volume, ~3 sends/day across the week.

- [ ] Day 8: run `/lead-pull` for the first time. You'll get 25–40 candidates. Don't queue them all.
- [ ] Day 8: handpick the 5 best — high priority + your gut says "this is real." Promote those; let the rest sit.
- [ ] Day 8–12: ~3 sends/day. Mix of Apollo-pulled and manually captured signals.
- [ ] Day 10: log Touch 2s for week 1 signals.
- [ ] Day 14: log Touch 3 (breakups) for week 1 signals that haven't replied.

**Success criterion for week 2:** 4+ replies from 20 cumulative sends (week 1 + week 2). That's 20% — at the bottom of the intent-based range. If you're under 10%, pause and review with `/lead-pipeline`.

---

## Week 3 — Pattern detection + ICP refinement

**Goal:** look at what's working and tighten.

- [ ] Day 15: review every reply you've gotten. Which signal types replied? Which didn't?
- [ ] Day 16: rerun `/lead-setup` and update signal-priority weighting based on what's converting.
- [ ] Day 16–21: ~5 sends/day. Lean harder on the signal types that replied last week.
- [ ] Continue Touch 2 + Touch 3 cadence on prior weeks' signals.

**Don't change the message yet** — message changes are noise. Two weeks of one message is the minimum baseline before you tweak. Revise voice samples in `/lead-setup` only if you've spotted a *consistent* pattern (e.g., 4+ replies all said "this felt template-y" — yes, rewrite).

**Success criterion for week 3:** at least 1 booked call from cumulative pipeline. That's the leading indicator that the system is working end to end.

---

## Week 4 — Steady state + first review

**Goal:** sustainable cadence + first real metrics review.

- [ ] Daily: `/lead-pipeline` first thing. Hit the recommended sends. Cap at the daily budget from setup (don't exceed).
- [ ] Day 28: review numbers:
  - Sends: how many touches went out across 4 weeks?
  - Reply rate: replies / sends.
  - Booking rate: bookings / sends.
  - Booking-from-reply rate: bookings / replies.
  - Time-from-signal-to-booking: average days.
- [ ] Day 28: decide the next phase based on numbers:
  - Reply rate <10% → message or signal sourcing is broken; pause and audit.
  - Reply rate 10–20% → working but soft; tweak signal weighting and voice samples.
  - Reply rate 20–30% → solid; scale daily budget by 25–50%.
  - Reply rate 30%+ → great; scale to daily budget cap and keep going.
  - Booking-from-reply rate <30% → you're getting replies but losing them in the discovery DM. Improve the response template you use after a reply (check `voice-rules.md`).

---

## Steady state (week 5+)

Once the system is humming:

- **Daily:** 15 minutes. Run `/lead-pipeline`, send connection requests + warming actions for new signals, send the day's DMs, log replies.
- **Per booked meeting:** 5 minutes. Run `/lead-brief SIG-X` 24h before each call.
- **Weekly:** 30 minutes. Run `/lead-pull` (Mondays), promote the 10–15 best, send connection requests for them, archive the rest.
- **Monthly:** 1 hour. Review numbers, adjust ICP / signal weighting via `/lead-setup` if needed.

**Hard rule:** don't scale daily volume until reply rate is consistently >20%. Volume amplifies whatever you already have. If your reply rate is 5%, scaling 10x just makes you a more efficient spammer.

---

## Anti-patterns to avoid

1. **Scaling too fast.** 50 sends in week 1 = burned signals + flagged LinkedIn account.
2. **Editing the message every send.** You'll never know what's working. Lock it for 2 weeks at a time.
3. **Logging only sends, never replies.** Reply data is the most valuable thing you'll generate. Log every one, even the no's.
4. **Skipping Touch 3.** Breakups pull a real fraction of your replies. Don't quit at Touch 2.
5. **Treating CRM as optional.** If you have a CRM and don't log to it, in 6 months you'll have no way to attribute a closed deal back to the signal that started it. The plugin can't do attribution if the data isn't in the CRM.
6. **Skipping warming.** A 24-hour warming sequence (2 likes + 1 substantive comment) before the DM lifts reply rates noticeably. Three minutes of work; biggest free leverage in the system.
7. **Going to a meeting without a brief.** Run `/lead-brief` 24h before every booked call. The first meeting is where most prospects get stuck — a real brief is the difference between "let me think about it" and a clear next step.
