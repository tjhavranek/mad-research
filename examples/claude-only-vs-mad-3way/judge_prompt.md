# The judge prompt (verbatim template)

This is the prompt template the Gemini judge received for each paper,
reconstructed verbatim from the orchestration script that ran the
comparison. `${p.title}` was replaced by the paper's title; the three
memos were appended below the instruction, each under a header
`===== REPORT A/B/C =====`, in the per-paper randomized order given in
the README's blinded-mapping table. The judge received **only this prompt
and the three memos** — not the underlying manuscripts.

```text
You are an independent senior meta-analysis methodologist acting as a
blinded judge. Below are THREE audit reports (A, B, C) of the SAME paper
"${p.title}", produced by three undisclosed methods. They differ in length
and format — judge SUBSTANCE, not length or polish.
Score each report on: (1) groundedness (are criticisms tied to real
quotes/locators and correct?), (2) materiality (do they target things that
matter?), (3) severity calibration (neither over- nor under-stated),
(4) coverage (important issues found), (5) false-positive risk (wrong or
over-stated criticisms).
Then: rank A/B/C from best to worst for an author who wants to improve the
paper, name the single best report, and give 2-3 sentences per report
justifying the placement. Be concrete and cite which specific criticisms
drove your judgement. Do not speculate about which method produced which
report.
```

Notes for auditors:
- The re-evaluations for `class`, `beauty`, and `alphas` (run after their
  T-arm memos were recovered from the rate-cap interruption) used the same
  instruction wording, assembled by an equivalent script.
- "Judge SUBSTANCE, not length or polish" is a length/style-neutrality
  instruction; the 2026 judge-bias literature finds such instructions
  reduce but do not eliminate verbosity/style bias (see the README's
  caveats).
- Per-dimension numeric scores were not requested; the deliverable was a
  ranking plus stated reasons, which is what `gemini_verdict.md` contains.
