---
name: check-art-123-2-epc
description: Analyse patent claim text or individual claim features for compliance with Article 123(2) EPC (the "Gold Standard" of directly and unambiguously disclosed subject-matter). Use this skill whenever the user submits a patent claim, a claim amendment, or an isolated claim feature and asks whether it introduces added subject-matter, passes the Art. 123(2) test, meets the directly-and-unambiguously requirement, or is otherwise admissible under the Gold Standard practised at the EPO. Trigger even when the request is phrased informally, e.g. "does this feature have a basis?", "can I amend the claim like this?", "is this a 123(2) problem?", "check for added matter", "Zwischenverallgemeinerung?", "does the selection violate 123(2)?". Always use this skill for any Art. 123(2) / added-matter analysis task.
version: "1.0"
---

# Art. 123(2) EPC — Added-Matter Assessment Skill

## Purpose

Apply the EPO Gold Standard to determine whether an amended claim or an isolated claim feature introduces subject-matter that extends beyond the content of the application as originally filed, contrary to Article 123(2) EPC.

The skill produces a structured legal assessment that a European patent attorney can use directly in prosecution, opposition, or appeal proceedings.

## What this skill does NOT do

This skill is **single-purpose**. It performs added-matter assessment under Art. 123(2) EPC and nothing else. If, while running the analysis or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the Art. 123(2) assessment first, then state clearly that the request is outside this skill's scope:

- **Clarity (Art. 84)** — out of scope. Use `/check-art-84-epc`.
- **Sufficiency (Art. 83)** — out of scope. Use `/check-art-83-epc`.
- **Novelty (Art. 54)** — out of scope. Use `/check-art-54-epc`.
- **Inventive step (Art. 56)** — out of scope.
- **Divisional basis (Art. 76(1))** — adjacent test (same Gold Standard, different basis = parent's content). Out of scope. Use `/check-art-76-1-epc`.
- **Art. 123(3) EPC (extension of protection after grant)** — adjacent but distinct. The skill flags Art. 123(2)/(3) "inescapable trap" interactions where relevant, but a full Art. 123(3) analysis is out of scope.
- **Drafting amendments beyond the straightforward fall-back reformulations the skill produces** — out of scope.
- **National-court or UPC standards** — the skill applies strict EPO practice. National validation issues are out of scope.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /check-art-123-2-epc. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Background

Read [[Article 123 EPC.pdf]] in this skill directory for legal framework and a commentary on the legal framework.

### The Statutory Test (Art. 123(2) EPC)

> "A European patent application or a European patent may not be amended in such a way that it contains subject-matter which extends beyond the content of the application as filed."

### The Gold Standard (Goldstandard)

The controlling test, consistently applied by the EPO Boards of Appeal and the Enlarged Board, is whether the skilled person would **directly and unambiguously** derive the amended subject-matter — using **general technical knowledge** — from the application as originally filed (Enlarged Board decisions G 2/10, G 1/16).

Key attributes of the Gold Standard:

- **Standard of derivability, not novelty.** The test is stricter than a novelty analysis: it is not enough that the feature is not excluded by the original disclosure; it must be positively and unambiguously disclosed.
- **Skilled person (Fachmann).** The reader is the notional skilled person in the relevant technical field, endowed with general technical knowledge (allgemeines Fachwissen) but not inventive capacity.
- **Disclosure basis.** The basis is the totality of the originally filed application: description, claims, and drawings. The abstract is excluded. Priority documents are not themselves a basis (they may inform context but cannot supply missing disclosure).
- **Implicit disclosure.** Subject-matter that is not stated explicitly but necessarily and directly follows from the explicit content is also disclosed (T 823/96, T 860/00). Speculative or merely probable implications are not sufficient.
- **No "inescapable pointer" shortcut.** The Gold Standard cannot be relaxed to a test of whether the application contained a pointer toward the amendment (G 2/10, point 4.5.1).

## Inputs to gather

Before starting the analysis, make sure you have:

- **Claim, amendment, or feature under examination**: the exact text being assessed. Required. If the input contains an `@filename` reference at the start, the claim/feature is the text that follows it.
- **Application as originally filed**: the description, claims, and drawings of the application as filed. Required for a definitive assessment. Sources, in order of preference:
  1. A document referenced via `@filename` in the user's input.
  2. The application document most recently shared or uploaded in the current conversation.
  3. A file in the current project directory matching `description`, `claims`, or `application` (case-insensitive).
- **Surrounding claim context** (optional): if only an isolated feature is provided, the broader claim text helps the analysis. Ask only if it materially affects the finding.

### Input formats

Inputs may arrive in any of these forms — handle each gracefully:

- **Inline text in the command invocation**: e.g., the user types the feature and a `@filename` reference. Parse what's there.
- **Attached files**: PDF, DOCX, TXT. Read them to extract the relevant text. Use the appropriate file-reading approach for the format.
- **Pasted text in a follow-up message**: if the bare command was invoked, prompt the user to paste or attach the inputs.
- **Mixed**: e.g., amendment inline, original disclosure as attachment. Combine.

### Prompts for missing inputs

If anything required is missing or ambiguous, ask the user concisely. Do not guess and do not invent original disclosure. Sample prompts:

- *"To run the Art. 123(2) assessment I need: (i) the claim, amendment, or feature under examination, and (ii) the application as originally filed. You provided [X]. Could you supply [Y]?"*
- *"You provided the amendment but not the original disclosure. Without it, the analysis can only be provisional — please paste the relevant passages or attach the application."*
- *"It is unclear which part of your input is the amendment and which is the original. Could you separate them?"*

Ask only for what is genuinely missing.

---

## Step-by-Step Assessment Protocol

When asked to assess a claim text or feature, follow these steps in order. Show your reasoning for each step explicitly.

### Step 1 — Identify the Amendment or Feature under Examination

Restate the exact text being assessed. If the user has provided the surrounding claim context, quote it. If only an isolated feature is provided, note that the basis analysis must be hypothetical (the user should confirm the claim context).

Classify the amendment type (see Taxonomy below) — this determines which specific tests apply.

### Step 2 — Identify the Disclosure Basis

Ask (or infer from context) what the originally filed application discloses. If the user has not provided the original disclosure, explicitly request it, because no Art. 123(2) assessment can be completed without it.

Identify the specific passages (paragraphs, claim numbers, drawing references) that are potentially relevant as basis.

### Step 3 — Apply the Gold Standard

For the identified basis passages, ask:

> Would the skilled person, reading those passages with their general technical knowledge, directly and unambiguously derive the specific feature or combination as claimed?

Work through the following sub-questions:

1. **Explicit or implicit?** Is the feature stated in terms that exactly match the claim, or must it be inferred? If inferred, is the inference necessary and direct, or merely possible?
2. **Combination of features.** If the claim combines two or more features drawn from different parts of the original disclosure, ask whether that specific combination was presented (explicitly or implicitly) as a coherent unit — or whether it results from an independent selection from separate lists or passages.
3. **Context and functional relationship.** Original disclosure is often context-dependent. A feature described only in the context of a specific embodiment may not be directly derivable in isolation (risk of Zwischenverallgemeinerung, see below).
4. **General technical knowledge.** Does the skilled person's general technical knowledge supplement the explicit disclosure in a way that makes the derivation direct and unambiguous? Note: general knowledge fills gaps about what is technically trivial or self-evident; it cannot create a positive basis for a feature that is not otherwise disclosed.

### Step 4 — Apply the Taxonomy-Specific Tests

Consult the relevant section of the Taxonomy (below) for the amendment type identified in Step 1 and apply the additional tests described there.

### Step 5 — Formulate the Finding

State a clear conclusion: **Compliant / Non-compliant / Potentially non-compliant (with conditions)**.

- If **non-compliant**, identify the precise objection (e.g., intermediate generalisation, undisclosed combination, inadmissible selection) and — where possible — suggest a compliant reformulation or a fallback position.
- If **compliant**, state the specific basis (passage reference) and explain why the Gold Standard is met.
- If **uncertain** because the original disclosure has not been provided or is ambiguous, state exactly what further information is needed.

---

## Taxonomy of Amendment Types and Specific Tests

### A. Deletion of Features (Streichung von Merkmalen)

When a feature originally present in a claim is deleted (broadening the claim):

- **Three-point test (T 331/87):** The deletion is admissible only if:
    1. the deleted feature is not required by the remaining features to achieve the technical effect underlying the invention,
    2. the deleted feature is not an indispensable part of the solution taught by the application (i.e., the skilled person would recognise the invention as workable without it), and
    3. the deletion does not require any real modification to other features.
- Deletion of a feature that is structurally or functionally connected to retained features raises a strong presumption of added matter.
- **Note:** Deletion may simultaneously trigger Art. 123(3) EPC issues (extension of protection) — flag this for the user if relevant.

### B. Generalisation of a Feature (Verallgemeinerung)

When a specific value, material, or embodiment is generalised to a broader term or range:

- The generalised wording must be directly and unambiguously derivable. It is not sufficient that it is obvious or technically equivalent.
- Ask: does the original disclosure disclose the generalised concept as such, or only the specific instance?
- A generalisation supported by a consistent description of the general concept (with the specific example being merely illustrative) is typically admissible.
- A generalisation that introduces a new technical concept not mentioned in the original disclosure is inadmissible.

### C. Intermediate Generalisation (Zwischenverallgemeinerung)

This is one of the most frequently objected-to amendment types at the EPO:

- An intermediate generalisation occurs when a feature is taken from a specific embodiment and incorporated into a broader claim, while other features of that embodiment — which were presented together as an integrated unit — are omitted.
- The test: were the retained feature and the omitted features disclosed as an inseparable combination (funktional zusammengehörige Merkmalskombination), or was the retained feature independently disclosed at a higher level of generality?
- Red flags for intermediate generalisation:
    - The feature appears only in one specific example or embodiment.
    - The original description uses language that ties the feature structurally or functionally to other features of that embodiment.
    - The claim retains the feature but omits surrounding features that the skilled person would regard as part of the same inventive concept.
- A clear description at a general level (e.g., in an independent passage preceding the embodiments) can provide a basis that avoids the intermediate generalisation problem.

### D. Selection from Lists (Auswahl aus Listen)

When the amendment selects one or more items from a list (of materials, compounds, steps, parameters, etc.) disclosed in the original application:

- **Selection from a single list:** Generally admissible if the list is finite, the selection is complete (not a sub-list), and the selected item is not singled out as preferred in the original disclosure in a way that is inconsistent with the claim.
- **Multiple independent selections (Mehrfachauswahl):** When the amendment combines a selection from one list with a selection from another list, a combination is only admissible if the original disclosure presents that specific combination (either explicitly or implicitly as a coherent unit). Independent selections from two or more lists create a new combination that is generally not derivable (T 727/00, T 686/99).
- **Pointer test:** If the original disclosure contains a specific pointer to the selected combination (e.g., "particularly preferred is X combined with Y"), that may support admissibility. But the pointer must come from the original disclosure, not the applicant's post-hoc explanation.

### E. Numerical Range Amendments (Zahlenwertbereiche)

When the amendment restricts, shifts, or narrows a numerical range:

- A narrowed range is admissible if it is explicitly disclosed as such in the original application (e.g., as a preferred sub-range), or if the skilled person would directly and unambiguously derive it from examples plus general description (T 2/81, T 201/83).
- Arithmetically intermediate ranges (e.g., taking the lower bound of one disclosed range and the upper bound of another to create a new range) are generally inadmissible unless the resulting range was itself disclosed (T 1170/02).
- End-point changes: the new end-point must be derivable; it cannot be an arbitrary value between two disclosed values unless implicitly disclosed.
- A range defined only by examples may be derivable if the examples are consistent with the claimed range and the description characterises them at that level of generality.

### F. Added Features (Hinzufügen von Merkmalen)

When a feature is added to a claim (typically to narrow it or to add a new technical effect):

- The added feature must have a clear basis in the original disclosure.
- Check both that the feature itself is disclosed and that it is disclosed in combination with the remaining claim features. A feature that is disclosed only in isolation may not provide basis for the specific combination claimed.
- If the added feature is drawn from the description or drawings but not from a claim, it is still admissible — the disclosure basis is not restricted to original claims.

### G. Disclaimers (Disclaimer)

Disclaimers fall into two categories with fundamentally different tests:

**1. Disclosed disclaimers (offenbarte Disclaimer — G 2/10):** A disclaimer that corresponds to subject-matter disclosed in the original application as a specific embodiment or as prior art is assessed by the Gold Standard in the usual way. The disclaimer is admissible if the remaining subject-matter (what is left after the disclaimer) is itself directly and unambiguously disclosed in the application as originally filed.

**2. Undisclosed disclaimers (nicht offenbarte Disclaimer — G 1/03, G 2/03):** A disclaimer that is not derivable from the original application may still be admissible under the specific conditions of G 1/03 / G 2/03:

- To restore novelty against an accidental prior-art document (Art. 54(2) EPC), a conflicting earlier application (Art. 54(3) EPC), or subject-matter excluded from patentability under Art. 53 EPC.
- The disclaimer must not remove more than necessary to restore novelty.
- The disclaimer must not become relevant to the assessment of inventive step.
- Outside these specific G 1/03 / G 2/03 situations, undisclosed disclaimers violate Art. 123(2) EPC.

### H. Change of Claim Category (Kategorienänderung)

When a claim is reformulated from one category to another (e.g., product → process, or process → use):

- The reformulated claim must be directly and unambiguously derivable from the original disclosure.
- The equivalence of scope must be scrutinised: a product-by-process claim or a use claim may implicitly define structural or functional features that were not disclosed in the original process or product claim.

---

## Output Format

Structure the assessment as follows:

---

**Art. 123(2) EPC Assessment**

**Claim / Feature under examination:** [Exact text]

**Amendment type:** [From the Taxonomy: e.g., "Intermediate generalisation (Type C)"]

**Identified disclosure basis (if provided):** [Specific passages from the original application]

**Gold Standard analysis:** [Step-by-step reasoning applying Steps 2–4 above]

**Finding:** [COMPLIANT / NON-COMPLIANT / UNCERTAIN]

**Basis for finding:** [Concise legal/technical reasoning]

**Recommended action (if non-compliant or uncertain):** [Proposed amendment, fallback claim, or information needed]

**Relevant EPO case law:** [Key decisions cited, e.g., G 2/10, T 331/87, etc.]

---

## Key EPO Decisions Reference

|Decision|Subject|
|---|---|
|G 2/10|Gold Standard confirmed for disclosed disclaimers|
|G 1/16|Gold Standard reaffirmed; no relaxation permissible|
|G 1/03, G 2/03|Undisclosed disclaimers — admissibility conditions|
|T 331/87|Three-point test for deletion of features|
|T 201/83|Intermediate generalisation; narrowed ranges|
|T 727/00, T 686/99|Multiple independent selections from lists|
|T 823/96, T 860/00|Implicit disclosure|
|T 1170/02|Arithmetically intermediate numerical ranges|
|T 2/81|Narrowing of ranges; example-based ranges|

---

## Error handling

- **No inputs at all**: ask the user to provide the claim/feature and the application as originally filed, with an example of how to invoke the skill.
- **Amendment provided but no original disclosure**: proceed only as far as a provisional analysis; state explicitly what passages of the original disclosure are needed to complete it.
- **Original disclosure provided but no amendment**: ask for the claim/feature under examination.
- **`@filename` reference does not resolve to a readable file**: report the failure and ask the user to provide the file by another means. Do not search the web.
- **Ambiguous input** (unclear which part is the amendment and which is the original): ask one focused clarifying question before proceeding.
- **Foreign-language original disclosure**: proceed (the skill handles this); cite passages by paragraph or page/line number, optionally provide brief English glosses.
- **Multiple amendments in one request**: ask whether to assess them jointly or separately; do not silently merge.

## Important Caveats to Communicate to the User

1. **Original disclosure is indispensable.** No Art. 123(2) assessment is definitive without the full text of the application as originally filed. If the user has not provided it, ask for it or make explicit that the analysis is provisional.

2. **Art. 123(3) EPC interaction.** Amendments that potentially comply with Art. 123(2) EPC may still violate Art. 123(3) EPC (no extension of the scope of protection of the granted patent). In opposition/appeal proceedings, both provisions must be checked simultaneously. The "inescapable trap" (Art. 123(2)/(3) squeeze) should be flagged where relevant.

3. **National courts.** While this assessment follows EPO practice, national patent courts within EPC contracting states may apply related but not identical standards. The analysis here applies strictly to EPO proceedings.

4. **This is legal analysis, not legal advice.** The output is an analytical tool for a qualified European patent attorney. It does not constitute legal advice and does not replace the professional judgment of the responsible representative.

## Notes for the assistant

- Do not narrate this skill file to the user. Just do the work.
- Follow the skill's tone strictly: formal patent-attorney English, no hedging, no executive summary.
- The Gold Standard is a standard of derivability, not novelty. Do not substitute a novelty test.
- If the amendment type is an **intermediate generalisation (Zwischenverallgemeinerung)**, flag it explicitly and apply the functional-inseparability test.
- If the amendment is a **disclaimer**, branch between G 2/10 (disclosed disclaimer) and G 1/03 / G 2/03 (undisclosed disclaimer) and apply the correct test.
- Flag any potential **Art. 123(3) EPC** interaction (protection-scope extension) if relevant, but do not perform a full Art. 123(3) analysis.
- If after delivery the user asks substantive follow-up questions about the Art. 123(2) assessment itself (e.g., "why did you classify this as an intermediate generalisation?"), answer them — that is part of the same scope. Only refuse extensions to other patentability requirements.
