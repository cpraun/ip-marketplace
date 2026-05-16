---
name: verify-en-de-translation
description: Verify the quality of an English-to-German translation by comparing the English source text against the German target text and reporting only the errors found. The skill performs two strict checks (1) terminology consistency — flag every English term that is rendered by two or more different German equivalents within the same translation; (2) completeness — flag every English paragraph that has no corresponding German paragraph. The output is a defect-only report; Do NOT use this skill for stylistic rewriting of a translation; producing a new translation from scratch (this is a checking skill, not a translation skill); language pairs other than English→German (use a different skill or general translation tooling for EN-FR, DE-EN reverse direction, etc.); detecting subtle nuance, register, or tone errors that go beyond term-level consistency; or substantive legal/technical review of the translated content.
---

# English → German Translation Check

This skill compares an English source text with its German translation and produces a **defect-only report**. It does two things, and only two things:

1. **Terminology consistency** — every English term should be rendered by exactly one German equivalent throughout the translation. If the same English term is translated by two or more different German terms within the same target document, that is a finding.
2. **Completeness** — every English paragraph should have a corresponding German paragraph. If a paragraph is missing on the German side, that is a finding.

Anything else — stylistic preferences, "this could be phrased more elegantly", grammar nitpicks, register, tone, idiom — is **out of scope**. The skill is a QA tool; it reports errors, not opinions.

## Why this matters

In serious translation work — patent specifications, contracts, validated EP patents in Germany, technical standards, regulatory filings — terminology drift is a real liability. If a claim term in an English patent is translated as *"Schraube"* in some places and *"Befestigungselement"* in others, the scope of the German validation can become unclear, and an opponent or court may exploit the inconsistency. Likewise, missing paragraphs in a translated specification can create Art. 70(2) EPC issues for German national validations.

The skill is therefore conservative: it flags every candidate inconsistency, even where a human reader might say "well, both renderings are obviously fine here". Decisions about whether a flagged inconsistency is acceptable belong to the reviewer, not to the QA tool.

## What this skill does NOT do

- **Produce a translation** — if the user gives you only English and asks for German, that is a translation task, not a check task.
- **Stylistic editing** — "this German sentence sounds clunky" is not a finding.
- **Grammar correction** — out of scope unless the grammar error happens to also be a consistency or completeness problem.
- **Other language pairs** — only EN→DE. If the user provides a French target, redirect or stop and ask.
- **Back-translation comparison** — the skill compares EN against DE directly; it does not translate the German back into English to compare.
- **Substantive review** — the skill does not check whether the translation is *correct*, only whether it is *internally consistent and complete*. A wrong-but-consistent translation will not be flagged here.

If the user wants any of the above, say so briefly and either redirect or, if the user confirms they want a different task, drop this skill and switch to the right one.

### Out-of-scope handling

This skill is **single-purpose**. It performs an EN → DE terminology-consistency and completeness check and nothing else. If, while running or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the defect report first, then state clearly that the request is outside this skill's scope:

- **Producing a translation from scratch** — out of scope. This is a checking skill, not a translation skill.
- **Stylistic rewriting / editing** — out of scope. "This German sentence sounds clunky" is not a finding.
- **Grammar correction** — out of scope unless the grammar error is also a consistency or completeness problem.
- **Other language pairs** — out of scope. Only EN → DE. For French targets (DE → FR, EN → FR), reverse-direction checks (DE → EN), or other pairs, stop and tell the user this command is EN → DE only.
- **Back-translation comparison** — out of scope. The skill compares EN against DE directly; it does not translate the German back to English to compare.
- **Substantive legal/technical review** — out of scope. A wrong-but-consistent translation will not be flagged here. The skill checks internal consistency and completeness, not correctness of translation choices.
- **Number / unit mismatches** — out of strict scope, but if obviously spotted, the skill mentions them under Notes.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /verify-en-de-translation. To do [X], please use [the appropriate other tool] or run a separate request."* Do not silently perform the out-of-scope task.

## Inputs to gather

The skill needs two text inputs:

- **English source** — the file or text containing the original English. Filename conventions to look for, case-insensitive: contains `english`, `EN`, `_en`, `-en`, or `source`.
- **German target** — the file or text containing the German translation. Filename conventions to look for: contains `german`, `deutsch`, `DE`, `_de`, `-de`, or `target`.

