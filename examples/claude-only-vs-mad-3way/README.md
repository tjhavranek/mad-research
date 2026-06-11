# Claude-only vs. full MAD (with Codex): a 3-way audit comparison

This example asks a concrete question about the `mad-research` skill: **does adding Codex (cross-model) actually produce better audits than an otherwise-identical Claude-only protocol?** It runs three audit methods on the **same five meta-analyses** and has an **independent third model (Gemini)** judge them blind.

The five papers are recent meta-analyses from meta-analysis.cz: relative risk aversion, class size & achievement, exercise & cognition, beauty & professional success, and hedge-fund alphas. **Provenance disclosure:** all five are co-authored by this repo's authors — the same deliberate choice as the WAIVE example (the tool must criticize friendly work), but it also means the test bed is a single genre (economics meta-analysis) from the authors' own ecosystem; treat generalization to other fields and genres as untested.

## The three methods (arms)

| Arm | What it is | Models |
|---|---|---|
| **T — full MAD (as-built)** | The real `mad-research` protocol: three Round-1 streams (Methodologist + Evidence Auditor + Contribution Skeptic), an anonymized Round-2 cross-critique, then a **fresh-Codex synthesis** against the locked rubric | Claude (1 stream) + **Codex** (2 streams + synthesis) |
| **C1 — structure-matched Claude-only** | The *same* protocol, prompts, rubric, and rounds — but **Claude in every seat**, including a fresh-context Claude subagent as the synthesizer | Claude only |
| **Panel — 5-lens Claude-only** | The earlier, simpler single-pass panel (5 independent lenses, no cross-critique, no fresh-judge synthesis) | Claude only |

`T vs C1` compares **the model** (Codex vs Claude) with the protocol held fixed — but it also changes the synthesis mechanism (a separate Codex process vs an in-harness Claude subagent), so it is not a clean model ablation; see the caveats. `T/C1 vs Panel` shows the effect of **the structured protocol** over a generic panel.

## The judge

**Gemini** (via CLI), chosen because it is independent of both Claude and Codex. For each paper the three memos were stripped of identifying headers, **randomized and relabelled A/B/C** (a different order per paper), and Gemini scored them blind on groundedness, materiality, severity calibration, coverage, and false-positive risk — then ranked them. (Blinding was imperfect — stylistic differences between the arms remained; see the caveats below.)

## Result

**Unanimous across all five papers: `C1 > T > Panel`.**

| Paper | Blinded ranking | Decoded | Winner |
|---|---|---|---|
| Relative risk aversion | B > A > C | **C1 > T > Panel** | Claude-only |
| Class size | C > B > A | **C1 > T > Panel** | Claude-only |
| Exercise & cognition | A > C > B | **C1 > T > Panel** | Claude-only |
| Beauty & success | C > A > B | **C1 > T > Panel** | Claude-only |
| Hedge-fund alphas | A > B > C | **C1 > T > Panel** | Claude-only |

(Blinded→arm mapping per paper is at the bottom, for auditability.)

### What this says
1. **The judge consistently preferred the structured-protocol memos over the Panel.** Both T and C1 (three streams + cross-critique + fresh-judge synthesis) were ranked above the simpler 5-lens Panel on every paper — the judge's stated reasons were fewer exact quotes/locators and weaker severity calibration in the Panel summaries. (Note the Panel inputs were also shorter and structurally distinct, so document form contributes to this margin too.)
2. **The same judge ranked the Claude-only memos above the Codex-augmented ones on all five papers.** That is a consistent perceived-quality verdict from one LLM judge — not a measurement of which arm caught more true flaws (no ground truth exists here), and not a causal decomposition of *why* (see Confounds).

### Why C1 edged out T (concrete, from Gemini's stated reasons)
- **risk:** the **Codex (T) arm** set the weak-instrument / GMM mechanical estimate–SE argument aside as `[EXTERNAL]` (unprovable from the manuscript text) — the more conservative, strictly-grounded call, and arguably *more correct* by the skill's own grounding rules — yet it ranked below the all-Claude arm, which won on other grounded catches (e.g. inconsistent reported finance ranges). The judge rewarded reach over caution.
- **beauty:** C1 uniquely caught a **sign contradiction** (text claims "no attenuation bias" while Table S2 shows IV > OLS) and a transcription error in the sex-worker credible intervals; T was "high-quality and well-calibrated" but less granular.
- Across papers the pattern repeats: the all-Claude arm produced deeper, more granular, more *material* catches; Codex produced solid, well-grounded, but slightly more conservative audits.

