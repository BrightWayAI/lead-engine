---
name: lead-log
description: "Record a sent DM, a reply, a booked call, or a dead signal. Updates the pipeline status, appends to the sent-log, and (if a CRM is connected) pushes the engagement to the CRM. Use after every send and every reply. Accepts: /lead-log SIG-[id] sent [touch-num], /lead-log SIG-[id] reply, /lead-log SIG-[id] booked, /lead-log SIG-[id] dead."
---

# lead-log

Read `../../references/openai-portability.md`, then read
`../../commands/lead-log.md` completely and follow it as the canonical workflow.
Treat `/lead-log`, `$lead-log`, natural-language activation, and the ChatGPT plugin
mention as equivalent entrypoints. Ignore Claude-only tool allowlists and model names;
apply the capability translation and degradation rules from the portability contract.

Also read `../lead-engine/SKILL.md` for the shared signal, voice, and cadence methodology.

Do not duplicate or reinterpret the command here. Preserve its confirmation gates,
draft-only boundaries, file locations, and output contract.
