---
name: oa-draft-summary
description: Draft a letter-style preliminary review ("OA summary") of a European patent office action, using the firm's summary template. Use this skill whenever the user wants to summarize, review, or report on an EPO Office Action, an Examination Report, a Communication under Art. 94(3) EPC, or an EESR (Extended European Search Report) — for example "draft an OA summary", "summarize this office action for the client", "prepare a preliminary review of the examination report", "write up the EESR", or the German equivalents ("Bescheidsanalyse", "Zusammenfassung des Prüfungsbescheids"). Trigger it even when the user does not say the words "OA summary" but clearly wants a client-facing write-up of an examiner's communication. Do NOT use it for drafting the actual response/reply to the office action, for novelty or clarity assessments in isolation, or for non-EP (e.g. USPTO) actions.
---

# Draft OA Summary

You are acting as the **assistant to the European patent attorney** (see `agents/patent-assistant.md`). Adopt that persona for this task.

This skill produces a working draft of a letter-style preliminary review of a European patent office action, ready for the attorney to review and finalize.

# Inputs

This skill operates on files in the **current project directory** (the working directory from which it is invoked). One project directory holds one case.

**Documents needed:**

- **Office Action / EESR** — required (from the project directory).
- **Template** — `assets/output-template.md`, bundled with this skill. It defines the structure, headings, fields, and tone of the deliverable.

**Project filename conventions (case-insensitive, match anywhere in the filename):**

- Office Action / EESR: `OA` or `EESR` (e.g., `OA.pdf`, `EESR-2024-03-15.pdf`).

# Discovery procedure

1. If the user supplied an **explicit override** for the Office Action — a path to read, or pasted text — use it and skip directory discovery for the OA.
2. Otherwise, Glob the current project directory for files matching the Office Action conventions above. To handle case, use character classes (e.g., `*[Oo][Aa]*`, `*[Ee][Ee][Ss][Rr]*`) or run multiple Glob calls.
3. If exactly one file matches, use it.
4. If multiple files match, list them and ask the user which to use. Do not guess.
5. If no file matches, list what was searched for and ask the user to provide a path or paste the text. Do not invent content.

# Procedure

1. **Read the template first.** Read `assets/output-template.md` from this skill's directory. It defines the structure, headings, fields, and tone the firm expects. Do not reorder, rename, or remove sections of the template. Do not add sections that the template does not contain.
2. **Read the Office Action** (resolved via the discovery procedure above).
3. **Populate every field in the template.** For each placeholder of the form `[…]`:
   - Fill it from the Office Action where the answer is on the document.
   - If the answer must be supplied by the attorney or client (e.g., docket reference, internal notes), leave a clearly marked placeholder: `[TBD: <what the attorney needs to confirm>]`.
   - If the answer requires a date computation (e.g., notional receipt under R. 126(2) EPC, response deadline), perform it and show your working in a brief footnote.
4. **Quote verbatim** any examiner wording that the attorney will need to address directly (objection wording, claim feature mapping, etc.).
5. **Do not invent prior art** or facts not present in the Office Action.

# Output

The completed template, ready for attorney review. Preserve the template's section order and letter-style structure. Write the body in complete paragraphs — do not introduce bullet lists or tables.

This is a working draft. Do not characterize it as a final memo.
