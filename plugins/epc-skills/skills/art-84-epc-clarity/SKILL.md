---
name: art-84-epc-clarity
description: Assess clarity and conciseness of one or more patent claims under Article 84 EPC, based on the claim wording alone. Use this skill whenever the user wants to check whether a claim is clear and concise — for example, when reviewing a draft application before filing, preparing a response to an EPO examination report under Art. 84, screening a competitor's claims for vulnerabilities, evaluating amendments after opposition, or doing a pre-filing claim review. Trigger this skill whenever the user mentions "clarity", "Art. 84 EPC", "Klarheit", "indefinite", "ambiguous claim", "concise claims", or asks to review claim wording for defects, even if they don't explicitly say "clarity assessment". Do NOT use this skill for support objections (Art. 84, third requirement), sufficiency (Art. 83), added matter (Art. 123(2)), or novelty/inventive step.
---

# Clarity and Conciseness Assessment under Article 84 EPC

This skill produces a strict, EPO-style review of one or more patent claims for **clarity** and **conciseness** under Art. 84 EPC, based on the claim wording alone. The output is a tabular issue list per claim plus cross-claim observations, written for a patent attorney audience (assume the reader knows EPC terminology). Suggested fixes are provided where straightforward.

## What this skill does NOT do

- **Support by the description (Art. 84, third requirement)** — out of scope. Support requires comparing the claim against the description, not just reading the claim. If the user asks for a support assessment, redirect or ask for the description.
- **Sufficiency of disclosure (Art. 83 EPC)** — out of scope. Sufficiency is about whether the skilled person can carry out the invention; clarity is about whether they can determine its scope. Clarity defects are not the same as sufficiency defects, even though they sometimes look similar (G 1/03, point 2.5.2 of the reasons; G 3/14).
- **Added matter (Art. 123(2) EPC)** — out of scope. If the claim is an amended claim and the user wants to check both clarity and added matter, run this skill first and note that an Art. 123(2) check is separate.
- **Novelty / inventive step (Art. 54, 56 EPC)** — out of scope. Use the novelty-assessment-epc skill (or a separate inventive-step assessment) for those.
- **Clarity of granted claims (G 3/14)** — important caveat. Under G 3/14, the clarity of a claim as granted cannot be re-examined in opposition unless an amendment introduces a clarity defect. If the user is in opposition and asks about clarity of unamended granted claims, flag this limitation but still perform the analysis if requested (the analysis may still be useful for understanding the claim, even if it cannot ground an opposition objection).

If the user asks for any of the above after the clarity assessment is done, that's fine to do as a follow-up — but the assessment itself stays clean.

## Inputs to gather

Before starting the analysis, make sure you have:

1. **The claim(s) to be assessed.** Process exactly the claims provided. If multiple claims are given, assess each individually and also look for cross-claim issues (inconsistencies between claims, redundant claims, unclear back-references).
2. **The description and figures** (optional). Not used to find clarity defects, but the skilled person reads the claim in light of the description, so a defect that is resolved by the description may be a less serious clarity issue than one that is not. If the user provides the description, use it only to gauge severity, not to introduce or eliminate issues. The claim must be clear *on its own*: it is the wording of the claims that defines the protection, not the description (Art. 69 EPC and the established case law that Art. 69 governs scope of protection but does not cure unclear claims for the purposes of Art. 84 — see *Case Law of the Boards of Appeal*, II.A.6.3.).
3. **Indication of any user-specific concerns.** If the user has a particular suspicion (e.g., "the term 'substantially' worries me"), address it explicitly — but do not limit the analysis to that.

If only the claims are provided, that is sufficient. Do not block the analysis waiting for the description.

## The legal standard (apply strictly)

The skill applies the EPO Guidelines F-IV and established Boards of Appeal case law:

### Clarity (Art. 84, second requirement)

A claim is unclear if the skilled person, reading the claim in light of the common general knowledge in the relevant technical field, cannot determine the scope of protection it confers. The standard is set out in EPO Guidelines F-IV, 4 and the established case law:

