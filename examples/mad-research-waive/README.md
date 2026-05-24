# Worked example: MAD-research on WAIVE

A real run of the `mad-research` skill (Mode B) on a paper plus
presentation pair from the meta-research literature. Inputs reused from
the `tjhavranek/research-audit-duel-protocol` v1.7 example so this run
can be compared directly to the manual Duel v1.7 output.

## Inputs

- `input/maive.pdf` — Irsova, Bom, Havranek, Rachinger,
  *Spurious precision in meta-analysis of observational research*,
  Nature Communications 16: 8454 (2025). The published baseline.
- `input/waive_ottawa.pdf` — Havranek et al., *WAIVE: Weighted
  Adjustment Instrumental Variable Estimator*, MAER-Net Annual
  Colloquium, Ottawa, 18 October 2025. The proposed extension under
  audit.
- `input/manuscript_text.md` — text version with `<!-- PAGE_INDEX N -->`
  and `<!-- WAIVE slide N -->` locator comments. This is what the two
  Codex streams read. Claude (Methodologist stream) read the PDFs
  directly.

## Task

> Stress-test the claimed contribution of WAIVE over MAIVE as a top-5
> referee would.

## Output

- `round1/methodologist.md` — Claude as Methodologist (Round 1).
- `round1/evidence_auditor.md` — Codex as Evidence Auditor (Round 1).
- `round1/contribution_skeptic.md` — Codex as Contribution Skeptic (Round 1).
- `round2/*_R2.md` — anonymized cross-critiques.
- `final_memo.md` — Claude's synthesis with locked-rubric verdict, top
  6 surviving criticisms, minority report (double-counting concern),
  points rejected, action list, audit trail.
- `build_notes/codex_critique_of_example.md` — Codex's critique of
  this entire example, run *after* the synthesis was first drafted.
  Caught five real bugs in the original synthesis (fabricated
  Anderson-Rubin reference, "External considerations: None" overclaim,
  mis-labeled minority report, missing reproducibility objection,
  overstated independence). The final memo above is the revision.

## What this example demonstrates

**1. The protocol works.** The three streams produced substantively
distinct outputs (despite two being from the same Codex model),
converged on three high-severity grounded objections (negative-residual
interpretation, exponential weight motivation, missing ablation), and
the Contribution Skeptic surfaced a new structural concern in Round 2
(double-counting of the first-stage residual) that became the
designated minority report.

**2. The protocol critiques itself.** Codex's post-synthesis critique
of the final memo caught real synthesis bugs that Claude (the
orchestrator) missed. This is not a failure of the protocol — it is
the protocol working as designed. The final memo includes both the
revisions and a transparent record of what was changed and why.

**3. Honest about limitations.** The audit's final 5 slides of WAIVE
(slides 22-26, results/applications/conclusions/Q&A) were truncated in
text extraction. The synthesis acknowledges this prominently. Any
external reader should re-evaluate the criticisms against the full
slide deck before forwarding the memo.

**4. The example is not a hostile audit.** The orchestrator and the
WAIVE team are collaborators. The example was deliberately chosen as a
case where the skill would have to produce real criticism of friendly
work. A usable adversarial audit tool that can only produce critiques
of strangers is not useful.

## Reproducing this example

You cannot exactly reproduce the Codex outputs (LLMs are non-deterministic)
but you can reproduce the structure: install the skill (see the repo
README), put the two PDFs in a folder, and ask Claude Code:

```
MAD-research on maive.pdf and waive_ottawa.pdf. Task: stress-test
the WAIVE proposal as a top-5 referee would.
```

Expect ~30 minutes wall-clock and 4-6 Codex calls.

## Cost

This run used 5 Codex `exec` calls (2 R1 + 2 R2 + 1 critique) plus
Claude's own work. On a $20 Codex Plus subscription, this was well
within monthly quota and took ~30 minutes wall-clock. PDF processing
by Claude is covered by the user's Claude subscription.

## License

The reuse of MAIVE and WAIVE source material here is for demonstration
of the auditing skill. Both source documents are openly accessible (MAIVE
is open-access Nature Communications; WAIVE slides are publicly hosted at
the MAER-Net colloquium archive). Credit to the original authors.