## Honest caveats (read before drawing conclusions)
- **This is an illustration, not evidence.** n = 5 papers, **one run per arm** (no replication), so it is underpowered and not a statistical test.
- **One judge — so 5/5 unanimity is judge consistency, not five confirmations.** The five rankings share one judge, one run per arm, and persistent arm-level style differences; adding more papers would not add independent evidence — more judges and replicate runs would. Gemini is itself an LLM with its own preferences, and the 2026 judge-bias literature quantifies how strong such preferences are: style bias 0.76–0.92 across judge models (markdown/format preferred even for identical content) and verbosity bias ≈ +17% (arXiv:2604.23178; arXiv:2604.16790). A different judge — or a human expert — could rank differently; current best practice is a roster of ~3 judges from 3 model families, which this example did not have. There is **no seeded ground truth** here: Gemini scored *perceived* quality, not verified correctness.
- **What the judge saw.** Gemini received **only the three memos** per paper (embedded in one prompt, relabelled A/B/C) — **not the five underlying manuscripts**. Its "groundedness" and "false-positive risk" scores therefore reflect how specific and internally consistent each memo's citations *appear*, not whether they verify against the source papers. The judge-prompt template is published verbatim in [`judge_prompt.md`](judge_prompt.md); per-dimension scores were not requested, only rankings with stated reasons.
- **Conservatism was penalized.** On `risk`, Codex's choice to reject an unprovable-from-text claim is arguably *more correct* by the skill's own grounding rules, yet it scored lower. "Better" here means "the judge found it more useful," which is a value judgment about reach vs. strict grounding.
- **Confounds.** T vs C1 changes the model *and* the synthesis mechanism (a separate Codex process vs an in-harness Claude subagent). A clean test would also vary those independently.
- **Blinding was imperfect.** The Codex-arm (T) memos kept protocol scaffolding — `Supporting audits: X/Y/Z` tags and source line-locators — that the Claude-only (C1) memos did not, so the arms were stylistically distinguishable even with headers stripped, and on `alphas` the judge explicitly docked the T report for that "distracting meta-commentary." Part of the `C1 > T` gap is therefore **presentation, not reasoning**. The protocol-beats-Panel margin is less exposed to this particular tell (the scaffolding distinguished T from C1, not the structured arms from the Panel), but it is **not unaffected** by the deeper limits: the Panel texts were also stylistically identifiable and much shorter, and the single-judge / perceived-quality / no-ground-truth caveats apply to every margin in this example.
- **A real test would** seed known flaws, add human adjudication, replicate with k ≥ 3 seeds, and use more than one arbiter. This example motivates that experiment; it does not substitute for it.

## Bottom line
On these five meta-analyses, **one blinded LLM judge consistently ranked the Claude-only memos above the Codex cross-model memos, and both above the simple panel.** Read with all the caveats above, this is a single, internally consistent, confounded observation — enough to motivate shipping the Claude-only mode as an option and to justify the ground-truth evaluation in [`docs/EVALUATION.md`](../../docs/EVALUATION.md), and not enough to establish which configuration catches more real flaws. The judge's stated reasons suggest the Claude arm produced more granular catches and the Codex arm was more conservative (which the judge penalized) — a hypothesis the planned evaluation can test, not a conclusion this example can carry. Codex's per-run cost and rate-cap fragility (below) are real and count against the default independently of audit quality.

## Files (per paper)
- `full_mad_codex.md` — the **T** arm's final memo (fresh-Codex synthesis).
- `claude_only.md` — the **C1** arm's final memo (all-Claude, same protocol).
- `gemini_verdict.md` — Gemini's blinded scoring and ranking.
- `judge_prompt.md` — the verbatim judge-prompt template Gemini received.
- The 5-lens Panel verdict for each paper is reproduced at the bottom of this README — these paragraphs are **verbatim what the judge received** as the Panel arm.
- Raw per-stream outputs do **not** ship — see "Evidence status" below for the single authoritative account.

## Evidence status — what survives and what was lost (authoritative account)
Earlier versions of this README described the raw-file situation
inconsistently ("corrupted two of them" / "three papers' files lost"); this
section replaces all previous accounts.

1. During the original run, Codex's daily usage cap interrupted the T arm;
   six calls across three papers (`class`, `beauty`, `alphas`) were re-run
   after the cap reset. The **final synthesized memos** for all five papers
   are complete multi-stream products and ship here unchanged.
