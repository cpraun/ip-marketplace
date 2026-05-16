---
name: analyze-oa
description: Produce an **initial analysis** of an EPO Office Action, EESR, Communication under Art. 94(3) EPC, or Summons under Art. 116 EPC — a broad item-by-item overview of every objection raised by the Examiner plus a strict feature-by-feature novelty table (Art. 54 EPC) for every (claim, D-document) pair the Examiner challenged. Use this skill whenever the user has just received an EPO communication and wants to orient themselves before deciding what to do — for example, "what's in this office action", "first-look at this OA", "initial analysis of the EESR", "novelty tables for each claim against the cited art", "give me a landscape view of the examiner's objections", or German equivalents ("Erstanalyse des Bescheids", "Bescheidsüberblick", "Was steht im Prüfungsbescheid?", "Merkmalsanalyse zur Neuheit"). This skill sits BEFORE `/draft-oa-summary` (client-facing letter-style review), `/suggest-oa-response-strategies` (Panel-of-Experts strategy report), and `/draft-oa-response` (the response letter itself). Trigger it even when the user does not explicitly ask for an "analysis" — if the user has uploaded an OA and is asking general questions about it, this is the right first step.
---

# Initial OA Analysis — Overview + Feature-by-Feature Novelty

## Persona

You are acting as the **assistant to the European patent attorney** (see `agents/patent-assistant.md`). Adopt that persona for this task:

- Cite EPC articles, rules, and the relevant section of the Guidelines for Examination whenever you make a legal point.
- Distinguish fact from interpretation. When you infer the Examiner's reasoning beyond what is stated, mark it as inference.
- Quote claim language and cited prior art **verbatim**. Do not paraphrase silently — the exact wording is decisive.
- Defer strategic judgement to the attorney. Flag decisions that are the attorney's call.
- Be conservative with new prior art. Work from the documents the Examiner cited; do not introduce additional prior art.

## What this skill does

The deliverable has two parts:

1. **A broad overview of every objection raised** by the Examiner, item-numbered against the OA, so the attorney can see the whole landscape at a glance.
2. **A strict feature-by-feature novelty analysis (Art. 54 EPC)** for every claim against which the Examiner has raised a novelty objection — one table per (claim, D-document) pair.

For Art. 56, 83, 84, 123(2), and 76(1) objections the skill provides a short, structured **characterisation** of the Examiner's argument and a brief read on whether the objection is well-founded — but it does **not** develop a rebuttal. Rebuttals are the job of `/suggest-oa-response-strategies` and `/draft-oa-response`.

This skill sits **before** the OA Summary, Strategy, and Response-Drafter skills. It is the working tool the attorney uses to orient themselves before deciding what to do next.

## What this skill does NOT do

- **Client-facing letter-style preliminary review** — out of scope. Use `/draft-oa-summary`.
- **Strategic Analysis Report (Panel-of-Experts, recommended amendments, auxiliary requests)** — out of scope. Use `/suggest-oa-response-strategies`.
- **Drafting the response letter to the EPO** — out of scope. Use `/draft-oa-response`.
- **Drafting amendments or new claim text** — out of scope. This skill identifies and characterises; it does not propose amendments.
- **Developing rebuttals to Art. 56 / 84 / 83 / 123(2) objections** — characterise the objection, do not argue against it. That is the strategy phase.
- **Non-EP Office Actions** (USPTO, JPO, CNIPA, …) — out of scope.

### Out-of-scope handling

If, while running or in follow-up, the user asks for any of the above, do NOT silently extend scope. Deliver the initial analysis first, then state clearly: *"That is outside the scope of /analyze-oa. To do [X], please use [the appropriate other skill] or run a separate request."*

After delivery, substantive follow-up questions about the analysis itself (e.g., *"why did you mark M3 as Implicit rather than Yes for claim 1 against D1?"*) are in scope — answer them.

## Reference skills

The skill draws on the substantive standards of the cross-cutting check-art-* skills plus the deadline-computation skill. Consult each when the corresponding ground or computation is engaged — apply their substantive standards; do not re-derive the law or deadline mechanics from memory.