- **Each claim must be clear on its own.** It is not sufficient that the description clarifies the meaning. Art. 69 EPC governs the extent of protection of a granted patent but does not relax the clarity requirement during examination (Guidelines F-IV, 4.2; *Case Law*, II.A.6.3).
- **Relative terms** ("thin", "wide", "strong", "high", "approximately", "substantially", "about") are unclear unless the term has a well-recognised meaning in the art (e.g., "high-frequency amplifier") or the claim itself provides a precise reference point. Mere recourse to the description to define the term is generally not enough (Guidelines F-IV, 4.6).
- **Result-to-be-achieved** clauses are typically unclear unless the result can be unambiguously determined by tests or procedures specified in the claim or known to the skilled person, and the claim does not amount to mere reservation of any solution achieving the result (Guidelines F-IV, 4.10).
- **Optional features** ("preferably", "if desired", "for example", "such as", "in particular") within a claim create ambiguity about scope and are typically unclear (Guidelines F-IV, 4.9). Tolerable in some narrow forms (e.g., dependent-claim language in a chain), but generally to be avoided in independent claims.
- **Use of trade marks** is unclear because trade marks may change in meaning over time and identify origin, not technical content (Guidelines F-IV, 4.8).
- **Negative limitations / disclaimers**: permissible if clear; problematic if the excluded subject-matter is itself unclear (Guidelines F-IV, 4.20; G 1/03, G 2/03, G 1/16).
- **Open-ended ranges** ("at least", "more than X", "up to") are not per se unclear, but combined with technical impossibility (e.g., "at least 200 % efficiency") become objectionable (Guidelines F-IV, 4.7).
- **Parameters and unusual parameters** must be measurable by methods known to the skilled person, otherwise the parameter is unclear; if a measurement method is not specified and several methods give different results, the parameter is typically unclear (Guidelines F-IV, 4.11; T 1845/14).
- **"Comprising" vs "consisting of"** — both are clear in principle; flag only if the claim mixes them inconsistently or uses an unusual variant.
- **Functional features** are permissible if the function is sufficiently defined and the skilled person can identify means for performing it without undue burden (Guidelines F-III, 8; F-IV, 4.10; F-IV, 6.5). "Means for [doing X]" claims need clarity of the function itself.
- **Two-part form (Rule 43(1) EPC)** — formal requirement, not strictly a clarity issue, but if requested in two-part form and not provided, this is a Rule 43 deficiency that examiners typically raise alongside clarity. Flag if relevant.
- **Reference signs in claims** — Rule 43(7); reference signs do not limit the claim. Missing reference signs in a claim with figures is a Rule 43(7) issue, not strictly Art. 84.
- **Inconsistency between claim and description** — relevant for support, not clarity per se. Out of scope (see exclusions).
- **Categories of claims** (product, process, use, apparatus) — a claim must be of a single category. Mixed-category claims (e.g., "a method of using device X comprising parts A, B, C") may be unclear (Guidelines F-IV, 3.1).
- **Essential features** — a claim must contain all features essential for defining the invention (Art. 84 first sentence; Guidelines F-IV, 4.5). If a feature obviously essential to the invention's operation is missing, this is a clarity defect.

### Conciseness (Art. 84, second requirement)

Claims must be concise both individually and as a set. Apply Guidelines F-IV, 5:

- **Number of claims** — must be reasonable in view of the nature of the invention. Excessive numbers of claims, or many independent claims of the same category, may violate conciseness (Rule 43(2) EPC limits independent claims per category; Guidelines F-IV, 3.2).
- **Repetition** — claims that repeat features already covered by other claims, or that overlap in scope without adding distinguishing matter, are not concise.
- **Prolix claim wording** — a single claim that is unnecessarily long, with redundant phrases or repeated definitions, is not concise.
- **Multiple independent claims of the same category** are only allowed in the limited cases of Rule 43(2)(a)–(c) EPC. Flag if the claim set has more than one independent claim of the same category and does not appear to fall within those exceptions.

## Workflow

### Step 1: Read each claim carefully

For each claim, identify its category (product, process, use, apparatus) and structure (preamble, characterising portion if two-part). Note any back-references to other claims.

### Step 2: Issue spotting

Go through each claim and identify potential clarity and conciseness issues. Be systematic — work through this checklist mentally for each claim:

1. Are there relative or vague terms ("substantially", "approximately", "about", "thin", "high", "low")?
2. Are there optional features ("preferably", "such as", "for example", "in particular", "if desired")?
3. Are there result-to-be-achieved formulations not tied to specific structural or procedural features?
4. Are there parameters? If so, is a measurement method specified or known?
5. Are there functional features ("means for", "configured to", "adapted to")? Is the function sufficiently defined?
6. Are there negative limitations / disclaimers? Is what is excluded clear?
7. Are there trade marks?
8. Are there open-ended ranges with technical issues?
9. Is the claim category single, or mixed?
10. Are essential features missing?
11. Are back-references correct (e.g., "claim 1" exists; dependent claims reference an existing claim)?
12. Is the wording prolix or repetitive?
13. Are there inconsistencies in terminology within the claim (e.g., "the device" vs "said apparatus" referring to the same thing)?
14. Is "comprising"/"consisting of" used consistently?

### Step 3: Cross-claim check

After per-claim review, check the claim set as a whole:

- Multiple independent claims of the same category — Rule 43(2) issue?
- Inconsistent terminology between claims (e.g., "the housing" in claim 2 referring to the "casing" of claim 1)?
- Dependent claims that don't actually narrow the scope?
- Redundant claims that cover the same scope as other claims?
- Total number of claims reasonable?

### Step 4: Categorise each issue

For each issue identified, assign:

- **Type**: one of {Relative term, Optional feature, Result-to-be-achieved, Parameter, Functional feature, Disclaimer, Trade mark, Open range, Mixed category, Missing essential feature, Back-reference, Prolix, Inconsistent terminology, Comprising/consisting, Conciseness — claim set, Conciseness — single claim, Other}
- **Severity**: one of {High, Medium, Low}
  - **High** — examiner will almost certainly raise this; the claim's scope cannot be determined or the defect is patent.
  - **Medium** — examiner is likely to raise this; reasonable people could disagree.
  - **Low** — minor stylistic or borderline issue; may or may not be raised, but worth noting.
