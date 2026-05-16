---
name: check-art-123-2-epc
description: Run a strict EPO-style added-matter assessment under Art. 123(2) EPC of a claim, claim amendment, or isolated claim feature against the application as originally filed. Applies the Gold Standard (directly and unambiguously derivable). Output is a structured Compliant / Non-compliant / Uncertain finding with cited basis and recommended action.
argument-hint: "[@filename] claim feature or amendment under examination"
---

# /check-art-123-2-epc — Added-Matter Assessment under Article 123(2) EPC

You are running a single-purpose command. Your only task is to perform an added-matter assessment under Art. 123(2) EPC of the claim, claim amendment, or isolated claim feature the user provides, against the application as originally filed.

You **must** use the `art-123-2-epc-added-matter` skill for the substantive analysis. Do not re-derive the legal standard from memory — load the skill and follow its workflow.

## Step 1: Collect the inputs

The user will have invoked the command with some combination of: inline text, attached files, an `@filename` reference, a paste of claim and original disclosure text, or just the bare command. Before doing anything else, identify what you have:

- **Claim, amendment, or feature under examination**: the exact text being assessed. Required. If `$ARGUMENTS` contains an `@filename` reference at the start, the claim/feature is the text that follows it.
- **Application as originally filed**: the description, claims, and drawings of the application as filed. Required for a definitive assessment. Sources, in order of preference:
  1. A document referenced via `@filename` in `$ARGUMENTS`.
  2. The application document most recently shared or uploaded in the current conversation.
  3. A file in the current project directory matching `description`, `claims`, or `application` (case-insensitive).
- **Surrounding claim context** (optional): if only an isolated feature is provided, the broader claim text helps the analysis. Ask only if it materially affects the finding.

### Detect format

Inputs may arrive in any of these forms — handle each gracefully:

- **Inline text in the command invocation**: e.g., the user types the feature and a `@filename` reference. Parse what's there.
- **Attached files**: PDF, DOCX, TXT. Read them to extract the relevant text. Use the appropriate file-reading approach for the format.
- **Pasted text in a follow-up message**: if the bare command was invoked, prompt the user to paste or attach the inputs.
- **Mixed**: e.g., amendment inline, original disclosure as attachment. Combine.

### Prompt for missing inputs

If anything required is missing or ambiguous, ask the user concisely. Do not guess and do not invent original disclosure. Sample prompts:

- *"To run the Art. 123(2) assessment I need: (i) the claim, amendment, or feature under examination, and (ii) the application as originally filed. You provided [X]. Could you supply [Y]?"*
- *"You provided the amendment but not the original disclosure. Without it, the analysis can only be provisional — please paste the relevant passages or attach the application."*
- *"It is unclear which part of your input is the amendment and which is the original. Could you separate them?"*

Ask only for what is genuinely missing. If you have everything, proceed silently to Step 2.

## Step 2: Load and apply the skill

Load the `art-123-2-epc-added-matter` skill and follow its workflow strictly:

1. Identify the amendment or feature under examination; restate exactly; classify the amendment type per the Taxonomy (A–H).
2. Identify the disclosure basis — the specific passages of the application as originally filed that are potentially relevant.
3. Apply the Gold Standard (G 2/10, G 1/16): would the skilled person, using general technical knowledge, directly and unambiguously derive the feature or combination from the original disclosure?
4. Apply the taxonomy-specific tests (e.g., three-point test for deletions T 331/87, intermediate-generalisation test, multiple-selection test T 727/00, ranges T 1170/02 / T 2/81, G 1/03 / G 2/03 for undisclosed disclaimers).
5. Formulate the finding: **COMPLIANT / NON-COMPLIANT / UNCERTAIN**.
6. Output strictly in the skill's prescribed format: `Claim / Feature under examination | Amendment type | Identified disclosure basis | Gold Standard analysis | Finding | Basis for finding | Recommended action | Relevant EPO case law`.

Output strictly as the skill prescribes — no executive summary, no general remarks at the bottom, English language, formal patent-attorney register.

## Step 3: Stay in scope

This command is **single-purpose**. It performs added-matter assessment under Art. 123(2) EPC and nothing else. If, while running the analysis or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the Art. 123(2) assessment first, then state clearly that the request is outside this command's scope:

- **Clarity (Art. 84)** — out of scope. Use `/check-art-84-epc`.
- **Sufficiency (Art. 83)** — out of scope. Use `/check-art-83-epc`.
- **Novelty (Art. 54)** — out of scope. Use `/check-art-54-epc`.
- **Inventive step (Art. 56)** — out of scope.
- **Divisional basis (Art. 76(1))** — adjacent test (same Gold Standard, different basis = parent's content). Out of scope. Use `/check-art-76-1-epc`.
- **Art. 123(3) EPC (extension of protection after grant)** — adjacent but distinct. The skill flags Art. 123(2)/(3) "inescapable trap" interactions where relevant, but a full Art. 123(3) analysis is out of scope.
- **Drafting amendments beyond the straightforward fall-back reformulations the skill produces** — out of scope.
- **National-court or UPC standards** — the skill applies strict EPO practice. National validation issues are out of scope.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /check-art-123-2-epc. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Step 4: Error handling

- **No inputs at all**: ask the user to provide the claim/feature and the application as originally filed, with an example of how to invoke the command.
- **Amendment provided but no original disclosure**: proceed only as far as a provisional analysis; state explicitly what passages of the original disclosure are needed to complete it.
- **Original disclosure provided but no amendment**: ask for the claim/feature under examination.
- **`@filename` reference does not resolve to a readable file**: report the failure and ask the user to provide the file by another means. Do not search the web.
- **Ambiguous input** (unclear which part is the amendment and which is the original): ask one focused clarifying question before proceeding.
- **Foreign-language original disclosure**: proceed (the skill handles this); cite passages by paragraph or page/line number, optionally provide brief English glosses.
- **Multiple amendments in one request**: ask whether to assess them jointly or separately; do not silently merge.

## Notes for the assistant

- Do not narrate this command file to the user. Just do the work.
- Do not repeat the skill's content — load and use it.
- Follow the skill's tone strictly: formal patent-attorney English, no hedging, no executive summary.
- The Gold Standard is a standard of derivability, not novelty. Do not substitute a novelty test.
- If the amendment type is an **intermediate generalisation (Zwischenverallgemeinerung)**, flag it explicitly and apply the functional-inseparability test.
- If the amendment is a **disclaimer**, branch between G 2/10 (disclosed disclaimer) and G 1/03 / G 2/03 (undisclosed disclaimer) and apply the correct test.
- Flag any potential **Art. 123(3) EPC** interaction (protection-scope extension) if relevant, but do not perform a full Art. 123(3) analysis.
- If after delivery the user asks substantive follow-up questions about the Art. 123(2) assessment itself (e.g., "why did you classify this as an intermediate generalisation?"), answer them — that is part of the same command's scope. Only refuse extensions to other patentability requirements.
