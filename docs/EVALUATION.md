# The planned ground-truth evaluation (public specification)

**Status: specified, not yet run.** No configuration of `mad-research` has
been validated against ground truth: across all releases there is no
measured catch rate against known flaws, no false-positive rate, and no
human adjudication of any memo on external work. Every contested design
question (above all: does the cross-model default beat Claude-only?) is
deferred to the study below. Publishing the spec is part of the answer to
that criticism — it makes the deferral checkable instead of indefinite.

This spec incorporates the fix-list produced when the design memo was
itself audited by the skill (the 2026-06-02 "dogfood MAD"), and the 2026
judge-bias literature.

## Question

Does including Codex (cross-model) in `mad-research` produce better audits
than an otherwise-identical Claude-only configuration — and is the margin,
in either direction, worth Codex's cost and fragility?

## Arms (configuration-level, not a clean single-factor ablation)

All arms share the same prompts, rubric, rounds, and output template.

- **T (as-built):** Claude Methodologist + Codex Evidence Auditor + Codex
  Contribution Skeptic + fresh-Codex synthesis.
- **C1 (Claude-only):** Claude in all three streams + a fresh-context
  Claude subagent as synthesizer.
- **Seat-ablation arms (added per dogfood finding H1):** because T differs
  from C1 in a *bundle* of seats, "the pure cross-model effect" is not
  identified by T vs C1 alone. At minimum: Claude streams + Codex judge,
  and Codex streams + Claude judge. Equal token budgets; identical text
  inputs; output scaffolding stripped to a common format before judging
  (the v1.1 example showed arm-identifiable scaffolding contaminates
  blinding).

## Ground truth (three tracks)

1. **Seeded flaws (primary).** Host papers perturbed with a pre-registered
   set of graded defects (mis-computed pooled SE, dropped clustering
   adjustment, over-claimed causal sentence, misquoted citation,
   uncontrolled multiplicity). Outcomes: recall of seeded defects and
   false-alarm rate, scored against a **pre-registered codebook**
   (defect→criticism matching rules, duplicate handling, partial credit,
   false-positive netting — dogfood H2).
2. **Organic-flaw validation (dogfood M5).** Human adjudication of
   criticisms on unperturbed real papers, to test whether seeded-flaw
   recall transports to real audit quality.
3. **Blinded relative quality, multi-judge.** Not one judge: a roster of
   **three frontier judges from three model families** (per the 2026
   judge-bias literature: style bias 0.76–0.92 and verbosity bias ≈ +17%
   make any single LLM judge inadequate), with length/style-neutral
   instructions, position counterbalancing, and per-dimension scores
   reported. Judge–human agreement (κ) computed on the human-adjudicated
   subset; low κ down-weights the judge track by a pre-registered rule
   (dogfood M4 requires the sampling frame, blinding, κ threshold, and
   down-weighting function fixed in advance).

## Discipline

- **Replication:** k ≥ 3 independent runs per arm per paper; report
  distributions. (With k = 3 the variance estimate is weak — dogfood H3 —
  so the decision rule below is pre-registered with an explicit minimum
  important difference rather than a "beyond variance" test.)
- **Pre-registration:** the seeded-defect key, the scoring codebook, the
  judge roster and prompts, the decision rule (test statistic, minimum
  important difference, cost ceiling, conflict rule between recall and
  calibration), and the failed-run handling (intention-to-treat vs retry
  vs exclude; per-arm failure rates reported as an outcome — dogfood M6).
- **Provenance (dogfood M7 + the v1.1 example's lesson):** exact model IDs
  (served model, not just CLI version), CLI versions, commands, and seeds
  logged per call; raw per-stream artifacts copied to durable storage at
  capture time; all artifacts published.
- **Test-bed breadth:** papers beyond the authors' own ecosystem and genre
  (the v1.1 example used five of the authors' own economics meta-analyses;
  generalization is untested).

## Decision rule (shape, to be pre-registered exactly)

Codex retains the default if T (or a Codex-containing ablation arm)
exceeds C1 on seeded-flaw recall net of false alarms by at least the
pre-registered minimum important difference, at acceptable marginal cost.
A null or negative result flips the default to Claude-only and demotes
cross-model to the opt-in. **A null is informative and will be published
as such.**

## What exists today instead

- A blinded but single-judge, single-run, confounded 3-way example
  ([`examples/claude-only-vs-mad-3way/`](../examples/claude-only-vs-mad-3way/)) —
  evidence about perceived quality, judged honest-but-weak by its own
  README.
- Scattered cross-model catches in the development history (CHANGELOG
  v0.5, v1.0.1, v1.1.2) — anecdotal, not systematic.
- The 2026 external literature: group diversity among the dominant drivers
  of debate success (arXiv:2511.07784) — supportive of the cross-model
  premise but not a substitute for measuring this tool.
