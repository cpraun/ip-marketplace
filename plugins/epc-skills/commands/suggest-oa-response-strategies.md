---
name: suggest-oa-response-strategies
description: Suggest concrete response strategies to overcome objections raised in an EPO Communication (Office Action under Art. 94(3) EPC, EESR, or Summons under Art. 116 EPC) using a Panel-of-Experts simulation built on the problem–solution approach. Output is a Strategic Analysis Report identifying the most promising amendment routes from the dependent claims and the description, plus argumentation-only and procedural fallbacks.
argument-hint: "[optional: explicit Office Action path or pasted text — overrides project-directory discovery]"
allowed-tools: Read, Grep, Glob
---

# /suggest-oa-response-strategies — Strategic Analysis Report for an EPO Communication

You are running a single-purpose command. Your only task is to produce a Strategic Analysis Report proposing concrete response strategies to an EPO Communication.

You **must** use the `oa-response-strategy-epc` skill for the substantive analysis. Do not re-derive the Panel-of-Experts method, the problem–solution approach, the discovery procedure, or the output structure from memory — load the skill and follow its workflow strictly.

## Step 1: Collect the inputs

This command operates on files in the **current project directory** (the cwd from which it was invoked). One project directory holds one case. The skill itself contains the discovery procedure; your job is to surface the inputs and let the skill drive.

- **Office Action / EESR / Summons** — required. Sources, in order of preference:
  1. `$ARGUMENTS` — if non-empty, treat it as an **explicit override**: a path to read, or pasted OA text. The skill still attempts directory discovery for the *other* documents.
  2. Project-directory discovery — the skill Globs for filenames matching `OA`, `EESR`, or `summons` (case-insensitive).
- **Application as filed** — recommended (description + claims + drawings). Required to verify Art. 123(2) EPC basis for any proposed amendment and to source distinguishing features. The skill discovers it by filename convention (`description`, `claims`, `drawing`/`drawings`).
- **Cited reference documents (D1, D2, …)** — required for any substantive Art. 54 / 56 analysis. The skill discovers them by filename convention (filenames starting with `D1`, `D2`, …). If absent, the skill flags art-based objections as tentative.

### Prompt for missing inputs

The skill handles its own discovery and stop-and-ask. Do not pre-empt it. In particular, the skill requires before any final strategy:

- One or more D-documents on which an objection is based.
- Identifiable legal grounds for each objection (Art. 54, 56, 83, 84, 123(2) EPC, …).
- The deadline for responding to the OA / Summons.

If any of these are missing after discovery, the skill will stop and ask. Let it.

If the user invokes the command in a directory that is clearly not a case folder (no OA file, no description), say so concisely and ask whether they meant to invoke from a different directory or want to paste the OA text.

## Step 2: Load and apply the skill

Load the `oa-response-strategy-epc` skill and follow its workflow strictly:

1. Discover the OA, application as filed, and cited references.
2. Phase 1 — summarize each Examiner objection and assess its merit objectively as **Strong / Weak / Subjective** with a one-line reason, separated from any recommendation.
3. Phase 2 — for each clarity (Art. 84) objection, propose amendments that traverse the objection by importing explicit clarifying statements from the Description, or argue for the term being clear with literal supporting citations.
4. Phase 3 — Panel-of-Experts simulation for Art. 54 / 56: internally enumerate candidate amendment routes from (A) dependent claims and (B) the description, apply the problem–solution approach (Guidelines G-VII, 5) to each (Closest Prior Art, Distinguishing Feature, Technical Effect, Objective Technical Problem, Art. 123(2) basis), and present only the curated, highest-probability strategies.
5. Alternative response routes — argumentation-only attack lines, auxiliary requests ordered from broadest to narrowest, procedural options (Art. 116 oral proceedings, examiner interview, Art. 121 further processing, divisional under Art. 76).
6. Per-route assessment — likelihood of success (low / medium / high, with a 0–100 % estimate where meaningful) and risks (Art. 123(2), scope reduction, loss of priority under Art. 87, knock-on Art. 84, etc.).
7. Output the report in the structure prescribed by the skill (assets/output-template.md inside the skill's directory).

Output strictly as the skill prescribes — Markdown, EN-US, literal italic quotes with citation, no paraphrasing of technical features, avoid the words "invention" and "applicant" per the skill's terminology rules. Only carry forward actionable recommendations whose likelihood of success exceeds 30 %.

## Step 3: Stay in scope

This command is **single-purpose**. It produces a Strategic Analysis Report — not the response letter itself. If, while running or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the strategy report first, then state clearly that the request is outside this command's scope:

- **Drafting the actual response letter** — out of scope. Use `/draft-oa-response`.
- **Drafting the OA summary** — out of scope. Use `/draft-oa-summary`.
- **Stand-alone novelty, clarity, sufficiency, added-matter, or divisional-basis assessment** — out of scope. Use the dedicated `/check-art-*` commands.
- **Opposition strategy (Art. 99 EPC)** — adjacent but different procedure (different parties, different evidentiary posture, G 3/14 limits on Art. 84). Out of scope.
- **Revocation, nullity, or infringement analysis** before national courts or the UPC — out of scope.
- **Non-EP Office Actions** (USPTO, JPO, CNIPA, …) — out of scope. The legal framework and amendment rules differ.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /suggest-oa-response-strategies. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Step 4: Error handling

- **No OA found and no `$ARGUMENTS`**: the skill lists what was searched for and asks the user. Do not invent OA content.
- **Application as filed missing**: the skill proceeds and explicitly flags every proposed amendment whose Art. 123(2) basis cannot be verified.
- **Cited references missing**: the skill proceeds and flags art-based objections as tentative; it does not search the web for D-documents.
- **Response deadline not stated in the OA**: the skill stops and asks the user before offering a final strategy.
- **OA raises only clarity objections and the user wants a deep clarity-only analysis**: the skill may redirect to `/check-art-84-epc`. Honour the redirect.
- **Confidence below 90 % in a particular assessment**: the skill states explicitly what is missing rather than guessing. Do not paper over.

## Notes for the assistant

- Do not narrate this command file to the user. Just do the work.
- Do not repeat the skill's content — load and use it.
- Do not list every individual brainstorm from the Panel-of-Experts simulation; the skill curates and presents only the consolidated, highest-probability strategies.
- Keep the **assessment of merit** of each objection separate from any recommendation. The attorney must be able to read "the Examiner is right on objection 2" without that being mixed up with "and here is what we should do about it".
- This is a working draft for attorney review, not a final filing or client memo. Do not characterize it as such.
- If after delivery the user asks substantive follow-up questions about the strategy itself (e.g., "why did you rank the dependent-claim-3 route above the description-paragraph-32 route?"), answer them — that is part of the same command's scope. Only refuse extensions to other tasks.
