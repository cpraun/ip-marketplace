---
name: art-83-epc-sufficiency
description: Assess sufficiency of disclosure of one or more patent claims under Article 83 EPC, based on the application as filed (claims and description, with figures optional). Use this skill whenever the user wants to check whether a claimed invention is disclosed in a manner sufficiently clear and complete for the skilled person to carry it out — for example, when reviewing a draft application before filing, preparing a response to an EPO examination report under Art. 83, evaluating an opposition ground under Art. 100(b) EPC, screening a competitor's patent for sufficiency vulnerabilities, or assessing whether a broad functional or parameter-based claim is enabled across its whole scope. Trigger this skill whenever the user mentions "sufficiency", "Art. 83 EPC", "enablement", "carry out the invention", "Ausführbarkeit", "undue burden", "whole-scope sufficiency", "plausibility" (G 2/21), or asks whether the description supports performing the invention. Do NOT use this skill for clarity (Art. 84), support (Art. 84 third requirement), added matter (Art. 123(2)), or novelty/inventive step.
---

# Sufficiency of Disclosure Assessment under Article 83 EPC

This skill produces a strict, EPO-style sufficiency assessment of one or more patent claims under Art. 83 EPC, based on the application as filed (or the patent specification). The output is a tabular issue list per claim, written for a patent attorney audience (assume the reader knows EPC terminology). Suggested amendments are provided where a straightforward restriction to enabled subject-matter is supported by the description.

## What this skill does NOT do

- **Clarity (Art. 84 EPC)** — out of scope. Use the clarity-assessment-epc skill. Note that clarity and sufficiency objections sometimes target the same wording (e.g., unclear parameters); the established case law treats unclear features as primarily a clarity issue (T 1845/14), but if the unclarity prevents the skilled person from carrying out the invention across the whole scope, sufficiency is engaged (T 593/09 line). When in doubt, the present skill flags the issue under sufficiency only if the *carrying out* is affected, not merely the *delimiting* of scope.
- **Support by the description (Art. 84, third requirement)** — out of scope. Support is a comparison between claim breadth and what is disclosed; sufficiency is about whether the disclosure enables the skilled person to perform the invention. The two questions overlap but are conceptually distinct (T 409/91, point 3.5 of the reasons; G 1/03, point 2.5.2).
- **Added matter (Art. 123(2) EPC)** — out of scope. If a suggested fix requires amendment, the skill notes the supporting passage in the description but does not perform a full Art. 123(2) analysis.
- **Novelty / inventive step (Art. 54, 56 EPC)** — out of scope.
- **Industrial applicability (Art. 57 EPC)** — out of scope.
- **Full plausibility analysis under G 2/21 across all subject-matter** — only invoked where the claim involves a technical effect (see workflow Step 3). The skill does not turn into a G 2/21 treatise for claims where no technical effect is asserted.

If the user asks for any of the above after the sufficiency assessment is done, that's fine to do as a follow-up — but the assessment itself stays clean.

## Inputs to gather

Before starting the analysis, make sure you have:

1. **The claim(s) to be assessed.** Process exactly the claims provided. If the user does not specify, default to all independent claims; ask before extending to dependents.
2. **The description.** Mandatory. Sufficiency cannot be assessed from the claims alone. If only claims are provided, ask the user for the description before proceeding. Do not invent description content.
3. **Figures** (optional). Useful where the invention has structural or schematic content; not required.
4. **The technical field**, either explicit or inferable from the description. The skilled person and the common general knowledge are field-specific, so identify the field early.
5. **Procedural context** (optional). Examination, opposition, pre-filing review, or freedom-to-operate. Note the context if the user provides it.

If the description is missing, ask once before proceeding. Do not run a sufficiency assessment on claims alone.

## The legal standard (apply strictly)

The skill applies EPO Guidelines F-III and the established case law:

### Core test

The skilled person, equipped with the application as filed and common general knowledge in the relevant technical field at the filing date (or priority date, where applicable), must be able to **carry out the invention** across the whole scope of the claim **without undue burden** (Art. 83 EPC; Guidelines F-III, 1; T 409/91; T 435/91).

- **"Carry out"** means produce the claimed product, perform the claimed process, or use the claimed entity for the claimed purpose. Mere understanding of the invention is not enough.
- **"Without undue burden"** means without an excessive amount of experimentation, trial-and-error, or research effort. Routine experimentation is acceptable; a research programme is not (T 435/91; Guidelines F-III, 1).
- **"Whole scope"** means every embodiment falling under the claim. A claim covering classes of compounds, ranges of parameters, or functional definitions is sufficiently disclosed only if the skilled person can carry out essentially all embodiments within the scope (T 409/91; T 1063/06; *Case Law of the Boards of Appeal*, II.C.5).

### Specific issue patterns

