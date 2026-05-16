---
name: draft-oa-response
description: >-
  Draft a response letter to the European Patent Office (EPO) addressing an
  Office Action, Communication, EESR/WO-ISA, or Summons to oral proceedings,
  using the firm's standard response template (assets/output-template.md) and a
  chosen prosecution strategy. The draft covers amendments and their Art. 123(2)
  EPC basis, clarity (Art. 84 EPC), and novelty/inventive-step (Art. 54/56 EPC)
  argumentation following the problem–solution approach. Use this skill whenever
  the user wants to draft, prepare, or write an EPO Office Action response, and
  trigger it whenever the user mentions an "OA response", "Office Action
  response", "EPO response letter", "response to an examination report",
  "Bescheidserwiderung", "Erwiderung", "Prüfungsbescheid", responding to an
  EESR / WO-ISA / Summons, or asks to turn an OA-response strategy into a filed
  draft — even if they don't explicitly name the template or this skill.
---

# Draft EPO Office Action Response

You are acting as the **assistant to the European patent attorney** (see `agents/patent-assistant.md`). Adopt that persona for this task.

This skill produces a working draft of a response letter to the EPO, implementing a chosen prosecution strategy and following the firm's response template. It is a draft for attorney review, never a filing.

## What this skill does NOT do — out-of-scope handling

This skill is **single-purpose**. It drafts the response letter implementing a chosen strategy. If, while running or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the response draft first, then state clearly that the request is outside this skill's scope:

- **Generating a strategy** (rather than implementing one) — out of scope. Use `/suggest-oa-response-strategies`.
- **Drafting the OA summary** — out of scope. Use `/draft-oa-summary`.
- **Stand-alone novelty, clarity, sufficiency, added-matter, or divisional-basis assessment** — out of scope. Use the dedicated `/check-art-*` commands.
- **Drafting a divisional application or a new claim set unrelated to the strategy** — out of scope.
- **Non-EP responses** (USPTO, JPO, CNIPA, …) — out of scope. The template and the legal framework are EPO-specific.
- **Filing the response** — out of scope. The output is a working draft for attorney review, never a filing.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /draft-oa-response. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

# Inputs

This skill operates on the files in the **current project directory** — the working folder for the case — plus a **strategy** supplied by the user. One project directory holds one case.

**Strategy (required):**

The strategy that this draft must implement. It is normally produced by an earlier strategy-suggestion step (e.g. a `/suggest-oa-response-strategies` run) and present in the current chat context. The user supplies it as part of their request; it may take any of the following forms:

- A **reference to an in-context strategy** (e.g., "use the dependent-claim-3 narrowing from the strategy report", "use auxiliary request 2"). Resolve it against the strategy content already in the conversation.
- **Pasted strategy text**, used verbatim.
- A **path** to a file containing the strategy notes; read the file.

If no strategy is supplied or identifiable, **stop and ask the user** which strategy to apply. Do not proceed and do not invent a strategy. Sample prompt:

- *"To draft the response I need to know which strategy to implement. You can: (i) reference a strategy already in this conversation (e.g., 'main request + auxiliary request 2 from the strategy report'); (ii) paste the strategy text; or (iii) give me a path to a strategy file."*

If the strategy reference is ambiguous (no matching strategy in context, or several plausible matches), list the candidates and ask the user to disambiguate. Do not guess.

**Documents needed (from the project directory):**

- **Office Action / EESR** — required.
- **Application as filed** — recommended (description + claims + drawings). Required to verify Art. 123(2) EPC basis for any amendment; if not present, every amendment must be flagged with `[TBD: verify basis]`.
- **Cited reference documents** — optional, useful for accurate Art. 54 / 56 argumentation.

**Template (bundled with this skill):**

- `assets/output-template.md`, located in this skill's own folder. Read it relative to the skill directory.

**Project filename conventions (case-insensitive, match anywhere in the filename):**

- Office Action / EESR: `OA` or `EESR`.
- Patent description: `description`.
- Claims: `claims`.
- Drawings: `drawing` or `drawings`.
- Cited reference documents: starting with `D1`, `D2`, `D3`, …

# Discovery procedure

