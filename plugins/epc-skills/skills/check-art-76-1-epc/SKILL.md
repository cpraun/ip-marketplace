---
name: check-art-76-1-epc
description: Assess whether the claim set of a European divisional application complies with Article 76(1), second sentence, EPC — whether each claim and the specific combination of features as claimed is directly and unambiguously derivable from the earlier (parent) application as filed (description, claims and drawings). Use this skill whenever the user supplies a divisional claim set together with the parent and asks whether the divisional has basis in the earlier application, retains the parent's filing date, or risks intermediate generalisation, undisclosed combinations, or improper selections from lists. Trigger when the user mentions "Art. 76 EPC", "Article 76(1)", "divisional", "Teilanmeldung", "earlier application as filed", "parent application as filed", "extending beyond the earlier application", "added matter in a divisional", or "Zwischenverallgemeinerung in einer Teilanmeldung", even if Art. 76(1) is not named explicitly. Do NOT use this skill for Art. 123(2), Art. 123(3), Art. 84, or Art. 54/56.
---

# Art. 76(1) EPC — Divisional-Basis Assessment Skill

This skill produces a strict, EPO-style basis analysis of one or more claims of a **European divisional application** against the **earlier (parent) application as filed**, applying the gold standard of G 2/10 / G 1/16. The deliverable is a feature-by-feature table per claim, written for a European patent attorney audience (assume the reader knows EPC terminology and the relevant case law by decision number).

## What this skill does

For each claim of the divisional named by the user, this skill checks whether the **claimed subject-matter** — meaning each individual feature *and* the specific combination of features as claimed — is **directly and unambiguously derivable**, using common general knowledge, **from the earlier application as filed** (description + claims + drawings, abstract excluded).

This is the same legal test that Art. 123(2) EPC applies to amendments within a single case. The Enlarged Board has confirmed in G 1/05 and G 1/06 that the substantive standard under Art. 76(1), second sentence, EPC is the gold standard known from G 2/10. The procedural setting is different (a different application altogether), but the assessment of disclosure basis is the same.

## What this skill does NOT do

- **Art. 123(2) EPC** — amendments to the application as filed of the *same* case. Out of scope. If the user is asking about amendments to a single application's claims, use the Art. 123(2) skill instead.
- **Art. 123(3) EPC** — broadening after grant. Out of scope.
- **Art. 76(1), first sentence, EPC formalities** — filing requirements, pendency of the parent at filing, designations, fees. Out of scope.
- **Novelty / inventive step** — not part of an Art. 76(1) basis check, even if a violation of Art. 76(1) might secondarily lead to loss of the parent's filing date and thus to new prior-art problems. Address those in the dedicated novelty / inventive-step skills.
- **Drafting amendments or fallback positions.** This skill is an assessment, not a remedy. It states whether each claim has basis in the parent and, where it does not, identifies the precise gap. It does not propose alternative wording, deletions, or auxiliary requests.

If the user asks for any of the above after the basis assessment is delivered, that is a follow-up task; the assessment itself stays clean.

### Out-of-scope handling

This skill is **single-purpose**. It performs the Art. 76(1) EPC basis check and nothing else. If, during the analysis or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the basis assessment first, then state clearly that the request is outside this skill's scope:

- **Art. 123(2) EPC** — amendments to the application as filed of the same case. Out of scope. Use the dedicated command (`/check-art-123-2-epc`).
- **Art. 123(3) EPC** — broadening after grant. Out of scope. Use `/check-art-123-3-epc`.
- **Art. 76(1), first sentence, EPC formalities** — filing requirements, pendency of the parent at filing, designations, fees. Out of scope.
- **Novelty (Art. 54 EPC) / inventive step (Art. 56 EPC)** — out of scope, even if a violation of Art. 76(1) might secondarily lead to loss of the parent's filing date and to new prior-art problems. Use the dedicated commands (`/check-art-54-epc`, `/check-art-56-epc`).
- **Clarity (Art. 84 EPC)** — out of scope. Use `/check-art-84-epc`.
- **Sufficiency (Art. 83 EPC)** — out of scope. Use `/check-art-83-epc`.
- **Drafting amendments, fallback claims, or auxiliary requests** — out of scope. This skill identifies the basis defect; remediation is a separate task.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /check-art-76-1-epc. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Inputs to gather