- **Whole-scope sufficiency** — the most common ground for sufficiency objections. A broad claim that is enabled only for a narrow subset of embodiments is insufficient (T 409/91; T 1063/06). Typical for: broad chemical genus claims, claims with broad parameter ranges, "means for" claims with broad functions.
- **Inventive contribution must be reproducible** — if the alleged inventive contribution lies in achieving a particular technical effect, the skilled person must be able to achieve that effect across the scope of the claim. Failure here is sufficiency, not (or not only) clarity.
- **Plausibility (G 2/21)** — applies only where the claim relies on a **technical effect** (e.g., "compound X for treating disease Y", "a composition having improved stability", a parameter-defined product whose patentability rests on the parameter's effect). Under G 2/21, post-published evidence may be relied upon if the technical effect is **encompassed by the technical teaching and embodied by the same originally disclosed invention**. The application as filed must make the technical effect plausible to the skilled person; bare assertion is not enough. Where the claim does not turn on a technical effect, do not invoke G 2/21.
- **Insufficient guidance / examples** — a single working example may suffice for a narrow claim but rarely for a broad one. The number and diversity of examples should match the breadth of the claim.
- **Parameter-based claims** — the parameter must be measurable by methods known to the skilled person and the application must enable the skilled person to obtain values within the claimed range across the scope (T 593/09; Guidelines F-III, 4). Pure unmeasurability is clarity (T 1845/14); inability to reach the claimed values is sufficiency.
- **Functional / "reach-through" claims** — claims defined by a function (e.g., "a compound that inhibits enzyme X") are sufficient only if the skilled person can identify, without undue burden, compounds satisfying the function. Pure invitation to research is insufficient (T 435/91).
- **Biological material / deposits** — Rule 31 EPC; if the invention requires biological material not available to the public and not described in a way that enables reproduction, a deposit is required.
- **Working examples vs prophetic examples** — prophetic examples are acceptable in principle but bear less evidentiary weight; if all examples are prophetic and the claim is broad, sufficiency may fail.

## Workflow

### Step 1: Identify the technical field, the skilled person, and the CGK

For each claim, briefly identify the technical field and the relevant skilled person (or team). Note the common general knowledge that the skilled person would bring to reading the application. This anchors the rest of the analysis.

Keep this brief — one or two sentences per claim or for the application as a whole, whichever is more economical.

### Step 2: Identify the claimed subject-matter and its scope

For each claim, identify:
- The claim category (product, process, use, apparatus).
- The breadth of the claim — narrow (single embodiment), medium (a defined class), or broad (functional, parameter-defined, or generic).
- The features that bear the inventive contribution (typically the characterising features or whatever distinguishes the claim from prior art the application identifies).

### Step 3: Identify any technical effect relied upon

If the claim or the description identifies a technical effect (e.g., "for treating X", "having improved Y", "wherein the device achieves Z"), flag it. **Only then** is G 2/21 plausibility engaged. Note:
- What is the alleged technical effect?
- Is it plausible from the application as filed (using the technical teaching and the originally disclosed invention as the reference point under G 2/21)?
- If the user has post-published evidence, note that this can supplement but not substitute for plausibility at filing.

If no technical effect is asserted, skip this step and do not invoke G 2/21.

### Step 4: Test enablement across the whole scope

For each claim, ask:
- Can the skilled person, using the application + CGK, carry out at least one embodiment within the claim? (If no — total insufficiency.)
- Can the skilled person carry out essentially all embodiments within the claim? (If no — whole-scope insufficiency.)
- Where in the description are the working examples / detailed embodiments / experimental data? Are they representative of the claimed scope?
- Does the claim contain functional or parameter-defined features that are not adequately exemplified?
- Is there guidance for selecting from broad classes (e.g., among many possible substituents, polymers, antibodies, parameter values)?

### Step 5: Categorise each issue

For each sufficiency issue identified, assign:

- **Type**: one of {Whole-scope insufficiency, Total insufficiency, Plausibility (G 2/21), Insufficient examples, Parameter unobtainable, Functional / reach-through, Missing guidance for selection, Undue burden / research programme, Biological material — deposit, Inventive contribution not reproducible, Other}
- **Severity**: one of {High, Medium, Low}
  - **High** — examiner or opponent will almost certainly succeed; the disclosure plainly does not enable the claim across its scope.
  - **Medium** — the objection is likely to be raised and reasonable people could disagree; depends on argument and evidence.
  - **Low** — borderline issue; flag for awareness, may not be raised or may be overcome with argument.
- **Suggested fix**: where a straightforward restriction to enabled subject-matter is supported by the description, propose the restriction with citation to the supporting passage (e.g., "Restrict to compounds of formula I where R1 = methyl, ethyl, or propyl; supported by [paragraph 0042] and Examples 1–3"). Where no such restriction is available, write "No straightforward fix — would require either evidence that the claim is enabled across its scope or substantive amendment beyond the original disclosure (Art. 123(2) caveat)."

### Step 6: Build the table

One table per claim, using this exact column structure:

| Issue # | Aspect of claim | Type | Severity | Suggested fix |

- **Issue #**: 1.1, 1.2 for issues in claim 1; 2.1 for claim 2; etc.
- **Aspect of claim**: the feature, range, function, or scope element that gives rise to the issue (verbatim quote where useful).
- **Type**: from the categories above.
- **Severity**: High / Medium / Low, with a one-sentence justification.
- **Suggested fix**: concrete restriction with description citation, or explicit "no straightforward fix" note.

If a claim has no sufficiency issues, write a single line under its heading: "No sufficiency issues identified."

### Step 7: Cross-check before finalizing

Before delivering, sanity-check:

- Did you accidentally raise clarity (Art. 84) issues? Move out of scope or hand off to the clarity skill.
- Did you accidentally raise support issues (claim broader than disclosure, but disclosure does enable specific examples)? That is support, not sufficiency — note the difference.
- Did you accidentally raise inventive-step issues (no technical contribution)? That is Art. 56, not Art. 83.
- For each "High" severity issue: have you identified concretely which embodiments cannot be carried out, or are you merely asserting "the claim is broad"? Breadth alone is not insufficiency — there must be a concrete enablement gap.
- Did you invoke G 2/21 where no technical effect is at issue? If so, remove.
- For suggested fixes: does the fix find genuine support in the description (Art. 123(2)), or are you constructing it? If constructed, mark accordingly.

## Output format

Strictly tabular per claim, English language, no preamble beyond the title. Structure:

```
# Sufficiency of Disclosure Assessment under Art. 83 EPC

**Claim(s) analyzed:** [list]
**Application:** [bibliographic reference, if known]
**Procedural context:** [examination / opposition / pre-filing review / other — note if relevant]
**Technical field and skilled person:** [one or two sentences]

## Claim 1 [label, e.g., "(independent, product)"]

[Optional: one short paragraph identifying the technical effect relied upon, if any, before the table]

| Issue # | Aspect of claim | Type | Severity | Suggested fix |
|---|---|---|---|---|
| 1.1 | "…" | … | High / Med / Low | … |
| 1.2 | "…" | … | … | … |

## Claim 2 …

[as above for each claim]
```

No executive summary at the top, no general remarks at the bottom. The patent attorney reads the tables; that's the deliverable.

## Tone and register

Write in formal, neutral patent-attorney English. Use EPC terminology precisely ("the skilled person", "common general knowledge", "without undue burden", "across the whole scope", "carry out the invention"). Cite Guidelines sections (F-III, 1) and case law decisions (T 409/91, T 435/91, G 2/21, T 1063/06) where they directly support an issue, but do not pad with citations.

Avoid hedging language ("arguably", "one might think") — the severity column carries the nuance. If an issue is genuinely borderline, mark it Low severity rather than softening the description.

## Edge cases to handle gracefully

- **Description in a foreign language**: work with it, cite passages by paragraph or page/line number, optionally provide brief English glosses. Do not refuse the task.
- **No working examples in the description**: this alone is not fatal — prophetic examples plus CGK can suffice for narrow claims. But for broad claims, lack of working examples typically tips toward insufficiency. Flag with appropriate severity.
- **Computer-implemented inventions**: sufficiency requires that the skilled person can implement the claimed functionality. A claim defined purely by what the software achieves, with no algorithmic detail, may be insufficient if implementation is non-trivial.
- **Range claims**: enablement must extend across the entire range. Examples at the extremes may be needed if the technical effect varies across the range.
- **Claims to "use of X for Y"**: sufficiency requires that the use can be performed, i.e., that X actually achieves Y as claimed.

## Self-check: am I doing sufficiency, or something else?

If you find yourself writing any of the following, stop — you have drifted out of Art. 83:

- "the claim is unclear because [term] has no defined meaning" — that's clarity (Art. 84), not sufficiency.
- "the claim is broader than what is disclosed" — that's support (Art. 84 third requirement), not sufficiency, unless the breadth means specific embodiments cannot be carried out.
- "the claimed effect is obvious in light of [prior art]" — that's inventive step (Art. 56), not sufficiency.
- "this feature was not in the original application" — that's added matter (Art. 123(2)), not sufficiency.
- "the invention has no industrial application" — that's Art. 57, not Art. 83.

Sufficiency is about whether the skilled person can *carry out* the invention across the whole scope of the claim, using the application + CGK, without undue burden. If you are reasoning about prior art, claim breadth in the abstract, or whether a feature is supported, you have drifted out of scope.
