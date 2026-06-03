# Claude-only vs. full MAD (with Codex): a 3-way audit comparison

This example asks a concrete question about the `mad-research` skill: **does adding Codex (cross-model) actually produce better audits than an otherwise-identical Claude-only protocol?** It runs three audit methods on the **same five meta-analyses** and has an **independent third model (Gemini)** judge them blind.

The five papers are recent meta-analyses from meta-analysis.cz: relative risk aversion, class size & achievement, exercise & cognition, beauty & professional success, and hedge-fund alphas.

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
1. **The structured protocol clearly helps.** Both T and C1 (three streams + cross-critique + fresh-judge synthesis) beat the simpler 5-lens Panel on every paper — largely because the Panel gave fewer exact quotes/locators and less severity calibration.
2. **Codex did *not* earn its place here.** The all-Claude version of the same protocol (C1) was ranked *above* the Codex-augmented version (T) on all five papers, by an independent judge.

### Why C1 edged out T (concrete, from Gemini's stated reasons)
- **risk:** the **Codex (T) arm** set the weak-instrument / GMM mechanical estimate–SE argument aside as `[EXTERNAL]` (unprovable from the manuscript text) — the more conservative, strictly-grounded call, and arguably *more correct* by the skill's own grounding rules — yet it ranked below the all-Claude arm, which won on other grounded catches (e.g. inconsistent reported finance ranges). The judge rewarded reach over caution.
- **beauty:** C1 uniquely caught a **sign contradiction** (text claims "no attenuation bias" while Table S2 shows IV > OLS) and a transcription error in the sex-worker credible intervals; T was "high-quality and well-calibrated" but less granular.
- Across papers the pattern repeats: the all-Claude arm produced deeper, more granular, more *material* catches; Codex produced solid, well-grounded, but slightly more conservative audits.

## Honest caveats (read before drawing conclusions)
- **This is an illustration, not evidence.** n = 5 papers, **one run per arm** (no replication), so it is underpowered and not a statistical test.
- **One judge.** Gemini is itself an LLM with its own preferences; it may systematically favour the longer, more assertive Claude style. A different judge — or a human expert — could rank differently. There is **no seeded ground truth** here: Gemini scored *perceived* quality, not verified correctness.
- **Conservatism was penalized.** On `risk`, Codex's choice to reject an unprovable-from-text claim is arguably *more correct* by the skill's own grounding rules, yet it scored lower. "Better" here means "the judge found it more useful," which is a value judgment about reach vs. strict grounding.
- **Confounds.** T vs C1 changes the model *and* the synthesis mechanism (a separate Codex process vs an in-harness Claude subagent). A clean test would also vary those independently.
- **Blinding was imperfect.** The Codex-arm (T) memos kept protocol scaffolding — `Supporting audits: X/Y/Z` tags and source line-locators — that the Claude-only (C1) memos did not, so the arms were stylistically distinguishable even with headers stripped, and on `alphas` the judge explicitly docked the T report for that "distracting meta-commentary." Part of the `C1 > T` gap is therefore **presentation, not reasoning** — treat `C1 > T` as weaker than the (unaffected) finding that both structured arms beat the Panel.
- **A real test would** seed known flaws, add human adjudication, replicate with k ≥ 3 seeds, and use more than one arbiter. This example motivates that experiment; it does not substitute for it.

## Bottom line
On these five meta-analyses, judged blind by an independent Gemini arbiter, **the full Codex MAD did not beat the Claude-only version of the same protocol.** The measurable value came from the *structured protocol*, not from adding a second model. Codex's contribution here was real but largely *redundant* with Claude's (and on one paper more conservative in a way the judge penalized) — worth weighing against Codex's extra cost and the rate-limit fragility noted below.

## Files (per paper)
- `full_mad_codex.md` — the **T** arm's final memo (fresh-Codex synthesis).
- `claude_only.md` — the **C1** arm's final memo (all-Claude, same protocol).
- `gemini_verdict.md` — Gemini's blinded scoring and ranking.
- (Raw per-stream outputs are omitted: a capture-encoding issue during the rate-limit recovery corrupted two of them, so only the final memos and the blinded verdict — the substantive artifacts — are included.)
- The 5-lens Panel verdict for each paper is reproduced at the bottom of this README.

## Reproducibility notes
- T-arm Codex calls were real `codex exec` runs (v0.133.0, `--sandbox read-only`, prompt via stdin): 5 Codex calls per paper (2 Round-1 streams, 2 Round-2 streams, 1 synthesis). Claude verified quotes and formatted only — it did not write the verdict.
- Codex hit its daily usage cap mid-run; 6 calls across 3 papers (`class`, `beauty`, `alphas`) were re-run after the cap reset, so the final memos here are complete multi-stream runs. (The raw per-stream files produced during that recovery had encoding corruption and are therefore not included; the synthesized memos are clean.)
- Gemini's CLI emitted a non-fatal internal "NumericalClassifierStrategy" routing error on some calls; the substantive evaluation still generated correctly and is what is saved here.

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