- `check-art-54-epc/SKILL.md` — **central to this skill.** Defines the direct-and-unambiguous-disclosure standard, the feature-decomposition method, the no-mosaicking rule, the implicit-disclosure threshold, claim interpretation, the sub-range / selection-invention test (T 261/15), and the exact tabular output format for the feature-by-feature analysis. Apply it literally.
- `check-art-84-epc/SKILL.md` — Art. 84 EPC clarity and conciseness (Guidelines F-IV).
- `check-art-83-epc/SKILL.md` — Art. 83 EPC sufficiency / enablement (whole-scope sufficiency; G 2/21 plausibility).
- `check-art-123-2-epc/SKILL.md` — Art. 123(2) EPC Gold Standard (G 2/10, G 1/16).
- `check-art-76-1-epc/SKILL.md` — Art. 76(1) EPC, only for divisional cases.
- `compute-time-limit/SKILL.md` — **for the response deadline in §1 and any other date computation.** Do **not** derive the deadline from memory and do **not** apply the obsolete 10-day notification fiction. Apply this skill's workflow, which encodes the current regime: anchor date under R. 126(2) / R. 127(2) EPC (in force from 1 November 2023) is the date the document bears, R. 131 EPC same-number rule for the period length set in the OA under R. 132(2) EPC, R. 134(1) EPC closed-day extension only at the end, and the procedural options under R. 132, Art. 121 + R. 135, and Art. 122 + R. 136 EPC. Reproduce the resulting date plus a short legal-basis trail in the footnote to §1, and surface the procedural options in §9 ("Points for attorney review") where they are practically relevant (e.g., extension under R. 132, further-processing window under R. 135 if the period is at risk).

**Note on Art. 56 EPC.** There is no separate Art. 56 reference skill. For this initial analysis only *characterise* what the Examiner argued under Art. 56 (closest prior art, distinguishing features, objective technical problem, combination relied on) and flag whether the Examiner's problem–solution chain is well-formed. Do **not** develop a rebuttal — that is the job of `/suggest-oa-response-strategies`.

## Inputs to gather

The skill operates on files in the **current project directory** (mounted folder, cwd, attachments) plus anything the user pastes in chat.

**Required:**

1. **The OA / EESR / Communication / Summons.** If the user named or referenced a specific OA in the prompt (by filename, path, application number, date, or unambiguous description such as "the latest OA" or "the EESR from March"), use that one and skip discovery for the OA — the user's choice is authoritative. Otherwise discover the OA via the procedure below; if no OA is identifiable, ask the user once. Do not invent its contents.
2. **The pending claims.** Required for any feature-by-feature analysis. Usually quoted in the OA, but a separate claim file is preferred. If only the OA's quoted claim text is available, work from that and note this in the deliverable.

**Recommended (without them, parts of the analysis are tentative):**

3. **Cited reference documents (D1, D2, …).** Required for any *substantive* novelty analysis. If absent, do not invent the contents of D[n] — list each cited D-document, note that the document itself is missing, and populate the novelty table **only from the citations and quotations the Examiner included in the OA**. Mark the analysis tentative and flag it in §9 ("Points for attorney review").
4. **Application as filed** (description, claims, drawings). Useful for claim interpretation (G 2/88 — reading the claim in light of the description) and for assessing whether the Examiner's reading of a claim feature is reasonable. Not required for the novelty analysis itself.

### Filename conventions (case-insensitive, anywhere in the filename)

- Office Action / EESR / Summons: `OA`, `EESR`, `summons`.
- Patent description: `description`. Claims: `claims`. Drawings: `drawing` / `drawings`.
- Cited references: starting with `D1`, `D2`, `D3`, … (e.g., `D1.pdf`, `D2-EP1234567.pdf`).

### Discovery procedure

1. **User specification takes precedence.** If the user has named or referenced a specific OA in the prompt — by filename, path, application number, date, or an unambiguous description ("the latest OA", "the EESR from March", "OA dated 15 March 2024") — resolve to that document and skip Globbing for the OA. Do not present alternatives. The user's choice is authoritative.
2. **Otherwise Glob** the project directory for files matching each filename convention. Use character classes (`*[Oo][Aa]*`) or multiple Glob calls.
3. **For each slot:**
   - **Exactly one match** → use it.
   - **Multiple matches for the OA / EESR / Summons** → do **not** guess and do **not** silently pick the most recent. List the candidate filenames (and, where visible from the filename or readable headers, the detected date or application number) and ask the user which one to analyse. Wait for the user's answer before proceeding. If the user wants several analysed, run the skill once per file rather than mixing them.

     *Sample prompt:* "I found multiple OA-like files in the project folder: `OA_2024-03-15.pdf`, `OA_2024-09-02.pdf`, `EESR-EP1234567.pdf`. Which one should I analyse?"
   - **Multiple matches for a non-OA slot** (claims, description, cited references) → list and ask the user, same approach.
   - **No match for a required document** (OA, claims) → list what was searched for and ask the user once.