Sources, in order of preference:

1. Explicit paths or pasted text supplied by the user — used directly.
2. Project-directory discovery — Glob the current directory by the conventions above.
3. Files attached to the conversation — read them directly.

If files are attached or a folder is mounted, search for these conventions. If two files are provided without obvious naming, look at the content: the English file is the one in English. If the user pastes both texts inline, use them as supplied.

If only one of the two is available, stop and ask for the other. Do not try to guess what the missing side says.

If a non-German target is supplied (French, Spanish, Italian, …), tell the user this skill is EN→DE only and stop.

### File formats

- Plain text (`.txt`, `.md`) — read directly.
- Word documents (`.docx`) — extract the text first; the `docx` skill or a simple Python extraction is fine.
- PDFs (`.pdf`) — extract text first; the `pdf` skill can help. Be aware that PDF text extraction sometimes scrambles paragraph boundaries — flag this if it becomes a problem and ask whether the user can supply a text version.
- HTML / XML — extract the visible text, ignoring tags.

The pair must be in the *same* logical structure (same section ordering, same headings). If the German file is reorganised (e.g., chapters in a different order), say so and ask whether the user wants the comparison anyway.

### Prompts for missing inputs

If only one of the two files is available, stop and ask for the other. Do not try to guess what the missing side says.

If two files are provided without obvious naming, the skill identifies the English file by content. If the user pastes both texts inline, the skill uses them as supplied.

## Method

### Step 1 — Align paragraphs

Walk through the English source paragraph by paragraph. For each English paragraph, find its German counterpart. Use heading text, numbered section markers, claim numbers, figure captions, and content cues to establish the alignment.

If a paragraph has no German counterpart, flag it as a *missing paragraph*. Do not flag short connective elements (a stand-alone heading that has been merged into the next paragraph in the German is normal layout variation, not an error) — the user cares about substantive missing content.

If the German has paragraphs that the English does not (i.e., extra content on the German side), that is *not* in the standard output, but mention it in a brief note at the end of the report under "Notes" — the user may want to know.

### Step 2 — Build the term map

Identify candidate **terms** in the English source. A term, for the purposes of this check, is a content-bearing word or fixed multi-word expression that appears more than once. Function words ("the", "of", "and", "is"), generic verbs ("to be", "to have"), and one-off proper names are not terms in this sense.

Useful term candidates in technical / legal text:

- noun phrases that name parts, components, or concepts (e.g., *"control unit"*, *"phase-change material"*, *"closing date"*)
- domain-specific verbs (*"to mount"*, *"to hereby agree"*)
- defined terms — terms set in *Italics*, **Bold**, "double quotes", or introduced with phrases like "(hereinafter the **X**)" — these are explicit terminology decisions and **must** be translated consistently
- claim language in patent texts — every word in the claims is a candidate term, and consistency between description and claims matters

For each English term that appears more than once, collect every German rendering used at the corresponding position. If two or more distinct German renderings appear for the same English term, that is an inconsistency.

### Step 3 — Filter the inconsistency findings

Not every variation is a defect. Apply these filters before reporting:

- **Inflection is not inconsistency.** *"Schraube"* and *"Schrauben"* (singular vs plural), or *"Schraube"* / *"der Schraube"* / *"die Schraube"* (declension), are the same term. Reduce to lemma / base form before comparing.
- **Compound resolution is not inconsistency.** When the English "the cooling element" is rendered once as *"Kühlelement"* and once as *"das Kühlelement"*, that is the same term.
- **Genuinely synonymous renderings of a non-defined word may still be inconsistencies in technical / legal text.** Be conservative: if two distinct German *root* words are used for the same English term, flag it. The reviewer will decide if it matters.
- **Defined terms** — *never* tolerate variation. If the English source defines a term (capitalised throughout, in quotes, or by an explicit definition), every rendering must be identical.

### Step 4 — Produce the report

Write the report exactly in the output format below. Findings only. Skip any section that has no findings. If both sections are empty, output the single line `No errors found.`

## Output format

Use this structure exactly. Do not add a preamble, an executive summary, or closing remarks. The reviewer reads the findings and acts on them.

