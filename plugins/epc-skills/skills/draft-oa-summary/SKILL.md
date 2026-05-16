---
name: draft-oa-summary
description: Draft a letter-style preliminary review ("OA summary") of a European patent office action, using the firm's summary template. Use this skill whenever the user wants to summarize, review, or report on an EPO Office Action, an Examination Report, a Communication under Art. 94(3) EPC, or an EESR (Extended European Search Report) — for example "draft an OA summary", "summarize this office action for the client", "prepare a preliminary review of the examination report", "write up the EESR", or the German equivalents ("Bescheidsanalyse", "Zusammenfassung des Prüfungsbescheids"). Trigger it even when the user does not say the words "OA summary" but clearly wants a client-facing write-up of an examiner's communication. Do NOT use it for drafting the actual response/reply to the office action, for novelty or clarity assessments in isolation, or for non-EP (e.g. USPTO) actions.
---

# Draft OA Summary

You are acting as the **assistant to the European patent attorney** (see `agents/patent-assistant.md`). Adopt that persona for this task.

This skill produces a working draft of a letter-style preliminary review of a European patent office action, ready for the attorney to review and finalize.

## What this skill does NOT do — out-of-scope handling

This skill is **single-purpose**. It drafts the OA summary and nothing else. If, while running or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the summary first, then state clearly that the request is outside this skill's scope:

- **Drafting the response letter to the EPO** — out of scope. Use `/draft-oa-response`.
- **Suggesting response strategies** — out of scope. Use `/suggest-oa-response-strategies`.
- **Stand-alone novelty, clarity, sufficiency, added-matter, or divisional-basis assessment** — out of scope. Use the dedicated `/check-art-*` commands.
- **Non-EP Office Actions** (USPTO, JPO, CNIPA, …) — out of scope. The template and the legal framework are EPO-specific.
- **Client correspondence beyond the firm's letter-style preliminary review** — out of scope; the template defines what is produced.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /draft-oa-summary. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

# Inputs

This skill operates on files in the **current project directory** (the working directory from which it is invoked). One project directory holds one case.

**Documents needed:**

- **Office Action / EESR** — required (from the project directory).
- **Template** — `assets/output-template.md`, bundled with this skill. It defines the structure, headings, fields, and tone of the deliverable.

**Project filename conventions (case-insensitive, match anywhere in the filename):**

- Office Action / EESR: `OA` or `EESR` (e.g., `OA.pdf`, `EESR-2024-03-15.pdf`).

**Sources, in order of preference:**

1. An explicit override — a path to read, or pasted Office Action text passed in by the user. Use it and skip directory discovery for the OA.
2. Project-directory discovery — the skill Globs for filenames matching `OA` or `EESR` (case-insensitive).

# Discovery procedure

1. If the user supplied an **explicit override** for the Office Action — a path to read, or pasted text — use it and skip directory discovery for the OA.
2. Otherwise, Glob the current project directory for files matching the Office Action conventions above. To handle case, use character classes (e.g., `*[Oo][Aa]*`, `*[Ee][Ee][Ss][Rr]*`) or run multiple Glob calls.
3. If exactly one file matches, use it.
4. If multiple files match, list them and ask the user which to use. Do not guess.
5. If no file matches, list what was searched for and ask the user to provide a path or paste the text. Do not invent content.

If the user invokes the skill in a directory that is clearly not a case folder (no OA file, no description, no claims), say so concisely and ask whether they meant to invoke from a different directory, or whether they want to paste the OA text.

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

# Error handling

- **No Office Action found and no explicit override**: the skill lists what was searched for and asks the user. Do not invent OA content.
- **Multiple OA files match**: the skill lists them and asks which to use. Do not guess.
- **Explicit override is a path that does not resolve**: report the failure and ask the user to provide the file by another means.
- **OA in a foreign language**: proceed (the skill handles this); quote in the original language with optional English glosses.
- **Template missing**: this should not happen — the template is bundled with the skill. If it does, stop and report it rather than improvising a structure.

## Notes for the assistant

- Do not narrate this skill file to the user. Just do the work.
- This is a working draft for attorney review. Do not characterize the output as final.
- Follow the firm's tone strictly: formal patent-attorney English, letter-style, no executive summary at the top, no general remarks at the bottom.
- If after delivery the user asks substantive follow-up questions about the summary itself (e.g., "why did you flag this objection as Strong?"), answer them — that is part of the same scope. Only refuse extensions to other tasks.