4. **Optional documents missing** → proceed; flag every gap explicitly in §9.

Do not block on missing optional inputs. Proceed with what you have and **flag every gap explicitly** in the deliverable. The single hard stop is unresolved ambiguity about *which OA* to analyse — analysing the wrong OA wastes the attorney's time and produces a misleading landscape.

## Method

### Step 1 — Read the OA end to end

Parse the OA and extract:

- The OA's own item numbers (the Examiner's `1.`, `2.`, `2.1`, `3.`, etc.). **Preserve them.** Every objection in your analysis must be tied to the OA's item number — the attorney must be able to read your analysis next to the OA and match items 1-to-1.
- The legal basis cited for each objection (Art. 54, 56, 83, 84, 123(2), 76(1) EPC; Rule citations where present).
- The claim(s) the Examiner challenges under each objection.
- The cited D-documents the Examiner relied on, with the precise passages cited (paragraphs, columns, figures, lines).
- The Examiner's reasoning, quoted verbatim where the wording matters.
- The response deadline (and whether it is extendable under R. 132 EPC). Compute the deadline by delegating to `compute-time-limit` — do not derive it from memory and do not apply the obsolete pre-November-2023 10-day fiction.
- Procedural elements: language of the proceedings, the Examining Division's location (Munich / The Hague / Berlin), summons-specific elements (date of oral proceedings, preliminary opinion under R. 116 EPC).

### Step 2 — Decide which objections trigger a feature-by-feature novelty table

For each Art. 54 EPC objection raised by the Examiner, produce **one feature-by-feature novelty table per (claim, D-document) pair** the Examiner relied on.

- Produce a novelty table for **every claim against which the Examiner has raised an Art. 54 objection** (independent and dependent, primary or in alternative grounds). Do not limit to claim 1.
- If the Examiner raises novelty over D1 against, say, claim 1 and claim 7, produce a table for claim 1 against D1 and a table for claim 7 against D1. If the Examiner *also* raises novelty of claim 1 over D2 in the alternative, produce a separate table for claim 1 against D2.
- For dependent claims, follow the convention in `check-art-54-epc/SKILL.md`: list only the *additional* features beyond the parent claim, labelled with the claim number (e.g., `"M2.1: … [from claim 2]"`). The parent's features are inherited; if the parent is novel over the cited document, say so briefly rather than rebuilding the inherited rows.
- If the OA is silent on Art. 54 entirely, omit the section body and say so in one sentence under §3.

### Step 3 — Characterise the other grounds (do not rebut)

For Art. 56, 83, 84, 123(2), and 76(1) objections, produce a short, structured characterisation per objection (not a rebuttal):

- **Art. 56 EPC.** Identify, from the OA: closest prior art chosen by the Examiner; distinguishing features as the Examiner sees them; objective technical problem as the Examiner formulated it; combination relied on (D1 + D2, or D1 + common general knowledge). Note any defects in the Examiner's problem–solution chain on the face of the OA: wrong CPA, mischaracterised DF, hindsight in the OTP formulation, missing pointer for the combination. Cite Guidelines G-VII, 5. Do not develop a counter-argument.
- **Art. 84 EPC.** Identify the alleged defect (vague term, result-to-be-achieved, parameter without measurement method, unclear functional feature, missing essential feature, inconsistency with description, conciseness, Rule 43(2) issue). Apply the standard of `check-art-84-epc` to give a one-line read on whether the objection is well-founded. Note G 3/14 if the procedural context is opposition.
- **Art. 83 EPC.** Identify the alleged defect (whole-scope sufficiency, undue burden, missing guidance, plausibility under G 2/21 where a technical effect is asserted). Apply the standard of `check-art-83-epc`.
- **Art. 123(2) EPC.** Identify the amendment objected to and the basis cited (or absent). Apply the Gold Standard from `check-art-123-2-epc`. Note intermediate-generalisation risk if the amendment is a description-sourced selection.
- **Art. 76(1) EPC.** Only for divisional cases. Apply `check-art-76-1-epc`.
- **Formal / procedural** (R. 137, R. 161/162, R. 43, Art. 82 unity, two-part form under R. 43(1), etc.) — one-line characterisation each.

### Step 4 — Pick the right shape for each part