1. **Resolve the strategy first.** If no strategy was supplied, stop and ask the user for it. Otherwise interpret the strategy input per the three forms above (in-context reference, pasted text, or path) and capture the resolved strategy text. If a reference is ambiguous (no matching strategy in context, or several plausible matches), list the candidates and ask the user to disambiguate.
2. Glob the current project directory for files matching each project convention above. Use character classes (e.g., `*[Oo][Aa]*`, `*[Cc]laims*`) or multiple Glob calls to handle case-insensitivity.
3. For each slot:
   - Exactly one match → use it.
   - Multiple matches → list them and ask the user which to use. Do not guess.
   - No match for a **required** slot (Office Action) → list what was searched for and ask the user to provide a path or paste the text. Do not invent content.
   - No match for an **optional** slot (application as filed, cited references) → proceed without it, and explicitly flag downstream what could not be verified.
4. Read every resolved file, including the response template bundled with this skill (`assets/output-template.md`).

# Procedure

The output **must** follow `assets/output-template.md` literally: same headings, same section order, same fixed paragraphs, same conventions. The template uses HTML comments of the form `<!-- DRAFTER: … -->` to instruct the drafter; these are guidance only and must **not** appear in the final draft.

1. **Read the template first.** Note the fixed structure: ONLINE FILING header, application metadata block, intro paragraph announcing what is filed, Roman-numeral sections I–V, signature block, enclosures. Do not reorder, rename, merge, or invent sections.
2. **Read the Office Action** (and the application as filed and cited references resolved during discovery).
3. **Implement the resolved strategy.** The drafted response must follow the strategy's main request, amendments, and argumentation lines. If the strategy specifies auxiliary requests, include them in the order given. Do not silently substitute a different strategy; if you cannot implement part of the strategy (e.g., basis missing), flag it explicitly rather than diverging.
4. **Populate the ONLINE FILING header.**
   - Keep the bold `**ONLINE FILING**` line.
   - Address: by default `European Patent Office / 80298 Munich`. Replace with `2280 HV Rijswijk / NETHERLANDS` if the Examining Division is in The Hague.
   - Keep the `*DRAFT — to be filed by* [response deadline]` line; fill the deadline.
   - Fill application number, title (in straight quotes), applicant, internal reference.
5. **Write the intro paragraph** "In response to [title of the OA / Communication / Summons] dated [date of the OA]:".
6. **Write the "amendments submitted" paragraph** (the one starting "An amended set of claims …"). Adapt it to what is actually filed: claims only vs. claims + description, main vs. main + auxiliary requests, clean copy vs. clean + annotated. Adjust the claim ranges.
7. **Section I — New claims and their original disclosure.** Include whenever amendments are filed; omit entirely if no amendments.
   - Keep the verbatim paragraph beginning "Amendments discussed in the following should not be construed as acquiescence …".
   - For each amended independent claim, write a "New claim [X] is based on …" block, quote the inserted feature **literally in italics with a `>` blockquote** (preserving reference signs), cite the basis (original claims and/or paragraph `[00NN]`), and note any cancellations.
   - Conclude with "No new matter is added by these amendments, so that the new claims meet the requirements of Art. 123(2) EPC."
   - Keep the verbatim "Any subject matter deleted as a result of the amendment is not to be construed as an abandonment …" paragraph.
   - If the basis cannot be verified, flag the affected amendment with `[TBD: verify basis — <feature>]`.
8. **Section II — Clarity.** Include only if the OA raises Art. 84 EPC objections; otherwise **omit the heading entirely** (do not leave an empty section). Quote the examiner literally when stating what is alleged. Either show how the amendment imports clarifying language from the description / a dependent claim, or argue that the term is clear when read with a mind willing to understand and quote the supporting passage from the description.
9. **Section III — Novelty and inventive step.** Include only if the OA raises Art. 54 and/or Art. 56 objections. Use the three sub-sections in this order:
   - **Distinguishing features** — list each missing feature as `> **Fi**: *"…"*` with literal claim text. For each `Fi`, identify the passage of D[n] the examiner relied on, quote what that passage actually discloses, and explain why it does not disclose `Fi`. Conclude with the novelty statement under Art. 54 EPC.
   - **Technical effect and objective technical problem** — source the technical effect and OTP **only from the description** of the application under examination, never from the cited art. Use literal italic quotes from the description (with paragraph `[00NN]` citations). Phrase the OTP as "how to …".
   - **Could-would assessment (Art. 56 EPC)** — apply the problem–solution approach explicitly: state the solution by the distinguishing features, show that D[1] does not teach or suggest them, and explain why the skilled person would not be prompted to combine. If the examiner combines D1 with D2 (or further documents), address each combination explicitly with literal citations. Briefly dispose of any "A" references. Conclude with the inventive-step statement under Art. 56 EPC, and apply *mutatis mutandis* to other independent claims.