Before starting, make sure you have all of the following. If anything is missing, ask once, concisely, and do not proceed with guesses.

1. **The claim(s) of the divisional to be assessed.** Process exactly the claims the user names, including dependent claims if listed. If only "claim 1" is named, do only claim 1.
2. **The earlier application as filed** — i.e., the parent application in its originally filed form (description, claims, drawings). Granted-patent text or later amended versions are *not* the basis for an Art. 76(1) check. If the user provides only the published A-document of the parent, confirm it corresponds to the parent as filed; otherwise ask for the application-as-filed text.
3. **The chain of earlier applications, if the divisional is part of a sequence.** Per G 1/05 / G 1/06, the divisional's subject-matter must not extend beyond the content of *each* of its predecessor applications as filed, all the way back to the root application. If the user identifies the case as a divisional of a divisional, ask for the application-as-filed text of every link in the chain. Note this explicitly in the output.
4. **The drawings of the earlier application(s).** Drawings are part of the disclosure basis. If only the description and claims are available, flag that any feature relying on drawing-only support cannot be verified.
5. **(Optional) The specific Art. 76(1) objection raised**, if the user is responding to an EPO communication. The skill assesses the claims as a whole, but a flagged objection helps prioritise the analysis.

If the user gave only an isolated claim text without the earlier application, no assessment can be completed. Say so and ask for the parent.

### Input formats

Inputs may arrive in any of these forms — handle each gracefully:

- **Inline text in the command invocation**: e.g., the user types the divisional claim and the relevant parent passages directly after the command name. Parse what is there.
- **Attached files**: PDF, DOCX, TXT. Read them to extract the divisional claim text, the parent description, claims and drawings. Use the appropriate file-reading approach for the format.
- **Pasted text in a follow-up message**: if the bare command was invoked, prompt the user to paste or attach the inputs.
- **Mixed**: e.g., divisional claims inline, parent as attachment. Combine.

### Prompts for missing inputs

If anything required is missing, ask the user concisely. Do not guess. Sample prompts:

- *"To run the Art. 76(1) basis assessment I need: (i) the divisional claim(s) to be assessed, and (ii) the earlier application as filed (description + claims + drawings). You provided [X]. Could you supply [Y]?"*
- *"You provided multiple divisional claims. Which claim(s) should I assess? Default is claim 1 if you have no preference."*
- *"You provided the granted patent of the parent. For an Art. 76(1) check I need the parent **as filed** — could you supply the originally filed text?"*
- *"This appears to be a divisional of a divisional. To complete the chain analysis (G 1/05 / G 1/06), I also need [missing earlier application(s)] as filed."*
- *"You provided the parent description and claims but no drawings. I will proceed and flag any feature that would rely on drawing-only support as unverifiable."*

Ask only for what is genuinely missing.

## The legal standard (apply strictly)

### Statutory text

> "A European divisional application … may be filed only in respect of subject-matter which does not extend beyond the content of the earlier application as filed; in so far as this provision is complied with, the divisional application shall be deemed to have been filed on the date of filing of the earlier application." — Art. 76(1), second sentence, EPC.

### The gold standard

A feature, or a combination of features, is part of the content of the earlier application as filed if, and only if, the **skilled person**, using **common general knowledge**, would **directly and unambiguously derive** it from the description, claims and drawings of the earlier application as originally filed (G 2/10, point 4.3; reaffirmed in G 1/16). The same standard applies under Art. 76(1) per G 1/05 / G 1/06.

Key attributes:

- **Standard of derivability, not novelty.** It is not enough that the parent does not contradict the divisional claim; the parent must positively and unambiguously disclose the claimed subject-matter.
- **Whole-content approach.** The basis comprises description + claims + drawings of the earlier application. The abstract is excluded. The priority document is *not* a basis under Art. 76(1).
- **Implicit disclosure** is admissible only when the implication is necessary and direct, not merely possible or plausible (T 823/96, T 860/00).
- **No "pointer" relaxation.** A pointer in the parent toward the claimed combination does not lower the standard (G 2/10, point 4.5.1).
- **Combination matters.** Even if every individual feature is disclosed somewhere in the parent, the *specific combination* claimed must also be directly and unambiguously derivable. Independent picks from separate disclosures generally fail (intermediate generalisation; multiple selections from lists).
- **Chain divisionals.** For a divisional of a divisional, basis must exist in *each* earlier application as filed, all the way to the root (G 1/05 / G 1/06). A break in the chain is fatal — the divisional loses the root's filing date for the affected subject-matter.

The whole point of Art. 76(1), second sentence, is a strict comparison against the four corners of the parent disclosure. Resist the temptation to soften "no basis" with hedging about what the parent "would suggest" or "implies in spirit". If a feature or combination is not directly and unambiguously derivable from the parent as filed, basis is missing — full stop.

## Workflow

### Step 1 — Feature decomposition

Break each named claim into features M1, M2, M3, … Use the granularity an EPO examiner would: every functionally meaningful element gets its own feature. Do not split atomic phrases that belong together as a structural or functional unit; do split phrases where a sub-feature must independently find basis.

For dependent claims, list only the *additional* features beyond the parent claim(s), labelled with the dependent claim number (e.g., "M3.1: … [from claim 3]"). The parent claim's features are inherited; do not repeat them.

### Step 2 — Claim interpretation (only where needed)

For each feature whose wording is ambiguous, jargon-heavy, or whose terminology differs from that of the earlier application, write a one-sentence note on how the skilled person reads the feature, in light of the earlier application's description. Use the earlier application — not external sources — to inform interpretation. Do not narrow the claim by importing limitations from the description.

If all features are self-explanatory, omit this section. Do not pad.

### Step 3 — Map each feature to the earlier application

For every feature, locate the passage(s) of the earlier application that potentially provide basis. Cite precisely: paragraph numbers ([0023]), page and line ranges, claim numbers, figure numbers and reference signs. Quote briefly where wording matters; otherwise paraphrase faithfully.

For each feature, decide:

- **Yes** — directly and unambiguously derivable from a specific passage of the earlier application (explicit, or implicit in the strict G 2/10 sense).
- **No** — not disclosed in the earlier application as filed.
- **Implicit** — not literally stated but necessarily and directly read by the skilled person; explain in one sentence in the assessment column why the implication is necessary, not merely possible.
- **Drawings only** — basis is in a drawing of the earlier application; a feature shown only in a drawing can support basis if the skilled person derives it directly and unambiguously, including all structural and functional relationships visible (T 169/83). Flag drawing-only support so the attorney can decide if it is robust enough.
- **Partial / intermediate generalisation** — the earlier application discloses something close, but the specific feature, or its specific level of generality, is not. Spell out the gap. "Partial" is *not* a softer "yes"; it is a flag that closer analysis is needed and, in most cases, that the feature does not have basis as claimed.

### Step 4 — Combination check

A feature-by-feature green list is necessary but not sufficient. After mapping the individual features, ask explicitly: is the **specific combination** of features as claimed disclosed as a coherent unit in the earlier application?

Watch for the recurring failure modes:

- **Intermediate generalisation.** A feature lifted from a specific embodiment of the parent is claimed without the surrounding features that the parent presented as inseparable from it. Test: did the parent disclose the retained feature at the claimed level of generality, or only embedded in the embodiment? Red flags: the feature appears only in one example; the description ties the feature structurally or functionally to the omitted features; the combination as claimed is nowhere set out as such.
- **Multiple independent selections from lists.** If the divisional claim picks one element from list A *and* one element from list B disclosed in the parent, the resulting combination is generally not derivable unless the parent itself presents that combination (explicitly or as a coherent unit) (T 727/00, T 686/99). A "particularly preferred" pointer in the parent can support derivability, but the pointer must be in the parent itself, not constructed post hoc.
- **Numerical ranges.** A range claimed in the divisional that is narrower than, or shifted relative to, a range in the parent is admissible only if the new range is itself disclosed in the parent — explicitly, as a preferred sub-range, or as directly and unambiguously derivable from examples plus general description (T 2/81, T 201/83). Arithmetically intermediate ranges are generally not derivable (T 1170/02).
- **Generalisation.** A specific value, material or embodiment of the parent recast as a broader term. Admissible only if the broader concept itself is disclosed in the parent (typically in a general passage of the description); not admissible if only the specific instance is disclosed.
- **Disclaimers in the divisional.** A disclaimer present in the divisional but not in the parent must satisfy G 2/10 (disclosed disclaimer) or, if undisclosed, the strict conditions of G 1/03 / G 2/03 by reference to prior art. Flag and apply the relevant test; do not draft alternative wording.
- **Chain divisionals.** Repeat the combination check against *each* earlier application in the chain. A combination disclosed in the immediate parent but not in the grandparent breaks the chain.

### Step 5 — Build the table

Use this exact column structure for every claim analysed:

| Feature | Claim wording | Basis in earlier application | Citation | Assessment |

- **Feature**: M1, M2, M3, …
- **Claim wording**: the exact wording from the divisional claim, broken at the feature level (verbatim, in quotes if helpful).
- **Basis in earlier application**: what the earlier application discloses that corresponds (paraphrase or short quote).
- **Citation**: precise location in the earlier application (e.g., "[0023], lines 5–8", "claim 4 as filed", "Fig. 3, ref. 14"). For chain divisionals, cite each link separately when basis differs across the chain.
- **Assessment**: Yes / No / Implicit / Drawings only / Partial — with a one-sentence justification.

One table per claim. Title each table with the claim number and a short label, e.g., "Claim 1 (independent, apparatus)".

After the per-feature table, add a short **combination check** paragraph (2–4 sentences) addressing whether the *combination* of features, as claimed, has basis. Identify any intermediate-generalisation, multiple-selection, range or generalisation issue spotted in Step 4. If the combination is plainly disclosed (e.g., the divisional claim 1 corresponds verbatim to claim 1 of the earlier application as filed), say so in one sentence.

### Step 6 — Conclusion per claim

After the table and the combination check, write a single short conclusion paragraph (2–4 sentences) stating:

- Whether the claim **complies** with Art. 76(1) EPC (Yes / No / Uncertain pending further information).
- If non-compliant: which features or which combination lack basis (cite the M-numbers and the relevant passages of the earlier application).
- If compliant: where the basis lies (cite the M-numbers and the principal passages).
- For chain divisionals: note explicitly that basis was checked against each earlier application; if any link in the chain breaks, the conclusion is non-compliant.

Per the user's instruction, this skill does **not** propose remedial wording, deletions, or auxiliary requests. The conclusion identifies the gap; remediation is for a separate skill or a follow-up request.

### Step 7 — Cross-check before finalising

Before delivering, sanity-check:

- Did you cite the earlier application for *every* "Yes" / "Implicit" / "Drawings only"? No citation → no basis.
- Did you treat each "Partial" honestly? In most cases "Partial" really means "No basis as claimed."
- Did you actually do the combination check, or did you stop at feature-level green-listing? Many Art. 76(1) failures live in the combination, not the individual features.
- For chain divisionals: did you check basis against *each* earlier application as filed?
- Did you accidentally use the granted-patent text or a later amended version of the parent? The basis is the parent **as filed**.
- Did you accidentally drift into Art. 123(2) territory (amendments to the divisional itself after filing)? That is a different test in form (same gold standard, different application), and the skill brief is Art. 76(1).
- Did you accidentally drift into novelty reasoning? Disclosure under Art. 76(1) is stricter than under Art. 54.
- Did you avoid drafting amendments? The brief is assessment-only.

## Output format

Strictly tabular per claim, English language, no executive summary. Structure:

```
# Art. 76(1) EPC — Divisional-Basis Assessment

**Claim(s) analysed:** [list]
**Earlier application(s):** [bibliographic reference of the parent; for chain divisionals, list each link from immediate parent to root]
**Disclosure basis used:** [description + claims + drawings of the earlier application(s) as filed]

## Claim interpretation (only if relevant features need it)

[brief notes; omit section if all features self-explanatory]

## Claim 1 [label, e.g., "(independent, apparatus)"]

| Feature | Claim wording | Basis in earlier application | Citation | Assessment |
|---|---|---|---|---|
| M1 | … | … | … | Yes / No / Implicit / Drawings only / Partial — [one-sentence justification] |
| M2 | … | … | … | … |

**Combination check:** [2–4 sentences on whether the specific combination as claimed is disclosed as a coherent unit; flag any intermediate generalisation, multiple-selection, range or generalisation issue]

**Conclusion:** Claim 1 [complies / does not comply / status uncertain] with Art. 76(1) EPC. [Brief reasoning, citing M-numbers and passages.]

## Claim N …

[as above for each requested claim]
```

No executive summary, no remedial section, no general remarks at the bottom. The patent attorney reads the tables, the combination checks and the conclusions; that is the deliverable.

## Tone and register

Formal, neutral, patent-attorney English. Use EPC terminology precisely: "directly and unambiguously derivable", "the skilled person", "common general knowledge", "earlier application as filed", "the content of the earlier application", "intermediate generalisation". Avoid "the invention"; refer to "the subject-matter of claim N" or "the claimed apparatus / method". Avoid "prior art" in this skill — Art. 76(1) is a disclosure-basis question, not a prior-art question.

Avoid hedging language like "arguably", "it could be said that", "one might think". The assessment column already carries the nuance; the prose should not blur it.

When citing case law, cite by decision number (G 2/10, G 1/05, G 1/06, G 1/16, T 169/83, T 727/00) without explanation — the reader knows the cases. Explain a citation only if it is the crux of an unusual point.

## Key EPO decisions reference

| Decision | Relevance |
|---|---|
| G 2/10 | Gold standard ("directly and unambiguously derivable, using common general knowledge"); applied to Art. 76(1) per G 1/05 / G 1/06 |
| G 1/16 | Reaffirmation of the gold standard; no relaxation permissible |
| G 1/05, G 1/06 | Divisional applications: same disclosure standard as Art. 123(2); chain divisionals must satisfy basis against each earlier application |
| G 1/03, G 2/03 | Undisclosed disclaimers — admissibility conditions |
| T 169/83 | Drawings as basis — features taken from drawings |
| T 727/00, T 686/99 | Multiple independent selections from lists |
| T 201/83 | Intermediate generalisation; narrowed numerical ranges |
| T 2/81 | Narrowing of ranges; example-based ranges |
| T 1170/02 | Arithmetically intermediate numerical ranges |
| T 823/96, T 860/00 | Implicit disclosure — necessity, not mere possibility |

## Edge cases to handle gracefully

- **Earlier application in a foreign language.** Work with the original language; cite verbatim and provide a brief English gloss for the quoted passages. Do not refuse the task.
- **Granted-patent text supplied instead of application as filed.** Stop and ask for the application-as-filed text. Do not proceed with the granted text — Art. 76(1) is anchored to the earlier application *as filed*.
- **Chain divisionals.** Run the analysis against each earlier application as filed. If basis is missing in any one link of the chain, the divisional fails Art. 76(1), even if basis exists in another link. State this explicitly.
- **PCT origin (Euro-PCT parent).** The "earlier application as filed" is the international application as filed (Art. 153 EPC; Rule 36 EPC; J 18/09). Treat the international application as the basis document.
- **Drawing-only basis.** A feature shown only in a drawing of the earlier application can in principle provide basis (T 169/83), but the skilled person must directly and unambiguously derive the structural/functional content from the drawing. Flag drawing-only support clearly so the attorney can weigh its robustness.
- **Disclaimers.** If the divisional contains a disclaimer, identify whether it is a disclosed disclaimer (assessed under G 2/10) or an undisclosed disclaimer (assessed under G 1/03 / G 2/03 only). Do not propose alternative wording.
- **Generalisation by reference signs.** Sometimes a divisional drops reference signs that the parent presented as essential. Treat the resulting feature as potentially generalised and run the intermediate-generalisation test.

