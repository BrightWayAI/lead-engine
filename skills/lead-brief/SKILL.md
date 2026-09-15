---
name: lead-brief
description: "Generate a pre-call brief once a meeting is booked. Pulls everything we know about the contact and company (signal context, prior touches, replies, CRM record, recent web mentions) and produces a structured brief: contact snapshot, company snapshot, signal recap, talking points, likely objections, and a soft next-step. Use after /lead-log SIG-X booked."
---

# lead-brief

Read `../../references/openai-portability.md`, then read
`../../commands/lead-brief.md` completely and follow it as the canonical workflow.
Treat `/lead-brief`, `$lead-brief`, natural-language activation, and the ChatGPT plugin
mention as equivalent entrypoints. Ignore Claude-only tool allowlists and model names;
apply the capability translation and degradation rules from the portability contract.

Also read `../lead-engine/SKILL.md` for the shared signal, voice, and cadence methodology.

Do not duplicate or reinterpret the command here. Preserve its confirmation gates,
draft-only boundaries, file locations, and output contract.
