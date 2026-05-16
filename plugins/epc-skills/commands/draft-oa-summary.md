---
name: draft-oa-summary
description: Draft a letter-style preliminary review ("OA summary") of an EPO Office Action, EESR, or Communication under Art. 94(3) EPC, using the firm's summary template. Output is a client-facing working draft populated from the Office Action found in the current project directory.
argument-hint: "[optional: explicit path or pasted text — overrides project-directory discovery]"
allowed-tools: Read, Grep, Glob
---

# /draft-oa-summary — Draft an Office Action Summary

You are running a single-purpose command. Your only task is to produce a working draft of a preliminary review ("OA summary") of an EPO Office Action, using the firm's summary template.

You **must** use the `oa-draft-summary` skill for the substantive work. Do not re-derive the structure, template, or discovery procedure from memory — load the skill and follow its workflow strictly.

## Step 1: Collect the inputs

This command operates on files in the **current project directory** (the cwd from which it was invoked). One project directory holds one case. The skill itself contains the discovery procedure; your job is to pass the inputs to it.

- **Office Action / EESR / Communication** — required. Sources, in order of preference:
  1. `$ARGUMENTS` — if non-empty, treat it as an **explicit override**: a path to read, or pasted Office Action text. Pass it to the skill and skip project-directory discovery for the OA.
  2. Project-directory discovery — the skill handles this; it Globs for filenames matching `OA` or `EESR` (case-insensitive).
- **Template** — bundled with the skill at `assets/output-template.md` inside the skill's own directory. The skill loads it.

### Prompt for missing inputs

If `$ARGUMENTS` is empty and the skill's discovery procedure fails (no file matches, or multiple matches without disambiguation), the skill will report the problem and ask the user. Do not pre-empt that — let the skill drive.

If the user invokes the command in a directory that is clearly not a case folder (no OA file, no description, no claims), say so concisely and ask whether they meant to invoke from a different directory, or whether they want to paste the OA text.

## Step 2: Load and apply the skill

Load the `oa-draft-summary` skill and follow its workflow strictly:

1. Discover the Office Action via `$ARGUMENTS` override or project-directory Glob.
2. Read the template (`assets/output-template.md`, bundled with the skill).
3. Read the Office Action.
4. Populate every field in the template, verbatim-quoting examiner wording where the attorney will need it, leaving `[TBD: …]` placeholders where the answer must come from the attorney or client, and computing date placeholders (R. 126(2) EPC notional receipt, response deadline) with a brief footnote showing the working.
5. Do not invent prior art or facts not in the Office Action.

Output strictly as the skill prescribes — preserve the template's section order and letter-style structure, write the body in complete paragraphs, no bullet lists or tables in the body, English language, formal patent-attorney register.

## Step 3: Stay in scope

This command is **single-purpose**. It drafts the OA summary and nothing else. If, while running or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the summary first, then state clearly that the request is outside this command's scope:

- **Drafting the response letter to the EPO** — out of scope. Use `/draft-oa-response`.
- **Suggesting response strategies** — out of scope. Use `/suggest-oa-response-strategies`.
- **Stand-alone novelty, clarity, sufficiency, added-matter, or divisional-basis assessment** — out of scope. Use the dedicated `/check-art-*` commands.
- **Non-EP Office Actions** (USPTO, JPO, CNIPA, …) — out of scope. The template and the legal framework are EPO-specific.
- **Client correspondence beyond the firm's letter-style preliminary review** — out of scope; the template defines what is produced.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /draft-oa-summary. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Step 4: Error handling

- **No Office Action found and no `$ARGUMENTS`**: the skill will list what was searched for and ask the user. Do not invent OA content.
- **Multiple OA files match**: the skill lists them and asks which to use. Do not guess.
- **`$ARGUMENTS` is a path that does not resolve**: report the failure and ask the user to provide the file by another means.
- **OA in a foreign language**: proceed (the skill handles this); quote in the original language with optional English glosses.
- **Template missing**: this should not happen — the template is bundled with the skill. If it does, stop and report it rather than improvising a structure.

## Notes for the assistant

- Do not narrate this command file to the user. Just do the work.
- Do not repeat the skill's content — load and use it.
- This is a working draft for attorney review. Do not characterize the output as final.
- Follow the skill's tone strictly: formal patent-attorney English, letter-style, no executive summary at the top, no general remarks at the bottom.
- If after delivery the user asks substantive follow-up questions about the summary itself (e.g., "why did you flag this objection as Strong?"), answer them — that is part of the same command's scope. Only refuse extensions to other tasks.
