---
name: draft-oa-response
description: Draft a response letter to the EPO addressing an Office Action, EESR/WO-ISA, Communication, or Summons to oral proceedings, implementing a chosen prosecution strategy and using the firm's response template. Output is a working draft of the filed letter, ready for attorney review.
argument-hint: <required: strategy directive — name a strategy already in chat context, paste strategy text, or pass a path to a strategy file>
allowed-tools: Read, Grep, Glob
---

# /draft-oa-response — Draft an Office Action Response Letter

You are running a single-purpose command. Your only task is to produce a working draft of a response letter to the EPO, implementing a chosen prosecution strategy and following the firm's response template.

You **must** use the `oa-draft-response` skill for the substantive work. Do not re-derive the template, sections, conventions, or discovery procedure from memory — load the skill and follow its workflow strictly.

## Step 1: Collect the inputs

This command operates on files in the **current project directory** (the cwd from which it was invoked) plus a **strategy** supplied via `$ARGUMENTS`. The skill itself contains the discovery procedure; your job is to surface the strategy and let the skill drive.

- **Strategy (required) — `$ARGUMENTS`**: the strategy this draft must implement. Normally produced by an earlier `/suggest-oa-response-strategies` run and present in the current chat context. `$ARGUMENTS` may take any of the following forms:
  - A **reference to an in-context strategy** (e.g., "use the dependent-claim-3 narrowing from the strategy report", "use auxiliary request 2"). Resolve it against the strategy content already in the conversation.
  - **Pasted strategy text**, used verbatim.
  - A **path** to a file containing the strategy notes; the skill reads the file.
- **Office Action / EESR / Communication / Summons** — required. The skill discovers it from the project directory.
- **Application as filed** — recommended (description + claims + drawings). Required to verify Art. 123(2) EPC basis for any amendment; the skill flags `[TBD: verify basis]` if it is not available.
- **Cited reference documents (D1, D2, …)** — optional, useful for accurate Art. 54 / 56 argumentation. The skill discovers them by filename convention.
- **Template** — bundled with the skill at `assets/output-template.md` inside the skill's own directory. The skill loads it.

### Prompt for missing inputs

If `$ARGUMENTS` is empty, **stop and ask the user** which strategy to apply. Do not proceed and do not invent a strategy. Sample prompt:

- *"To draft the response I need to know which strategy to implement. You can: (i) reference a strategy already in this conversation (e.g., 'main request + auxiliary request 2 from the strategy report'); (ii) paste the strategy text; or (iii) give me a path to a strategy file."*

If `$ARGUMENTS` references an in-context strategy that is ambiguous (no matching content, or several plausible matches), list the candidates and ask the user to disambiguate. Do not guess.

The skill handles missing project-directory documents (asks for the OA if absent, flags `[TBD: verify basis]` if the application as filed is absent).

## Step 2: Load and apply the skill

Load the `oa-draft-response` skill and follow its workflow strictly:

1. Resolve the strategy via `$ARGUMENTS`.
2. Discover the OA, application as filed, and cited references via project-directory Glob.
3. Read the response template (`assets/output-template.md`, bundled with the skill).
4. Implement the strategy section by section, following the template literally: ONLINE FILING header, application metadata, intro paragraph, Sections I (basis for amendments under Art. 123(2)), II (clarity / Art. 84) — if raised, III (novelty/inventive step / Art. 54 + 56) — if raised, IV (formalities) — if applicable, V (concluding remarks — choose the correct alternative depending on whether the response is to a Summons or any other communication), signature block, enclosures.
5. Strip every `<!-- DRAFTER: … -->` comment and the trailing drafter's checklist before delivering.
6. End the message to the user (after the letter draft) with a short **"Points for attorney review"** list per the skill.

Output strictly as the skill prescribes — formal EPO register, literal italic blockquotes for inserted claim features and cited passages, verbatim examiner quotes where wording matters, conditional sections (II, III, IV) included only when their grounds are raised.

## Step 3: Stay in scope

This command is **single-purpose**. It drafts the response letter implementing a chosen strategy. If, while running or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the response draft first, then state clearly that the request is outside this command's scope:

- **Generating a strategy** (rather than implementing one) — out of scope. Use `/suggest-oa-response-strategies`.
- **Drafting the OA summary** — out of scope. Use `/draft-oa-summary`.
- **Stand-alone novelty, clarity, sufficiency, added-matter, or divisional-basis assessment** — out of scope. Use the dedicated `/check-art-*` commands.
- **Drafting a divisional application or a new claim set unrelated to the strategy** — out of scope.
- **Non-EP responses** (USPTO, JPO, CNIPA, …) — out of scope. The template and the legal framework are EPO-specific.
- **Filing the response** — out of scope. The output is a working draft for attorney review, never a filing.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /draft-oa-response. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Step 4: Error handling

- **`$ARGUMENTS` empty**: stop and ask the user for the strategy. Do not proceed.
- **`$ARGUMENTS` references an in-context strategy that cannot be resolved**: list what was found and ask the user to disambiguate or paste the strategy.
- **No OA found**: the skill lists what was searched for and asks the user. Do not invent OA content.
- **Application as filed missing**: proceed, but the skill will flag every amendment with `[TBD: verify basis]`. Include this in the "Points for attorney review" list.
- **Cited references missing**: proceed; the skill flags Art. 54/56 argumentation as tentative where it relied on the documents.
- **Strategy cannot be implemented as given** (e.g., basis missing for a proposed amendment): the skill flags it in "Points for attorney review" rather than silently diverging. Do not substitute a different strategy.
- **Template missing**: stop and report it rather than improvising structure — the template is the firm's filing standard.

## Notes for the assistant

- Do not narrate this command file to the user. Just do the work.
- Do not repeat the skill's content — load and use it.
- This is a working draft for attorney review. Do not characterize the output as filed or final.
- Follow the skill's tone strictly: formal EPO register, no rhetorical flourishes, conditional sections (II, III, IV) included only when their grounds are raised — never as empty placeholders.
- Reference signs in quoted claim text must match the figures of the application as filed.
- If after delivery the user asks substantive follow-up questions about the draft itself (e.g., "why did you place the disclaimer in Section I rather than arguing under Section III?"), answer them — that is part of the same command's scope. Only refuse extensions to other tasks.
