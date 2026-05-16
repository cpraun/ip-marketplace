---
name: check-art-54-epc
description: Assess novelty of one or more patent claims against a single prior art document under Article 54 EPC. Use this skill whenever the user wants to check whether a claim is novel over a specific piece of prior art (D1) — for example, when reviewing a search report citation, preparing a response to an EPO examination report under Art. 54, evaluating an opposition ground, doing freedom-to-operate analysis against a single reference, or screening a competitor's patent. Trigger this skill whenever the user mentions "novelty", "Art. 54 EPC", "anticipation", "directly and unambiguously disclosed", "Neuheit", or asks to compare claim features against a single document, even if they don't explicitly say "novelty assessment".
---

# Novelty Assessment under Article 54 EPC

This skill produces a strict, EPO-style novelty analysis comparing one or more patent claims against a single prior art document (D1). The output is a feature-by-feature table in English, written for a patent attorney audience (assume the reader knows EPC terminology).

## What this skill does NOT do

- **Inventive step (Art. 56 EPC)** — out of scope. Novelty assessment under Art. 54 is a strictly separate, formal exercise. Do not slip into problem-solution reasoning, do not consider "obvious modifications", do not combine D1 with general knowledge.
- **Multi-document comparisons** — only one prior art document at a time. If the user provides several documents, ask which one to use as D1, or run the analysis once per document.
- **Drafting amendments** — the skill produces an assessment, not amended claim language.

If the user asks for any of the above after the novelty assessment is done, that's fine to do as a follow-up — but the assessment itself stays clean.

### Out-of-scope handling

This skill is **single-purpose**. It performs novelty assessment under Art. 54 EPC and nothing else. If, while running the analysis or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the novelty assessment first, then state clearly that the request is outside this skill's scope:

- Inventive step (Art. 56) — out of scope.
- Multi-document novelty / comparison against multiple D1 — out of scope. If the user provides several documents, ask which one to use as D1, or run the skill separately for each.
- Clarity (Art. 84) — out of scope.
- Sufficiency (Art. 83) — out of scope.
- Added matter (Art. 123(2)) — out of scope.
- Drafting amendments — out of scope.
- Freedom-to-operate analysis — out of scope (FTO has different rules; this skill does the EPC novelty test, not infringement).

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /check-art-54-epc. To do [X], please use [the appropriate other command/skill] or run a separate request."* Do not silently perform the out-of-scope task.

## Inputs to gather

Before starting the analysis, make sure you have:

1. **The claim(s) to be assessed.** The user told us which claims to analyze — they specify per request. Process exactly the claims named, including dependent claims if listed. If only "claim 1" is named, do only claim 1.
2. **The prior art document D1.** Could be a published patent application, granted patent, journal article, datasheet, etc. The full text or relevant passages must be available — if the user only gave you a citation (e.g., "EP 1 234 567 A1"), ask them to provide the document or relevant passages. Do not invent the contents of D1.
3. **The application/patent text** that the claim belongs to, if available. Useful for claim interpretation (description and figures inform what the skilled person reads into the claim wording — see G 2/88, point 4 of the reasons).

If anything is missing, ask once, concisely. Don't proceed with guesses about what D1 says.

### Input formats

Inputs may arrive in any of these forms — handle each gracefully:

- **Inline text in the command invocation**: e.g., the user types the claim and D1 directly after the command name. Parse what's there.
- **Attached files**: PDF, DOCX, TXT. Read them to extract claim and D1 text. Use the appropriate file-reading approach for the format.
- **Pasted text in a follow-up message**: if the bare command was invoked, prompt the user to paste or attach the inputs.
- **Mixed**: e.g., claim inline, D1 as attachment. Combine.

### Prompts for missing inputs

If anything required is missing, ask the user concisely. Do not guess. Sample prompts:

- *"To run the novelty assessment I need: (i) the claim(s) to be assessed, and (ii) the text or relevant passages of D1. You provided [X]. Could you supply [Y]?"*
- *"You provided multiple claims. Which claim(s) should I assess? Default is claim 1 if you have no preference."*
- *"You gave me the bibliographic reference for D1 but not its contents. Please paste the relevant passages or attach the document."*

Ask only for what is genuinely missing. If you have everything, proceed silently.

## The legal standard (apply strictly)

The skill applies the established EPO standard for novelty:

- **Direct and unambiguous disclosure** — a feature is anticipated by D1 only if it is directly and unambiguously derivable from D1, including any features that are implicit to the skilled person reading D1 (G 2/88, G 1/03; *Case Law of the Boards of Appeal*, I.C.4.).
- **No mosaicking** — features from different embodiments within D1 may only be combined if D1 itself directly and unambiguously points to that combination. Do not freely combine separate examples or embodiments.
- **Implicit disclosure** is narrow. Two formulations are used in the case law and both express essentially the same threshold: (i) what the skilled person would *necessarily* read as part of the disclosure, and (ii) what the skilled person would understand to be disclosed *without doubt* when reading D1 with common general knowledge. Mere obviousness or mere possibility is not enough.
- **Claim interpretation**: read claim features as the skilled person would, in light of the description, but without importing limitations from the description into the claim. Functional features are anticipated if D1 discloses something that inherently performs the function.
- **Selection inventions**: for sub-ranges, apply the current EPO test (T 261/15 and subsequent case law): novelty of a sub-range over a broader known range requires that the sub-range is (i) narrow and (ii) sufficiently far removed from any specific examples disclosed and from the end-points of the known range. The former third criterion of "purposive selection" is no longer treated as a novelty requirement and belongs to inventive-step analysis. Flag if a sub-range or selection issue is present.
- **Disclaimers and "the same invention"** considerations from G 1/03 / G 2/03 are out of scope unless directly relevant; mention briefly only if the claim contains a disclaimer that interacts with D1.

The whole point of Art. 54 is that it is a black-and-white test on the four corners of D1. Resist the temptation to soften "no" into "probably no" with hedging about what D1 might suggest. If D1 doesn't disclose feature M3 directly and unambiguously, then M3 is not anticipated — full stop.

## Workflow

### Step 1: Feature decomposition

Break each claim into features M1, M2, M3, … Use the granularity that EPO examiners use: each functionally meaningful element gets its own feature. Don't split atomic phrases that belong together ("a hydraulic cylinder having a piston rod" is one feature if the rod is just a structural part of the cylinder; it's two features if the rod's properties matter independently for novelty).

For dependent claims, list only the *additional* features beyond the parent claim(s), and label them with the claim number (e.g., "M2.1: ... [from claim 2]"). The parent features are inherited; don't repeat them in the table.

### Step 2: Claim interpretation (brief, only where needed)

For each feature where the wording is ambiguous, technical-jargon-heavy, or where D1 uses different terminology, write a one-sentence note on how the skilled person reads the feature. Use the description and figures of the application to inform this — but do not narrow the claim by reading limitations from the description into the claim.

If all features are self-explanatory, skip the interpretation section. Don't pad.

### Step 3: Map each feature to D1

For every feature, find the relevant passages in D1. Cite precisely — page and line numbers, paragraph numbers ([0023]), claim numbers, figure numbers. Quote the actual D1 wording briefly where it matters; otherwise paraphrase.

For each feature, decide:

- **Yes** — directly and unambiguously disclosed (explicit, or implicit in the strict sense)
- **No** — not disclosed
- **Implicit** — not literally stated but necessarily read by the skilled person; explain why in the assessment column
- **Partial** — D1 discloses something close but not the full feature; explain the gap

"Partial" is a flag for the writer's analysis — don't use it to fudge a "no" into something softer for diplomatic reasons. A partial match for novelty purposes is a "no".

### Step 4: Build the table

Use this exact column structure for every claim analyzed:

| Feature | Claim wording | D1 disclosure | Citation | Assessment |

- **Feature**: M1, M2, M3, …
- **Claim wording**: the exact wording from the claim, broken at the feature level (verbatim, in quotes if helpful)
- **D1 disclosure**: what D1 says that corresponds (paraphrase or short quote)
- **Citation**: precise location in D1 (e.g., "[0023], lines 5–8" or "Fig. 3, ref. 14")
- **Assessment**: Yes / No / Implicit / Partial — with a one-sentence justification

One table per claim. Title each table with the claim number and a short label, e.g., "Claim 1 (independent, apparatus)".

### Step 5: Conclusion per claim

After each table, write a single short conclusion paragraph stating:

- Whether the claim is novel over D1 (Yes / No)
- If not novel: which features anticipate the claim (cite the M-numbers and the D1 passages)
- If novel: which feature(s) distinguish the claim (cite the M-numbers)

Keep this to 2–4 sentences. The reasoning is in the table; the conclusion just states the result.

### Step 6: Cross-check before finalizing

Before delivering, sanity-check:

- Are there any features marked "Partial" that should really be "No"? (Almost always yes.)
- Did you cite D1 for every "Yes" / "Implicit"? (No citation → not directly and unambiguously disclosed.)
- Did you accidentally combine separate embodiments of D1?
- Did you accidentally reason about "obvious modifications"? (That's Art. 56, not Art. 54.)
- For dependent claims: if the parent is novel, the dependent is automatically novel — say so briefly rather than rebuilding the full table for the dependent claim's inherited features.

## Output format

Strictly tabular per claim, English language, no preamble beyond the title. Structure:

```
# Novelty Assessment under Art. 54 EPC

**Claim(s) analyzed:** [list]
**Prior art document:** [bibliographic reference of D1]

## Claim interpretation (only if relevant features need it)

[brief notes; omit section if all features self-explanatory]

## Claim 1 [label, e.g., "(independent, apparatus)"]

| Feature | Claim wording | D1 disclosure | Citation | Assessment |
|---|---|---|---|---|
| M1 | … | … | … | Yes / No / Implicit / Partial — [justification] |
| M2 | … | … | … | … |

**Conclusion:** Claim 1 is [novel / not novel] over D1. [Brief reasoning.]

## Claim N …

[as above for each requested claim]
```

No executive summary at the top, no general remarks at the bottom. The patent attorney reads the tables and conclusions; that's the deliverable.

## Tone and register

Write in formal, neutral patent-attorney English. Use EPC terminology precisely ("directly and unambiguously disclosed", "the skilled person", "anticipates", "novel over"). Avoid hedging language like "arguably", "it could be said that", "one might think" — Art. 54 is a yes/no test and the assessment column already carries the nuance.

When citing case law, cite by decision number (e.g., G 2/88, T 261/15) without lengthy explanation — the reader knows the cases. Only explain a citation if it's genuinely the crux of an unusual point.

## Edge cases to handle gracefully

- **D1 in a foreign language**: if the user provides D1 in German/French/Japanese, work with it — cite in the original language, optionally provide a brief English gloss. Don't refuse the task.
- **D1 is a patent family member of the application**: this can affect prior-art status (Art. 54(2) vs (3) EPC). Briefly flag the prior-art status if the dates suggest it matters, but do not turn the task into a full prior-art-status analysis.
- **Numerical ranges / sub-ranges**: apply the current sub-range test (see "Selection inventions" above, T 261/15). Note the test in the assessment column.
- **Process vs product claims**: a process claim is not anticipated by a product disclosure that doesn't disclose the process, and vice versa, unless the process is implicit. Be careful here.

## Error handling

- **No inputs at all**: ask the user to provide the claim and D1, with an example of how to invoke the skill.
- **Claim provided but no D1**: ask for D1.
- **D1 provided but no claim**: ask for the claim(s).
- **D1 is only a bibliographic reference, no text**: ask for the text or relevant passages. Do not search the web for D1 unless the user explicitly authorises it.
- **Multiple D1 candidates**: ask which one to use; do not run multiple analyses without confirmation.
- **Foreign language D1**: proceed (the skill handles this); cite in original language with optional English gloss.
- **Application text not provided**: proceed without it; flag in the report only if claim interpretation became uncertain as a result.

## Self-check: am I doing novelty or inventive step?

If you find yourself writing any of the following, stop — you've drifted into Art. 56:

- "the skilled person would obviously combine …"
- "it would be straightforward to modify D1 to …"
- "given the teaching of D1, the skilled person would arrive at …"
- "D1 does not disclose feature M3, but this is a minor design choice"

Novelty is binary disclosure analysis on the four corners of D1. Modifications, however small, are not part of the test.

## Notes for the assistant

- Do not narrate this skill file to the user. Just do the work.
- Follow the skill's tone strictly: formal patent-attorney English, no hedging, no executive summary.
- If after delivery the user asks substantive follow-up questions about the novelty assessment itself (e.g., "why did you treat M3 as implicit?"), answer them — that is part of the same scope. Only refuse extensions to other patentability requirements.
