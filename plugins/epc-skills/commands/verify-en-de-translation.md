---
name: verify-en-de-translation
description: Check the quality of an English-to-German translation by comparing the English source against the German target. Reports only defects in two categories — terminology consistency (English terms rendered by two or more different German equivalents) and completeness (English paragraphs missing on the German side).
argument-hint: "[optional: paths to English source and German target files, or pasted text — overrides project-directory discovery]"
allowed-tools: Read, Grep, Glob
---

# /verify-en-de-translation — Translation Quality Check (EN → DE)

You are running a single-purpose command. Your only task is to compare an English source text with its German translation and produce a defect-only report covering (i) terminology consistency and (ii) completeness.

You **must** use the `translation-check-en-de` skill for the substantive analysis. Do not re-derive the alignment method, term-map construction, inflection-collapsing rules, or output format from memory — load the skill and follow its workflow strictly.

## Step 1: Collect the inputs

The skill needs two text inputs:

- **English source** — required. The skill recognises filenames containing `english`, `EN`, `_en`, `-en`, or `source` (case-insensitive).
- **German target** — required. The skill recognises filenames containing `german`, `deutsch`, `DE`, `_de`, `-de`, or `target` (case-insensitive).

Sources, in order of preference:

1. `$ARGUMENTS` — paths to the two files, or pasted text for either side. Pass to the skill.
2. Project-directory discovery — the skill Globs the current directory by the conventions above.
3. Files attached to the conversation — the skill reads them.

### File formats

- Plain text (`.txt`, `.md`) — read directly.
- Word documents (`.docx`) — extract text first.
- PDFs (`.pdf`) — extract text first; PDF extraction may scramble paragraph boundaries, so the skill flags it under Notes if alignment becomes uncertain.
- HTML / XML — extract visible text, ignore tags.

### Prompt for missing inputs

If only one of the two files is available, stop and ask for the other. Do not try to guess what the missing side says.

If two files are provided without obvious naming, the skill identifies the English file by content. If the user pastes both texts inline, the skill uses them as supplied.

## Step 2: Load and apply the skill

Load the `translation-check-en-de` skill and follow its workflow strictly:

1. Align paragraphs — walk through the English source paragraph by paragraph and find the German counterpart using headings, numbered section markers, claim numbers, figure captions, and content cues. Flag English paragraphs with no German counterpart as missing.
2. Build the term map — identify content-bearing terms appearing more than once in the English source (noun phrases, domain-specific verbs, defined terms set in italics/bold/quotes, claim language). For each, collect every German rendering used at corresponding positions.
3. Filter the inconsistency findings — collapse inflection variants (singular/plural, declension) to the lemma; collapse compound-resolution variants (e.g., article presence); flag genuinely distinct German root words for the same English term as inconsistencies; never tolerate variation for defined terms.
4. Produce the report — findings only, in the skill's exact output format.

Output strictly as the skill prescribes:

- Two sections: `### Inconsistent Translations` and `### Incomplete Translation: Missing Paragraphs`.
- Each finding as a single bullet in the format the skill defines.
- **Omit any section that has no findings.**
- If both sections are empty, output exactly `No errors found.` and nothing else.
- An optional `### Notes` section may follow the findings if (a) the German contains content with no English counterpart, or (b) PDF extraction introduced artefacts. Keep it terse.

## Step 3: Stay in scope

This command is **single-purpose**. It performs an EN → DE terminology-consistency and completeness check and nothing else. If, while running or in follow-up, the user asks for any of the following, do NOT silently extend scope. Instead, deliver the defect report first, then state clearly that the request is outside this command's scope:

- **Producing a translation from scratch** — out of scope. This is a checking skill, not a translation skill.
- **Stylistic rewriting / editing** — out of scope. "This German sentence sounds clunky" is not a finding.
- **Grammar correction** — out of scope unless the grammar error is also a consistency or completeness problem.
- **Other language pairs** — out of scope. Only EN → DE. For French targets (DE → FR, EN → FR), reverse-direction checks (DE → EN), or other pairs, stop and tell the user this command is EN → DE only.
- **Back-translation comparison** — out of scope. The skill compares EN against DE directly; it does not translate the German back to English to compare.
- **Substantive legal/technical review** — out of scope. A wrong-but-consistent translation will not be flagged here. The skill checks internal consistency and completeness, not correctness of translation choices.
- **Number / unit mismatches** — out of strict scope, but if obviously spotted, the skill mentions them under Notes.

For each out-of-scope request, the response is a single sentence: *"That is outside the scope of /verify-en-de-translation. To do [X], please use [the appropriate other tool] or run a separate request."* Do not silently perform the out-of-scope task.

## Step 4: Error handling

- **Only one of the two files provided**: stop and ask for the other. Do not guess content.
- **German target is in a different language** (French, Spanish, Italian, …): stop and tell the user this command is EN → DE only.
- **English and German files have different logical structure** (chapters reordered, headings renumbered): the skill says so and asks whether the user wants the comparison anyway.
- **PDF extraction produced scrambled paragraph boundaries**: the skill proceeds against the reflowed text and flags the limitation under Notes.
- **No findings at all**: output exactly `No errors found.` per the skill's format.
- **Extra content on the German side with no English counterpart**: not in the standard output; the skill mentions it under Notes if it appears substantive.

## Notes for the assistant

- Do not narrate this command file to the user. Just do the work.
- Do not repeat the skill's content — load and use it.
- The skill is conservative: it flags every candidate inconsistency, even where a human reader might say "well, both renderings are obviously fine here". Decisions about whether a flagged inconsistency is acceptable belong to the reviewer, not to this command.
- Defined terms (capitalised throughout, in quotes, or introduced with an explicit definition) are treated more strictly than ordinary terms — every rendering must be identical.
- Claim language in patent texts is highly sensitive — treat every noun phrase in a claim as a defined term.
- If after delivery the user asks substantive follow-up questions about the findings themselves (e.g., "why did you flag 'control unit' but not 'sensor'?"), answer them — that is part of the same command's scope. Only refuse extensions to other tasks.
