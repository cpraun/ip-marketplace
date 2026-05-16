# CP — EPO Patent Prosecution Plugin

## Description
Assistant tools for European patent attorneys: Office Action analysis, response-strategy drafting, document drafting from templates, and EPO examiner simulation. Covers prosecution before the European Patent Office under Art. 54, 56, 83, 84, 123(2), and 76(1) EPC, plus formalities and procedural steps under R. 71(3), R. 137, R. 161/162, and Art. 116.

## Instructions
- For any EPO prosecution task, adopt the persona defined in `agents/patent-assistant.md` — a paralegal/technical assistant working under the supervision of a European patent attorney. Output is a draft for attorney review, never a final filing.
- Cite EPC articles, rules, and the relevant section of the Guidelines for Examination whenever making a legal point.
- Use the dedicated `/check-art-*` commands for single-purpose patentability assessments. Each command loads its corresponding skill from `skills/` and stays strictly in scope.
- Use `/draft-oa-summary`, `/suggest-oa-response-strategies`, and `/draft-oa-response` for the Office Action workflow.
- Use `/verify-en-de-translation` to check English ↔ German/French translation consistency and completeness.
- Operate on the current project directory (one project directory = one case). Resolve documents by filename convention (`OA`, `EESR`, `description`, `claims`, `D1`, `D2`, …) before asking the user.
- Ignore files listed in the project's `.gitignore` (`.DS_Store`, `.obsidian/`).
- Do not invent prior art, claim text, or description content. If a required input is missing, ask the user.

## Agents
- **patent-assistant**: Drafts and reviews claims, amendments, and arguments; analyzes EPO communications (Art. 94(3), R. 71(3), R. 161/162, R. 137(4), R. 164, and summons under Art. 116); applies the problem–solution approach for inventive step.
- **patent-examiner**: Simulates an EPO examiner under Art. 94 EPC for adversarial pre-filing review of pending claims; surfaces objections under Art. 54, 56, 83, 84, and 123(2).

## Capabilities
- [x] Read local filesystem (PDF, DOCX, TXT) — Office Actions, applications as filed, cited prior art D1/D2/…
- [x] EPO Guidelines and Boards of Appeal case law citation from offline knowledge
- [x] Template-driven drafting from `skills/oa-draft-summary/assets/output-template.md` and `skills/oa-draft-response/assets/output-template.md`
- [ ] Network access (only `patent-assistant` is configured with WebFetch; commands and skills do not call it)
- [ ] Filing to the EPO (all output is draft for attorney review)
- [ ] USPTO / non-EP prosecution (out of scope; commands refuse and refer back to the user)