2. The recovery's capture path had an encoding fault: the compiled
   per-paper stream files for `beauty` and `alphas` were corrupted
   (mojibake), and `class`'s contained a stray delimiter token. At the v1.1
   pre-release review, all five compiled per-stream files were withdrawn
   from the example (two corrupted, the rest removed for uniformity).
3. The underlying raw working files were retained only in a temporary
   directory, which was later removed by routine OS temp cleanup before
   they could be archived. **No raw per-stream artifact now survives, for
   any arm, on any paper.**
4. Consequently this example **fails the protocol's own retention
   standard**: the memo→stream→manuscript traceability chain and the
   raw-vs-final anti-tamper comparison cannot be exercised on it. Weigh the
   example accordingly — the memos and blinded verdicts are genuine, but
   intermediate provenance is unverifiable. (Adopted rule for future runs:
   raw stream outputs are copied into a durable session folder at capture
   time, never left in temp.)
5. The disruption was **treatment-correlated**: it affected only the losing
   (T) arm, on three of five papers. Whether it depressed those three T
   memos' quality cannot be established from surviving artifacts — one more
   reason the `C1 > T` margin should be read as weak.

## Reproducibility notes
- T-arm Codex calls were real `codex exec` runs (CLI v0.133.0,
  `--sandbox read-only`, prompt via stdin): 5 Codex calls per paper
  (2 Round-1 streams, 2 Round-2 streams, 1 synthesis). Claude verified
  quotes and formatted only — it did not write the verdict.
- **Backing models (recorded imperfectly — itself a lesson):** the Codex
  CLI banner on the logged recovery calls showed served model `gpt-5.5`;
  per-call served-model identity was not systematically recorded for the
  original calls. The C1 and Panel arms ran as Claude Code subagents
  inheriting the orchestrating session's model (Claude Opus 4.8 at the
  time, June 2026). The judge ran via the Gemini CLI (npm
  `@google/gemini-cli`); the served Gemini model was not recorded. Future
  runs record exact model identities per call in `meta.json`.
- Gemini's CLI emitted a non-fatal internal "NumericalClassifierStrategy"
  routing error on some calls; the substantive evaluation still generated
  correctly and is what is saved here.

## Blinded label → arm mapping (auditability)
| Paper | A | B | C |
|---|---|---|---|
| risk | T | C1 | Panel |
| class | Panel | T | C1 |
| exercise | C1 | Panel | T |
| beauty | T | Panel | C1 |
| alphas | C1 | T | Panel |

## Panel (5-lens Claude-only) verdicts used in the comparison
- **risk:** mostly robust, holds with caveats. Corrected RRA ~1 (economics), 2–7 (finance). Concerns: all six pub-bias corrections assume a clean/exogenous SE, but the SE is pathological (mean 76.6, SD 730; 59% weak-IV GMM) and MAIVE is absent → the ~7× exaggeration may be overstated; headlines load on one regressor (the SE) on its native scale; Table 6 best-practice values rest on a subjective structural/reliability split and debatable plug-ins.
- **class:** mostly robust, holds with caveats. Class-size effect negligible (~−0.21). Concerns: the sub-15-students exception rides on equal-weight BMA (inverse-variance weighting kills it, not shown); pub-bias diagnostics softer on the preferred subset, "surprisingly undistorted" mildly under-hedged.
- **exercise:** mostly robust, holds with caveats. Adjusted SMDs collapse (memory 0.027, exec-fn 0.012) amid huge heterogeneity. Concerns: the memory near-zero rests entirely on the PET-PEESE channel the authors flag as over-adjusting; comparator type omitted from moderators though it is first-order; 90+ subgroup tests with no multiplicity caveat; 81% of SEs back-computed.
- **beauty:** mostly robust, holds with caveats. Premium 4.3% → 3% (pub-bias) → ~1.1% (ns) after controlling cognitive ability. Concerns: the ~1.1% is a direct effect net of ability, but the authors' own developmental account treats ability as a mediator (so beauty may still pay in total); abstract over-reaches vs the Discussion; data/code on a researcher URL with no DOI.
- **alphas:** mostly robust, holds with caveats. Current best-practice alpha −0.079%/mo, insignificant; robust decline. Concerns: the negative sign rests on a single linear-in-log time trend (no break/flexible check, CI spans zero); "current" is set by fixing publication year at its sample max; "free of selection bias" overstates a near-inert SE=0 correction.

---
*Generated 2026-06-03. Method: `mad-research` (T), structure-matched Claude-only (C1), 5-lens Claude panel (Panel); blinded judge: Gemini. See the skill repo for the protocol.*