```
### Inconsistent Translations

* **English Term:** `[term]` → **German Translations:** `[rendering A]`, `[rendering B]`
* **English Term:** `[term]` → **German Translations:** `[rendering C]`, `[rendering D]`, `[rendering E]`

### Incomplete Translation: Missing Paragraphs

* **Missing Paragraph 1:** `[full text of the first missing English paragraph]`
* **Missing Paragraph 2:** `[full text of the second missing English paragraph]`
```

If a section has no findings, **omit it entirely** (do not include the heading with "none below").

If both sections have no findings, output exactly:

```
No errors found.
```

### Optional appended note

If you noticed the German contains content that has no English counterpart, or if PDF extraction may have introduced artefacts, append a brief note **after** the findings:

```
### Notes

[brief observation, e.g., "The German file contains an additional paragraph at the end (about the contact address) that has no English counterpart." or "PDF extraction introduced line breaks within several English paragraphs; the alignment was performed against the reflowed text."]
```

Keep notes terse. The deliverable is the findings.

## Examples

**Example 1 — inconsistent term:**

English source contains: *"The control unit receives the signal."* and *"The control unit then activates the actuator."*

German target contains: *"Die Steuereinheit empfängt das Signal."* and *"Das Steuergerät aktiviert daraufhin den Aktuator."*

Finding:

```
* **English Term:** `control unit` → **German Translations:** `Steuereinheit`, `Steuergerät`
```

**Example 2 — missing paragraph:**

English source paragraph: *"In a preferred embodiment, the housing is made of die-cast aluminium."* — no corresponding paragraph in the German target.

Finding:

```
* **Missing Paragraph 1:** `In a preferred embodiment, the housing is made of die-cast aluminium.`
```

**Example 3 — no findings:**

```
No errors found.
```

## Edge cases

- **Patent claims** — claim language is highly sensitive. Treat every noun phrase in a claim as a defined term. A claim feature mentioned twice in the German claim that uses two different German words *for the same English source word* is always a finding, even when both German words are correct in isolation.
- **Acronyms / proper names** — identical strings on both sides count as identical (e.g., "EPC" / "EPC" is consistent).
- **Numbers and units** — a mismatch in a number or unit is *not* in the consistency-or-completeness scope of this skill, but if you spot one obviously, mention it under Notes.
- **Reordered sentences within a paragraph** — not a finding so long as the paragraph is present.
- **Boilerplate cut on the German side** — sometimes the translator legitimately omits standard boilerplate that the German reader has elsewhere. Still flag it as a missing paragraph; the reviewer decides.
- **Bilingual sections / quotes** — if the English source contains a German quote that is left in German also in the target, that is normal and not a finding.

## Error handling

- **Only one of the two files provided**: stop and ask for the other. Do not guess content.
- **German target is in a different language** (French, Spanish, Italian, …): stop and tell the user this command is EN → DE only.
- **English and German files have different logical structure** (chapters reordered, headings renumbered): the skill says so and asks whether the user wants the comparison anyway.
- **PDF extraction produced scrambled paragraph boundaries**: the skill proceeds against the reflowed text and flags the limitation under Notes.
- **No findings at all**: output exactly `No errors found.` per the skill's format.
- **Extra content on the German side with no English counterpart**: not in the standard output; the skill mentions it under Notes if it appears substantive.

## Self-check before delivering

Before handing the report to the reviewer:

- Are all findings reproducible — would another reviewer locate the same passages from the citations given?
- Are inflection variants (singular/plural, case endings, articles) collapsed to the lemma so they do not produce false positives?
- Are defined terms identified and treated more strictly than ordinary terms?
- Are sections with no findings actually omitted from the output?
- If everything is clean, does the report consist exactly of `No errors found.` and nothing else?

If any of these is "no", revise before delivering.

## Notes for the assistant

- Do not narrate this skill file to the user. Just do the work.
- The skill is conservative: it flags every candidate inconsistency, even where a human reader might say "well, both renderings are obviously fine here". Decisions about whether a flagged inconsistency is acceptable belong to the reviewer, not to this skill.
- Defined terms (capitalised throughout, in quotes, or introduced with an explicit definition) are treated more strictly than ordinary terms — every rendering must be identical.
- Claim language in patent texts is highly sensitive — treat every noun phrase in a claim as a defined term.
- If after delivery the user asks substantive follow-up questions about the findings themselves (e.g., "why did you flag 'control unit' but not 'sensor'?"), answer them — that is part of the same scope. Only refuse extensions to other tasks.
