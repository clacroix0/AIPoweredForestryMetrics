# Public Prompt Design Library

> **Public-safe design artifact.** These templates are adapted from the active prompt architecture in AI-Powered Forestry Metrics. They preserve the design pattern and metric logic while omitting internal identifiers, environment configuration, production connections, and deployment-only details. They are not a drop-in replacement for approved production prompts.

## What the original solution contains

The source solution contains a broader prompt/model set, including earlier and full-document variants. The active cloud-flow pattern uses **ten custom-prompt calls** arranged as five pairs:

| Document path | Metric | First pass | Second pass |
|---|---|---|---|
| Forest Management Plan (FMP) | Plan period | Find evidence in each text chunk | Consolidate evidence into one result |
| FMP | Allowable Annual Cut (AAC) | Find AAC evidence in each text chunk | Consolidate and format AAC result |
| Forest Inventory Analysis (FIA) | Commercial volume | Find volume/acres evidence in each text chunk | Consolidate and format volume result |
| FIA | Indicated Allowable Cut (IAC) | Find IAC evidence in each text chunk | Consolidate and format IAC result |
| FIA | Regulated Allowable Cut (RAC) | Find RAC evidence in each text chunk | Consolidate and format RAC result |

This two-pass structure is one of the project's most important design choices. The first pass narrows a long report to short evidence records. The second pass reviews those records together and produces a consistent result for the application.

## Shared guardrails

Every public template below follows the same core rules:

1. Use only the supplied document text; do not invent, estimate, or rely on outside sources.
2. Keep AAC, IAC, and RAC distinct. A value labeled for one metric cannot be reported as another.
3. Require a nearby label, heading, sentence, paragraph, or table cell that anchors the value to the requested metric.
4. Preserve numbers and units as written unless a separately approved conversion rule is explicit.
5. Return a stable, machine-readable plain-text layout.
6. Include a short supporting excerpt and page/section hint when evidence is found.
7. Return an exact no-evidence token when the chunk does not contain qualifying evidence.
8. Treat every result as a candidate that must be checked by a person against the source document.

## Placeholder convention

- `{{chunk_text}}` is one OCR text segment.
- `{{evidence_lines}}` is the joined output from all qualifying first-pass results for one metric.
- Bracketed items such as `[approved policy reference]` are deployment-specific and require organizational review.

---

## 1. FMP plan-period evidence prompt

```text
ROLE
Locate plan-period evidence in one Forest Management Plan text chunk.

INPUT
{{chunk_text}}

RULES
- Use only the chunk text. Do not infer dates.
- Accept “Year of the Plan” only when it is tied to approval, certification,
  adoption, or equivalent plan-approval language.
- Report start and end years only when the text states them explicitly.
- Mark a plan non-expiring only when the text clearly says it has no
  expiration, remains effective until revised, or uses equivalent language.
- Keep the response under the configured output limit.

RETURN
If no qualifying evidence exists, return exactly:
NO PLAN PERIOD EVIDENCE

Otherwise return:
Year of the Plan: <year or Not found>
Plan Start Year: <year or Not found>
Plan End Year: <year or Not found>
Non-expiring: <Yes/No/Not found>
Excerpt: "<short exact quote>"
Page: <page or Unknown>
Section: <section or Unknown>
```

## 2. FMP plan-period consolidation prompt

```text
ROLE
Create one plan-period result from the evidence records below.

INPUT
{{evidence_lines}}

RULES
- Use only explicit dates in the evidence.
- Prefer approval-page evidence for “Year of the Plan.”
- Do not turn an inferred duration into start or end years.
- If evidence conflicts, report the conflict rather than choosing silently.
- Include one or two short references only when at least one value is found.

RETURN
Year of the Plan: <year or Not listed in the FMP>
Plan Start Year: <year or Not listed in the FMP>
Plan End Year: <year or Not listed in the FMP>
Is the plan listed as non-expiring?: <Yes/No/Not listed>
References:
- <page/section>: "<short excerpt>"
```

---

## 3. FMP AAC evidence prompt