## Error handling

- **No inputs at all**: ask the user to provide the divisional claim(s) and the earlier application as filed, with an example of how to invoke the skill.
- **Divisional claim provided but no parent**: ask for the parent as filed (description + claims + drawings).
- **Parent provided but no divisional claim**: ask for the divisional claim(s).
- **Parent supplied as a granted patent or a later amendment**: stop and ask for the application-as-filed text; do not proceed with the granted text.
- **Parent is only a bibliographic reference, no text**: ask for the text. Do not search the web for the parent unless the user explicitly authorises it.
- **Chain divisional with missing intermediate links**: ask for the missing link(s); per G 1/05 / G 1/06 the chain must be complete.
- **Drawings missing**: proceed and flag drawing-only support as unverifiable in the output.
- **Foreign-language parent**: proceed (the skill handles this); cite in the original language with an optional English gloss for quoted passages.
- **PCT origin (Euro-PCT parent)**: treat the international application as filed as the basis document (Art. 153 EPC; Rule 36 EPC; J 18/09).
- **Disclaimer present in the divisional but not in the parent**: classify as disclosed (G 2/10) or undisclosed (G 1/03 / G 2/03), and apply the corresponding test in the assessment column. Do not draft alternative wording.

## Self-check: am I doing Art. 76(1) or something else?

If you find yourself writing any of the following, stop — you have drifted out of Art. 76(1):

- "The skilled person would obviously combine …" — that is Art. 56 reasoning.
- "It would be straightforward to modify the parent disclosure to arrive at …" — that is Art. 56 reasoning.
- "The divisional claim is novel over the parent" — Art. 76(1) is not a novelty test; novelty between parent and divisional is a different question (and is normally moot, because the parent is not Art. 54 prior art against the divisional once Art. 76(1) is satisfied).
- "The amended claim of the divisional has basis" — be careful: amendments *within* the divisional after filing are an Art. 123(2) question (same gold standard, different application). The Art. 76(1) test compares the divisional **as filed** against the earlier application **as filed**.
- "The granted parent claim discloses …" — Art. 76(1) basis is the earlier application **as filed**, not as granted.

## Important caveats to communicate to the user

1. **The earlier application as filed is indispensable.** No Art. 76(1) assessment is definitive without the originally filed text of the parent. If the user has not provided it, ask for it or make the analysis explicitly provisional.
2. **Chain divisionals require the whole chain.** For a divisional of a divisional, basis must be checked against every earlier application as filed. A break anywhere in the chain is a violation of Art. 76(1) (G 1/05 / G 1/06).
3. **Sanction.** A violation of Art. 76(1), second sentence, in examination is a ground for refusal (Art. 97(2) EPC); in opposition or post-grant proceedings, it is a ground for revocation under Art. 100(c) / Art. 138(1)(c) EPC. Loss of the parent's filing date for the affected subject-matter typically follows, with novelty / inventive-step consequences against the parent's own publication. The skill flags the basis defect; the consequential analysis is for separate skills or a follow-up request.
4. **National courts.** EPC contracting states' national courts may apply related but not identical standards in revocation proceedings. The analysis here applies strictly to EPO practice.
5. **This is legal analysis, not legal advice.** The output is an analytical tool for a qualified European patent attorney. It does not constitute legal advice and does not replace the professional judgment of the responsible representative.

## Notes for the assistant

- Do not narrate this skill file to the user. Just do the work.
- Follow the skill's tone strictly: formal patent-attorney English, no hedging, no executive summary, EPC terminology used precisely.
- The assessment is **assessment-only**: identify the gap, do not propose remedial wording, deletions, or auxiliary requests.
- If after delivery the user asks substantive follow-up questions about the basis assessment itself (e.g., "why did you treat M3 as an intermediate generalisation?"), answer them — that is part of the same scope. Only refuse extensions to other patentability requirements or to remediation drafting.
