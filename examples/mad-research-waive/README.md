# Worked example: `mad-research` on the WAIVE proposal

A real `mad-research` run on a paper-plus-presentation pair from the
meta-research literature. Inputs reused from the
[`tjhavranek/research-audit-duel-protocol` v1.7 example](https://github.com/tjhavranek/research-audit-duel-protocol)
so the automated and manual outputs can be compared directly.

## Inputs

- `input/maive.pdf` — Iršová, Bom, Havránek, Rachinger,
  *Spurious precision in meta-analysis of observational research*,
  Nature Communications 16: 8454 (2025). The published baseline.
- `input/waive_ottawa.pdf` — Havránek et al., *WAIVE: Weighted
  Adjustment Instrumental Variable Estimator*, MAER-Net Annual
  Colloquium, Ottawa, 18 October 2025. The proposed extension
  under audit.
- `input/manuscript_text.md` — structured text version with page
  locators preserved (`<!-- PAGE_INDEX N -->` for MAIVE pages and
  `WAIVE slide N` markers for the presentation). This is what the
  two Codex streams read; Claude (Methodologist stream) read the
  PDFs directly.

## Audit task

> Stress-test the claimed contribution of WAIVE over MAIVE as a
> top-5 referee would.

## What the skill produced

- `round1/` — three independent role streams (Methodologist via
  Claude; Evidence Auditor and Contribution Skeptic via separate
  `codex exec` calls).
- `round2/` — anonymized cross-critiques (each stream sees only the
  *other two* outputs, labeled Audit X / Y).
- `synthesis_packet/` — the anonymized inputs that were sent to the
  fresh-Codex synthesis call: `audit_X.md`, `audit_Y.md`,
  `audit_Z.md` (each containing one stream's Round 1 + Round 2
  output, role labels stripped), `manuscript_text.md`, `rubric.md`,
  and the synthesis prompt that was used.
- `final_memo_codex_raw.md` — the verbatim output of the fresh
  `codex exec` synthesis call. Preserved as the load-bearing audit
  artifact.
- `final_memo.md` — same content, lightly formatted by Claude as
  orchestrator, with a quote-verification note appended. Per the
  anti-tamper rule in `mad-research/helpers/orchestration.md`,
  Claude does not modify the verdict, criticisms, minority report,
  or action list — with one narrow exception: during mechanical
  quote verification, a criticism whose cited quote does not appear
  in the source can be moved to "Points rejected — locator failed
  verification." That move is logged in the audit trail; the original
  cited locator and the failure are preserved.

## What this example demonstrates

**In this run, three streams produced substantively distinct outputs
despite two being the same Codex model.** The Methodologist (Claude)
emphasised identification and instrument-validity concerns; the
Evidence Auditor (Codex) emphasised arithmetic, reproducibility, and
citation; the Contribution Skeptic (Codex) emphasised framing,
overclaim, and positioning. In this case, role priors and lane
boundaries appear to do the work — this is one friendly-case
observation, not evidence that the distinctiveness generalises across
documents or task types.

**A real grounded minority report survives.** The Contribution
Skeptic's Round 2 designated objection (WAIVE may double-use the
first-stage residual: MAIVE drops it from adjusted precision, then
WAIVE penalises observations based on negative values of it) was not
the majority view, but it survives in the final memo with explicit
counter-argument.

**The synthesis rejects unsupported claims with reasons.** The
`Points rejected` section in `final_memo.md` lists six criticisms
that were dropped during synthesis, each with a one-line reason
(no locator, locator failed verification, overread, or
reformulation). This is the audit trail.

**The audit is honest about extraction limits.** Slides 22-26 of
the WAIVE presentation were truncated in text extraction; the
memo's verdict acknowledges this prominently. An external reader
should re-evaluate any contribution-claim criticism against the
full slide deck.

The orchestrator and the WAIVE team are collaborators of one
another. The example was deliberately chosen as a case where the
skill would have to produce real criticism of friendly work.

## Reproducing this example

LLM outputs are non-deterministic, so a fresh run will not produce
the exact same memo. The *structure* is reproducible: install the
skills (see the [top-level README](../../README.md)), put the two
PDFs in a folder, and ask Claude:

```
MAD-research on maive.pdf and waive_ottawa.pdf. Task: stress-test
the WAIVE proposal as a top-5 referee would.
```

Expect ~30 minutes wall-clock and 5-6 Codex calls (two Round 1
streams + two Round 2 streams + the synthesis + optional doctor /
pre-flight). Typical cost on a $20 Codex Plus subscription:
$0.20-$0.50.

## License note

The reuse of MAIVE and WAIVE source material here is for
demonstration of the auditing skill. Both source documents are
openly accessible — MAIVE is open-access *Nature Communications*;
WAIVE slides are publicly hosted in the MAER-Net colloquium
archive. Credit to the original authors.
