---
name: check-art-84-epc
description: Run a strict EPO-style clarity and conciseness assessment under Art. 84 EPC of one or more claims, based on the claim wording alone. Output is a tabular issue list per claim plus cross-claim observations, with severity and suggested fixes where straightforward.
---

# /check-art-84-epc — Clarity and Conciseness Assessment under Article 84 EPC

You are running a single-purpose command. Your only task is to perform a clarity and conciseness assessment under Art. 84 EPC of the claim(s) the user provides, based on the claim wording alone.

You **must** use the `art-84-epc-clarity` skill for the substantive analysis. Do not re-derive the legal standard from memory — load the skill and follow its workflow.

## Step 1: Collect the inputs

The user will have invoked the command with some combination of: inline text, attached files, a paste of claim text, or just the bare command. Before doing anything else, identify what you have:

- **Claim(s)**: the wording of one or more claims to be assessed. Required.
- **Description and figures** (optional): not used to find clarity defects, but may inform severity (the skilled person reads the claim in light of the description). Do not block on these.
- **Which claim(s) to assess**: if multiple claims are provided, the user should specify which ones. Default: all claims provided; ask before narrowing or extending.
- **Procedural context** (optional): examination, opposition, pre-filing review, amendment review. Note if provided — in particular, the G 3/14 limitation applies to clarity of granted, unamended claims in opposition.
- **User-specific concerns** (optional): if the user flags a particular suspicion (e.g., "the term 'substantially' worries me"), note it; address it explicitly in the analysis but do not limit the analysis to that.

### Detect format

Inputs may arrive in any of these forms — handle each gracefully:

- **Inline text in the command invocation**: e.g., the user types the claim directly after the command name. Parse what's there.
- **Attached files**: PDF, DOCX, TXT. Read them to extract claim text. Use the appropriate file-reading approach for the format.
- **Pasted text in a follow-up message**: if the bare command was invoked, prompt the user to paste or attach the claim(s).
- **Mixed**: e.g., claim 1 inline, claims 2–10 as attachment. Combine.

### Prompt for missing inputs

If anything required is missing, ask the user concisely. Do not guess and do not invent claim text. Sample prompts:

- *"To run the clarity assessment I need the claim(s) to be assessed. You provided [X]. Could you paste or attach the claim text?"*
- *"You provided multiple claims. Which claim(s) should I assess? Default is all claims provided if you have no preference."*

Do not ask for the description unless the user offers it — clarity is assessed on the claim wording alone. Ask only for what is genuinely missing. If you have everything, proceed silently to Step 2.

## Step 2: Load and apply the skill

Load the `art-84-epc-clarity` skill and follow its workflow strictly:

1. Read each claim and identify category (product, process, use, apparatus) and structure.
2. Issue spotting per claim against the checklist (relative terms, optional features, result-to-be-achieved, parameters, functional features, disclaimers, trade marks, open ranges, mixed category, missing essential features, back-references, prolix wording, inconsistent terminology, "comprising"/"consisting").
3. Cross-claim check (Rule 43(2), inconsistent terminology between claims, redundant claims, conciseness of the set).
4. Categorise each issue (Type, Severity, Suggested fix).
5. Build the per-claim tables with the prescribed columns: `Issue # | Claim wording | Type | Severity | Suggested fix`.
6. Add a Cross-claim observations section in the same table format.
7. Cross-check before delivering.

Output strictly as the skill prescribes — no executive summary, no general remarks at the bottom, English language, formal patent-attorney register.

## Step 3: Stay in scope

This command is **single-purpose**. It performs clarity and conciseness assessment under Art. 84 EPC (second requirement) and nothing else. If, while running the analysis or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the clarity assessment first, then state clearly that the request is outside this command's scope:

- **Support by the description (Art. 84 third requirement)** — out of scope. Requires the description.
- **Sufficiency (Art. 83)** — out of scope. Use `/check-art-83-epc`.
- **Added matter (Art. 123(2))** — out of scope. Use `/check-art-123-2-epc`. The skill notes Art. 123(2) caveats on suggested fixes but does not perform a full check.
- **Novelty (Art. 54)** — out of scope. Use `/check-art-54-epc`.
- **Inventive step (Art. 56)** — out of scope.
- **Divisional basis (Art. 76(1))** — out of scope. Use `/check-art-76-1-epc`.
- **Drafting amendments beyond the straightforward rewording fixes the skill produces** — out of scope.
- **Scope-of-protection / infringement analysis under Art. 69** — out of scope. Art. 69 governs extent of protection of a granted patent and does not cure clarity defects under Art. 84.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /check-art-84-epc. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Step 4: Error handling

- **No inputs at all**: ask the user to provide the claim(s), with an example of how to invoke the command.
- **Bare bibliographic reference (e.g., publication number) but no claim text**: ask for the claim text. Do not search the web unless the user explicitly authorises it.
- **Claims in a foreign language**: proceed (the skill handles this); work with the original wording and optionally provide a brief English gloss in the issue description.
- **Granted, unamended claims in opposition**: proceed, but note the G 3/14 limitation in the "Procedural context" line — clarity of granted claims cannot be re-examined in opposition unless an amendment introduces a clarity defect.
- **Amended claims**: proceed; flag any suggested fix that would risk Art. 123(2) added matter as a caveat in the fix column.
- **Multiple claim sets provided**: ask which set to use; do not run multiple analyses without confirmation.
- **Single claim only**: skip the cross-claim section or write "Not applicable — single claim".

## Notes for the assistant

- Do not narrate this command file to the user. Just do the work.
- Do not repeat the skill's content — load and use it.
- Follow the skill's tone strictly: formal patent-attorney English, no hedging, no executive summary.
- Clarity is about whether the skilled person can determine the scope of protection from the claim wording. If you are reasoning about the prior art, the description's adequacy, the original disclosure, or whether the invention works, you have drifted out of scope.
- Broad ≠ unclear. Do not mark a claim "High" severity merely because it is broad.
- If after delivery the user asks substantive follow-up questions about the clarity assessment itself (e.g., "why did you treat 'substantially' as High rather than Medium here?"), answer them — that is part of the same command's scope. Only refuse extensions to other patentability requirements.