10. **Section IV — Formalities.** Include only if formal objections apply, or if the description has been amended (e.g., to acknowledge cited references, remove inconsistencies, conform with amended claims). Otherwise omit.
11. **Section V — Concluding remarks.** Mandatory in every response. Choose **exactly one** of the two alternative paragraphs from the template:
    - The "subject-matter is now in a state acceptable for grant …" paragraph when responding to a **Summons to oral proceedings**.
    - The "All objections raised in the [title of the OA] are addressed … oral proceedings are requested." paragraph when responding to any **other** communication.
    Delete the unused paragraph entirely. Then write the signature block: `Respectfully,` / `[Name]` / `European Patent Attorney` / `[Reg. No.]`.
12. **Enclosures.** Keep the `**Enclosures**` heading and list what is actually filed (e.g., "Amended set of claims [1 to N] (annotated and clean copy)"; add the description line only if a description is also filed).
13. **Strip all DRAFTER comments and the drafter's checklist.** Before returning the draft, remove every `<!-- DRAFTER: … -->` comment and the trailing checklist comment block from the output. None of these may appear in the filed draft.

# Conventions

- **Formal EPO register.** Precise, neutral, professional. No rhetorical flourishes.
- **Quote the examiner verbatim** where wording matters; do not paraphrase silently.
- **Literal italic blockquotes** for inserted claim features, prior-art passages, and quoted description text — using the `> *"…"*` form shown in the template.
- **Reference signs** in quoted claim text must match the figures of the application as filed.
- **Conditional sections** (II, III, IV) are included only when their grounds are raised — never as empty placeholders.

# Output

The completed draft response letter, ready for attorney review. Preserve the template's heading hierarchy and front matter. End the message to the user (after the letter draft) with a short **"Points for attorney review"** list including:

- Any amendment with `[TBD: verify basis]`.
- Any scope-concession decisions made in the draft.
- The oral-proceedings request (whether the conditional request should remain).
- Any part of the strategy that could not be implemented as given, with the reason.

This is a working draft for attorney review. Do not characterize it as filed or final.

# Error handling

- **No strategy supplied**: stop and ask the user for the strategy. Do not proceed.
- **Strategy reference cannot be resolved**: list what was found and ask the user to disambiguate or paste the strategy.
- **No OA found**: the skill lists what was searched for and asks the user. Do not invent OA content.
- **Application as filed missing**: proceed, but the skill will flag every amendment with `[TBD: verify basis]`. Include this in the "Points for attorney review" list.
- **Cited references missing**: proceed; the skill flags Art. 54/56 argumentation as tentative where it relied on the documents.
- **Strategy cannot be implemented as given** (e.g., basis missing for a proposed amendment): the skill flags it in "Points for attorney review" rather than silently diverging. Do not substitute a different strategy.
- **Template missing**: stop and report it rather than improvising structure — the template is the firm's filing standard.

## Notes for the assistant

- Do not narrate this skill file to the user. Just do the work.
- This is a working draft for attorney review. Do not characterize the output as filed or final.
- Follow the firm's tone strictly: formal EPO register, no rhetorical flourishes, conditional sections (II, III, IV) included only when their grounds are raised — never as empty placeholders.
- Reference signs in quoted claim text must match the figures of the application as filed.
- If after delivery the user asks substantive follow-up questions about the draft itself (e.g., "why did you place the disclaimer in Section I rather than arguing under Section III?"), answer them — that is part of the same scope. Only refuse extensions to other tasks.