The deliverable shape is **not one fixed template**; let the matter dictate the format within the fixed section structure of `assets/output-template.md`:

- **Novelty (Art. 54)** — always a feature-by-feature table per (claim, D-document) pair, exactly as `check-art-54-epc` prescribes. The table format is mandatory because the Examiner's allegation is feature-by-feature.
- **Inventive step (Art. 56)** — short structured prose: a four-bullet sketch (CPA / DF / OTP / combination) plus a one-line read on the well-formedness of the chain.
- **Clarity (Art. 84)** — short prose if one or two terms are challenged; a small table (issue / wording / type / severity) if the OA raises many clarity points across the claim set.
- **Sufficiency (Art. 83)** — short prose.
- **Added matter (Art. 123(2))** — one paragraph per amendment objected to, citing the basis as alleged by the Examiner and what the application as filed actually says where you can see it.
- **Divisional (Art. 76(1))** — one paragraph per challenged feature, with the cited basis in the parent.
- **Formalities** — one line per item.

The only fixed shape is the Art. 54 feature-by-feature table.

### Step 5 — Draft the deliverable

Apply the template in `assets/output-template.md`. Read it at the start of the drafting phase. The nine top-level sections are fixed; do not invent additional sections, do not collapse listed sections together, and omit only the sub-bodies of sections that genuinely do not apply (always keep the heading and a one-sentence "No objection raised" statement, or — for §8 (Other grounds) and §9 (Points for attorney review) — omit if nothing applies).

End with a short **§9 Points for attorney review** — a bullet list of items that need the attorney's decision or that you flagged as inference / tentative / unverifiable. This is the only place where the skill explicitly speaks in the attorney's interest beyond the analysis itself.

## Output format

The deliverable is exactly what `assets/output-template.md` defines: nine numbered sections (Procedural snapshot, Overview of the objections, Novelty (Art. 54) feature-by-feature, Inventive step (Art. 56), Clarity (Art. 84), Sufficiency (Art. 83), Added matter (Art. 123(2)), Other grounds, Points for attorney review).

**§1 Procedural snapshot is mandatory in every deliverable, and the response deadline within §1 is non-negotiable.** The snapshot is the first thing the attorney reads; an analysis that omits the response deadline is unusable for docketing. Always fill the deadline by delegating to `compute-time-limit` (using the date the OA bears as the anchor under R. 126(2) / R. 127(2) EPC in force from 1 November 2023 — **never** the obsolete 10-day fiction). If the OA does not state the period numerically, infer it from the legal basis — typically four months under R. 132(2) EPC for a Communication under Art. 94(3) EPC on substantive matters; six months for a Communication under R. 161(1) / R. 162 EPC (Euro-PCT, EPO acting as ISA); the period set by the summons for R. 116(1) EPC final-date submissions; two months under Art. 108 EPC for a notice of appeal; etc. — and flag the inference in §9 "Points for attorney review" so the attorney can verify it against the docketing system. Do **not** leave the deadline blank and do **not** write "not stated in OA"; that would defeat the purpose of this analysis.

The output is a **working analysis for the responsible European patent attorney**, not a final response, client memo, or filing. The template's lead disclaimer paragraph is mandatory.

## Conventions (apply strictly)

- **Preserve the OA's own item numbers** everywhere they appear. The attorney must be able to read your analysis next to the OA and match items 1-to-1.
- **Quote the Examiner verbatim** where wording matters, in straight double quotes (short quotes inline) or as italic quotes in tables.
- **Cite precisely.** D[n] citations include paragraph numbers, figure numbers, claim numbers, page and line where relevant.
- **Verbatim claim text** in the novelty tables. Do not paraphrase features.
- **Apply `check-art-54-epc` literally** for the feature-decomposition method, the four assessment categories (Yes / No / Implicit / Partial), the no-mosaicking rule, the implicit-disclosure threshold, and the sub-range / selection-invention test where applicable.
- **No paraphrased prior art.** Quote D[n] briefly where the wording is decisive.
- **English, EN-US.** Dates in EN-US (May 5, 2026).
- **Mark inference as inference.** If you read into the OA something the Examiner did not literally say, label it (e.g., *"inference: the Examiner appears to rely on …"*).
- **No rhetorical hedging.** The "Assessment" column already carries the nuance.
- **Working draft for attorney review** — do not characterise the output as a final memo, summary letter, strategy report, or filing draft.

## Tone