```text
ROLE
Locate Allowable Annual Cut (AAC) evidence in one FMP text chunk.

INPUT
{{chunk_text}}

RULES
- Use only the chunk text. Do not infer or estimate.
- AAC, IAC, and RAC are different. Never report IAC or RAC as AAC.
- Accept a number only when an AAC label or an approved AAC synonym is in
  the same line, sentence, paragraph under an AAC heading, or table row.
- Prefer values under an explicitly AAC-labeled section or table.
- Preserve units. Do not combine incompatible unit families.
- Any authorized arithmetic or conversion must be documented and tested
  separately; otherwise quote the value exactly as written.

RETURN
If no qualifying evidence exists, return exactly:
NO AAC EVIDENCE

Otherwise return:
AAC: <value or Not found>
Timberland AAC: <value or Not found>
Woodland AAC: <value or Not found>
Units: <units or Unknown>
Excerpt: "<short exact quote>"
Page: <page or Unknown>
Section: <section or Unknown>
```

## 4. FMP AAC consolidation prompt

```text
ROLE
Create one AAC result from the evidence records below.

INPUT
{{evidence_lines}}

RULES
- Report only explicit AAC values.
- Do not substitute IAC or RAC.
- Keep separate timberland and woodland values when the evidence does.
- Preserve units and identify conflicts or incompatible unit families.
- Do not manufacture a forest-wide total from incomplete evidence.

RETURN
AAC: <value or Not listed in the FMP>
Timberland AAC: <value or Not listed in the FMP>
Woodland AAC: <value or Not listed in the FMP>
AAC Unit: <units or Unknown>
References:
- <page/section>: "<short excerpt>"
```

---

## 5. FIA commercial-volume evidence prompt

```text
ROLE
Locate commercial-volume and related acreage evidence in one FIA text chunk.

INPUT
{{chunk_text}}

RULES
- Use only the chunk text. Do not infer or estimate.
- Accept a value only when it is tied to total volume, merchantable volume,
  commercial volume, or another approved volume label.
- Reject values that are clearly growth, mortality, stocking, basal area,
  AAC, IAC, or RAC.
- Read tables only when the title, header, or row clearly establishes the
  requested volume or acreage measure.
- Preserve values and units as written.

RETURN
If no qualifying evidence exists, return exactly:
NO COMMERCIAL VOLUME EVIDENCE

Otherwise return:
Total Volume: <value or Not found>
Timberland Volume: <value or Not found>
Woodland Volume: <value or Not found>
Total Acres Timberland: <value or Not found>
Total Acres Commercial: <value or Not found>
Total Acres Woodland: <value or Not found>
Units: <units or Unknown>
Excerpt: "<short exact quote>"
Page: <page or Unknown>
Section: <section or Unknown>
```

## 6. FIA commercial-volume consolidation prompt

```text
ROLE
Create one commercial-volume result from the evidence records below.

INPUT
{{evidence_lines}}

RULES
- Report only explicit values supported by the evidence.
- Prefer an explicit total over a calculated total.
- Do not add components unless an approved calculation rule is active.
- Keep timberland, woodland, and acreage fields separate.
- Preserve units and show one or two short references.

RETURN
Total Volume: <value or Not listed in the FIA>
Timberland Volume: <value or Not listed in the FIA>
Woodland Volume: <value or Not listed in the FIA>
Total Acres Timberland: <value or Not found>
Total Acres Commercial: <value or Not found>
Total Acres Woodland: <value or Not found>
Volume Units: <units or Unknown>
References:
- <page/section>: "<short excerpt>"
```

---

## 7. FIA IAC evidence prompt

```text
ROLE
Locate Indicated Allowable Cut (IAC) evidence in one FIA text chunk.

INPUT
{{chunk_text}}

RULES
- Use only the chunk text. Do not infer or estimate.
- IAC, AAC, and RAC are different. Never report AAC or RAC as IAC.
- Require an IAC label or a spelled-out term explicitly linked to IAC near
  the number or in the same table row.
- Prefer an explicit total, gross, net, timberland, or woodland IAC field.
- Reject unrelated fee, inventory, growth, and non-IAC values.
- Preserve units and exact number formatting.

RETURN
If no qualifying evidence exists, return exactly:
NO IAC EVIDENCE

Otherwise return:
IAC: <value or Not found>
Timberland IAC (Gross): <value or Not found>
Timberland IAC (Net): <value or Not found>
Woodland IAC (Gross): <value or Not found>
Woodland IAC (Net): <value or Not found>
IAC Unit: <units or Unknown>
Excerpt: "<short exact quote>"
Page: <page or Unknown>
Section: <section or Unknown>
```