- **Suggested fix**: a concrete proposed wording where the fix is straightforward (e.g., delete "preferably", replace "thin" with a specific dimension, specify a measurement method). If the fix would require substantive amendment beyond simple rewording (e.g., adding a feature from the description), say so but do not invent the feature — write "Requires substantive amendment from description" or similar.

### Step 5: Build the table

One table per claim, using this exact column structure:

| Issue # | Claim wording | Type | Severity | Suggested fix |

- **Issue #**: 1.1, 1.2, 1.3 for issues in claim 1; 2.1, 2.2 for claim 2; etc.
- **Claim wording**: the specific phrase or feature in the claim that gives rise to the issue (verbatim, in quotes).
- **Type**: from the categories above.
- **Severity**: High / Medium / Low, with a one-sentence justification if not obvious.
- **Suggested fix**: concrete proposal, or "Requires substantive amendment" with brief explanation.

If a claim has no issues, write a single line under its heading: "No clarity or conciseness issues identified."

### Step 6: Cross-claim section

After all per-claim tables, add a "Cross-claim observations" section listing any issues affecting the claim set as a whole (Rule 43(2), inconsistencies, conciseness of the set). Use the same table format with the issue numbered "X.1, X.2" (X for cross-claim). If none, write "No cross-claim issues identified."

### Step 7: Cross-check before finalizing

Before delivering, sanity-check:

- Did you accidentally raise support (Art. 84 third requirement) issues? Move out of scope.
- Did you accidentally raise sufficiency (Art. 83) issues? Move out of scope.
- Did you raise an issue that depends on knowing the prior art (e.g., "this term is broader than the disclosed embodiments")? That's support or scope-of-protection, not clarity.
- For each "High" severity issue: is the claim genuinely ambiguous, or merely broad? Broad ≠ unclear.
- Did you propose a fix that introduces added matter (Art. 123(2))? Flag this as a caveat in the fix column.
- For granted claims in opposition: did you note the G 3/14 limitation if relevant?

## Output format

Strictly tabular per claim plus a cross-claim section, English language, no preamble beyond the title. Structure:

```
# Clarity and Conciseness Assessment under Art. 84 EPC

**Claim(s) analyzed:** [list]
**Procedural context:** [examination / opposition / pre-filing review / other — note if relevant, e.g., G 3/14 caveat for opposition]

## Claim 1 [label, e.g., "(independent, apparatus)"]

| Issue # | Claim wording | Type | Severity | Suggested fix |
|---|---|---|---|---|
| 1.1 | "…" | … | High / Med / Low | … |
| 1.2 | "…" | … | … | … |

## Claim 2 …

[as above for each claim]

## Cross-claim observations

| Issue # | Claim wording | Type | Severity | Suggested fix |
|---|---|---|---|---|
| X.1 | … | … | … | … |
```

No executive summary at the top, no general remarks at the bottom. The patent attorney reads the tables; that's the deliverable.

## Tone and register

Write in formal, neutral patent-attorney English. Use EPC terminology precisely ("the skilled person", "scope of protection", "essential feature", "two-part form"). Cite Guidelines sections (F-IV, 4.6) and case law decisions (T 1845/14, G 3/14) where they directly support an issue, but do not pad with citations.

Avoid hedging language ("arguably", "one might think") — the severity column carries the nuance. If an issue is genuinely borderline, mark it Low severity rather than softening the description.

## Edge cases to handle gracefully

- **Single claim provided**: skip the cross-claim section entirely (or write "Not applicable — single claim").
- **Claims in a foreign language**: work with them, cite in the original language, optionally provide a brief English gloss in the issue description. Do not refuse the task.
- **Computer-implemented inventions**: "computer-readable medium", "computer program product", "method comprising the steps of …" — flag mixed-category issues if the claim conflates apparatus and method.
- **Functional language with `wherein` clauses**: `wherein` clauses are clear unless they introduce ambiguity about whether the wherein-feature is limiting or merely descriptive.
- **Use claims**: ensure the use is to a defined entity for a defined purpose; "use of X for Y" is clear, "use of X" alone is not.

## Self-check: am I doing clarity, or something else?

If you find yourself writing any of the following, stop — you've drifted out of Art. 84 clarity:

- "this is broader than what is disclosed in the description" — that's support (out of scope) or written description, not clarity.
- "the skilled person could not carry out this invention" — that's sufficiency (Art. 83), not clarity.
- "this feature is not new in light of [prior art]" — that's novelty (Art. 54), not clarity.
- "this would be obvious" — that's inventive step (Art. 56), not clarity.
- "this feature was not in the original application" — that's added matter (Art. 123(2)), not clarity.

Clarity is about whether the skilled person can determine the scope of protection from the claim wording. If you are reasoning about anything else — the prior art, the description, the original disclosure, or whether the invention works — you have drifted out of scope.