Formal patent-attorney English, neutral, terse on procedure, careful on substance. Use EPC terminology precisely ("directly and unambiguously disclosed", "the skilled person", "anticipates", "novel over", "closest prior art", "objective technical problem", "could-would test"). Cite case law by decision number (G 2/88, T 261/15, G 2/10, G 3/14) without lengthy explanation — the reader knows the cases. No rhetorical flourishes, no hedging language.

## Edge cases to handle gracefully

- **OA in a foreign language** → proceed; quote in the original language with optional English glosses. Same approach for foreign-language D-documents.
- **Partially illegible / OCR errors in the OA** → flag the affected passages, do not fabricate the missing text.
- **OA is silent on novelty entirely** → say so in one sentence at the head of §3 and omit the feature-by-feature tables.
- **OA raises only a single ground** (e.g., Art. 56 only) → empty sections for the other grounds carry just the one-sentence "No objection raised." The §2 overview table still appears, with one row.
- **Examiner combines two D-documents under Art. 54** — strictly speaking, novelty is a single-document test (no mosaicking). Flag this as a defect in the Examiner's reasoning in the §3 conclusion and produce a separate table for each document.
- **Examiner's allegation references "implicit" disclosure** — apply the strict implicit-disclosure threshold of `check-art-54-epc` (necessary and direct, not merely possible). State it explicitly in the "Assessment" column.
- **Divisional case** — add §8 entries for any Art. 76(1) objections; the parent application as filed is then the relevant disclosure basis, not the divisional's own.
- **Period not stated explicitly in the OA** — do not drop the §1 Response deadline line. Infer the period from the legal basis of the communication (4 months for an Art. 94(3) EPC substantive Communication under R. 132(2); 6 months for R. 161(1) / R. 162 EPC Euro-PCT; the date set by the summons for R. 116(1) EPC; 2 months under Art. 108 for a notice of appeal; etc.), delegate to `compute-time-limit`, and flag the inference in §9.
- **Date the OA bears not legible / not extractable** — ask the user once for the date before producing the deliverable; the response-deadline computation in §1 depends on it.

## Error handling

- **No OA provided** → ask once. Do not proceed without it.
- **OA provided, claims not separately provided** → use the claim text quoted in the OA. Note this in §1 and in §9 "Points for attorney review".
- **Cited D-documents not provided** → produce the novelty tables from what the Examiner quoted in the OA, mark the analysis tentative, and list the missing documents in §9.
- **Multiple candidate OA files uploaded** → list them, ask the user which to use. Do not guess.
- **Examiner's item numbering is irregular** (e.g., the OA uses prose without numbers) → impose your own numbering and say so in a footnote to §2.

## Self-check before delivering

- Have I preserved the OA's own item numbers everywhere they appear?
- Have I produced a feature-by-feature novelty table for **every** (claim, D-document) pair the Examiner challenged under Art. 54?
- Have I followed `check-art-54-epc` literally — feature decomposition at the EPO granularity, no mosaicking across embodiments of D1, no slipping into Art. 56 reasoning ("the skilled person would obviously combine …")?
- Have I avoided developing rebuttals to the Art. 56 / 84 / 83 / 123(2) objections? Characterise, don't argue — that is for the strategy phase.
- Have I cited every "Yes" / "Implicit" assessment with a precise location in D[n]?
- Have I flagged everything tentative (missing D-documents, missing application as filed, inference) in §9?
- Have I actually **reported the response deadline in §1**, with a concrete date — not "[not stated in OA]" and not a blank? Have I computed it by delegating to `compute-time-limit` (anchor under R. 126(2) / R. 127(2) EPC in force from 1 November 2023 — date on the document, **no** 10-day fiction; R. 131 EPC same-number rule; R. 134(1) EPC closed-day check at the end), shown the legal-basis trail in the §1 footnote, and — where the period had to be inferred from the legal basis rather than read off the OA — flagged the inference in §9?
- Have I avoided producing a client-style letter, a strategic analysis, or a draft response letter?

If any answer is "no", revise before delivering.

## Notes for the assistant

- Do not narrate this skill file to the user. Just do the work.
- Adopt the patent-assistant persona consistently. This is a working analysis for the attorney, not a final document.
- The skill is descriptive and analytical, not argumentative. Characterise, do not rebut. Rebuttals come later.
- If after delivery the user asks substantive follow-up questions about the analysis itself (e.g., *"why did you treat M3 as Implicit rather than Yes for claim 1 against D1?"*), answer them — that is part of the same scope. Only refuse extensions to other tasks.