## 8. FIA IAC consolidation prompt

```text
ROLE
Create one IAC result from the evidence records below.

INPUT
{{evidence_lines}}

RULES
- Use only explicit IAC values in the evidence.
- Do not infer IAC from AAC or RAC.
- Keep gross, net, timberland, and woodland values distinct.
- Preserve units and identify conflicts rather than silently choosing.

RETURN
IAC: <value or Not listed in the FIA>
Timberland IAC (Gross): <value or Not listed in the FIA>
Timberland IAC (Net): <value or Not found>
Woodland IAC (Gross): <value or Not listed in the FIA>
Woodland IAC (Net): <value or Not listed in the FIA>
IAC Unit: <units or Unknown>
References:
- <page/section>: "<short excerpt>"
```

---

## 9. FIA RAC evidence prompt

```text
ROLE
Locate Regulated Allowable Cut (RAC) evidence in one FIA text chunk.

INPUT
{{chunk_text}}

RULES
- Use only the chunk text. Do not infer or estimate.
- RAC, IAC, and AAC are different. Never report IAC or AAC as RAC.
- Require “RAC” as a standalone label or a spelled-out term explicitly
  linked to RAC in the same line, paragraph, or table row.
- Read a table only when its header or row establishes RAC.
- Preserve values and units. Do not convert units unless an approved rule
  is explicit in the prompt and covered by tests.

RETURN
If no qualifying evidence exists, return exactly:
NO RAC EVIDENCE

Otherwise return:
RAC: <value or Not found>
Timberland RAC (Gross): <value or Not found>
Timberland RAC (Net): <value or Not found>
Woodland RAC (Gross): <value or Not found>
Woodland RAC (Net): <value or Not found>
RAC Unit: <units or Unknown>
Excerpt: "<short exact quote>"
Page: <page or Unknown>
Section: <section or Unknown>
```

## 10. FIA RAC consolidation prompt

```text
ROLE
Create one RAC result from the evidence records below.

INPUT
{{evidence_lines}}

RULES
- Report only explicit RAC values.
- Do not substitute IAC or AAC.
- Keep gross, net, timberland, and woodland values distinct.
- Preserve units and include references only when RAC is found.

RETURN
RAC: <value or Not listed in the FIA>
Timberland RAC (Gross): <value or Not listed in the FIA>
Timberland RAC (Net): <value or Not listed in the FIA>
Woodland RAC (Gross): <value or Not listed in the FIA>
Woodland RAC (Net): <value or Not listed in the FIA>
RAC Unit: <units or Unknown>
References:
- <page/section>: "<short excerpt>"
```

---

## Prompt engineering features demonstrated

The project demonstrates more than a single question-and-answer prompt. Its design includes:

- **Metric-specific evidence gates:** values must be anchored to the requested label.
- **Cross-metric exclusion rules:** AAC, IAC, and RAC cannot be substituted for one another.
- **Two-pass analysis:** long documents are reduced to evidence before consolidation.
- **Structured output contracts:** predictable labels make downstream display and review easier.
- **Traceability:** excerpts and page/section hints support human verification.
- **Explicit absence handling:** exact no-evidence tokens reduce ambiguous empty responses.
- **Unit discipline:** prompts preserve units and avoid unsupported conversions.
- **Human review by design:** outputs are candidate findings, not official determinations.

## Public testing approach

Use only synthetic or approved non-sensitive fixtures. At minimum, test:

| Test | Expected behavior |
|---|---|
| Correct metric label and value | Returns the value and exact supporting excerpt |
| Number without a nearby metric label | Rejects the number |
| AAC and IAC appear together | Keeps them distinct |
| No qualifying evidence | Returns the exact no-evidence token |
| Conflicting values | Reports the conflict or keeps the result unresolved |
| Missing units | Returns `Unknown` rather than guessing |
| Malicious instructions inside the PDF | Treats document text as data and follows the system prompt |
| Long report with evidence near a chunk boundary | Overlap preserves the relevant passage |

See [`FLOW_BUILD_GUIDE.md`](FLOW_BUILD_GUIDE.md) for how these prompt pairs fit into the public-safe workflow blueprint.
