# MAD-research final memo — WAIVE proposal

**Audit target:** WAIVE: Weighted Adjustment Instrumental Variable Estimator (Havranek et al., MAER-Net Annual Colloquium, University of Ottawa, 18 October 2025), with MAIVE (Irsova, Bom, Havranek, Rachinger, *Nature Communications* 16: 8454, 2025) as the published baseline.

**Audit task:** Stress-test the claimed contribution of WAIVE over MAIVE as a top-5 referee would.

**Mode:** Default (Round 1 + Round 2 + synthesis; Round 3 skipped — no qualifying trigger).

---

## Verdict

WAIVE is a plausible incremental extension of MAIVE that adds a second-stage downweight applied to estimates with negative first-stage residuals. The proposed mechanism (`ω_i = exp[−max(0, −π_i)]`, WAIVE slide 21) is transparent and easy to audit. After three streams (one Claude, two Codex) and one anonymized cross-critique round, four high-severity grounded objections survive scrutiny — including a structural double-counting concern flagged in Round 2 and preserved here as the minority report. Together they shift the burden of proof: WAIVE should currently be presented as "an experimental sensitivity layer on top of MAIVE," not as a methodological successor.

**Important caveat (read first):** the final five slides of the WAIVE presentation (slides 22–26 — results, applications, conclusions, Q&A) were truncated from the text extraction. Several criticisms below (especially #3 — ablation) may be partly addressed there. This audit's findings are conditional on the available corpus and should be re-evaluated against the full slide deck before any external use.

## Top 5 surviving criticisms

### 1. Negative first-stage residuals are not a unique signal of p-hacking
- Severity: **High**
- Evidence: "Negative π_i → reported SE too small relative to N → possibly p-hacking." (WAIVE slide 20). The published baseline says the same residual term "soaks up, among other things, the spurious elements of the reported standard error related to p-hacking." (MAIVE p. 4).
- Supporting audits: All three streams (Methodologist R1 #3, Evidence Auditor R1 #2, Contribution Skeptic R1 #2).
- Why it matters: The slide's wording is tentative ("possibly"), but the estimator's action is mechanical: every negative residual triggers a downweight. Negative π_i can also reflect heteroskedasticity, legitimate design heterogeneity (richer controls, tighter measurement, cluster structure), or scale choices on the LHS variable. "Among other things" is fatal for a classifier unless WAIVE shows those other things are rare or harmless in real meta-analyses. Until then, WAIVE risks penalizing honest design as if it were misconduct.

### 2. The exponential downweight `ω_i = exp(−max(0, −π_i))` is unmotivated and scale-dependent
- Severity: **High**
- Evidence: "Exponential tilt: ω_i = exp[ −max(0, −π_i) ] ... Weight ∈ (0, 1], smooth and proportional decay." (WAIVE slide 21). Slide 12 mentions "In practice it's better to use logs in the first stage" — but the displayed formula on slide 21 is not restated in log form.
- Supporting audits: All three streams (Methodologist R1 #2, Evidence Auditor R1 #3, Contribution Skeptic R1 #3).
- Why it matters: "Smoothness" is a property of many possible curves, not a derivation of this one. The functional form has no stated structural justification. Worse, π_i's units depend on whether the first stage is in `SE`, `SE²`, or `log SE` — so the same evidence can receive different weights after rescaling. A reader implementing WAIVE from the slides will get materially different answers depending on which scale they pick.

### 3. The contribution claim is unsupported until a clean MAIVE-vs.-WAIVE ablation is shown
- Severity: **High**
- Evidence: "WAIVE first stage — same as MAIVE." (WAIVE slide 20).
- Supporting audits: Methodologist R1 #1 (framed as instrument-validity inheritance), Evidence Auditor R1 #1, Contribution Skeptic R1 #1 and #4.
- Why it matters: The slides explicitly state the first stage is shared with MAIVE. The contribution therefore rests entirely on the second-stage downweight. The available slides show WAIVE doing more aggressive downweighting *visually* (slide 19), but no panel directly compares WAIVE-PEESE to MAIVE-PEESE under the same simulation grid or the same empirical sample. Until such an ablation exists, "successor" framing exceeds what the documented evidence supports.

### 4. The downweight is asymmetric without defense
- Severity: **Medium**
- Evidence: "Penalize only negative residuals (π_i < 0)." (WAIVE slide 21). MAIVE p. 4 says the residual absorbs "the spurious elements of the reported standard error related to p-hacking" — without committing to one sign.
- Supporting audits: New in Round 2 (Methodologist R2). Not raised in Round 1.
- Why it matters: If π_i captures both p-hacking and misspecification, then *positive* π_i (reported SE larger than design implies) could also signal misbehavior — over-conservative clustering, redundant controls, fishing for null results. WAIVE penalizes only one tail. The asymmetry is defensible (researchers' incentives point toward significance, not insignificance) but the defense is not in the slides. Either justify or symmetrize.

### 5. WAIVE appears to double-count the same first-stage residual
- Severity: **High**
- Evidence: MAIVE adjustment drops `π_i` from the fitted SE (WAIVE slide 12: the residual term is struck through in `SE(Ê)²_adj,i = α̂_0 + α̂_1(1/N_i) + π_i̶`). WAIVE then uses that same residual to compute the downweight: "Residual from MAIVE first stage" (WAIVE slide 20) → `ω_i` (WAIVE slide 21).
- Supporting audits: New in Round 2 (Contribution Skeptic R2 — "One issue neither X nor Y raised" — designated by that stream as the minority report; see below).
- Why it matters: If MAIVE already purges `π_i` from the precision used for weighting, WAIVE's second-stage downweight applies the *same residual information* a second time, now on the observation's influence. The slides do not justify this two-step usage. The justification would need to argue that negative `π_i` predicts *biased point estimates*, not merely biased reported variances — but the slides motivate the residual as "reported SE too small relative to N" (slide 20), which speaks to variance, not bias in the estimate. This is the structural concern: WAIVE may be re-using the same signal as an estimand-level penalty after MAIVE has already neutralized it as a precision-level penalty.

### 6. First-stage instrument-validity assumption is inherited but not stress-tested for WAIVE specifically
- Severity: **Medium**
- Evidence: "MAIVE assumes that sample size is not subject to selection. If meta-analysts suspect that authors systematically choose sample sizes based on expected effect sizes, MAIVE may not offer an advantage over conventional estimators." (MAIVE p. 7).
- Supporting audits: Methodologist R1 #1. (Audit X's R1 #5 raised this but it was downgraded in R2 cross-critique as "boilerplate.")
- Why it matters: WAIVE inherits MAIVE's load-bearing assumption (sample-size exogeneity) and adds a second-stage operation that uses the first-stage residuals. If the assumption fails, WAIVE doesn't just inherit MAIVE's bias — it potentially amplifies it by allocating extra downweights based on noisy residuals. The slides do not address this interaction.

## Points rejected under scrutiny

| Claim | Origin | Reason rejected |
|---|---|---|
| "Practical deployment language implies readiness before evidence supports it" (Contribution Skeptic R1 #5; based on slide 17's `easymeta.org` screenshot) | C R1 #5 | Methodologist R2 judged this a stretch: slide 17 describes software capability, not validation status. Software supporting a proposed estimator is not an overclaim. |
| "Robustness coverage does not address inherited MAIVE fragilities" (Evidence Auditor R1 #5) | E R1 #5 | Double-counts MAIVE's documented limitations as a WAIVE-specific evidence gap. The substantive concern lives in surviving criticism #6. Merged. |
| "Reproducibility details too thin for implementation" (Evidence Auditor R1 #4) | E R1 #4 | Real but not high-severity in current form. The Evidence Auditor's R1 #4 grounded the criticism in slides 12 and 17 about controls, logs, clustering. Synthesis decided this is downstream of surviving criticism #2 (scale-dependence): fix the residual definition and the implementation details follow. Flagged here for transparency; the WAIVE team should still publish pseudocode and exact defaults. |
| "First-stage F threshold of 10 is too low" (Methodologist R1 #4) | M R1 #4 | Cross-critique noted that the criticism quoted material partly outside the audit corpus. MAIVE's own paper already reports a stricter F > 100 subset (MAIVE p. 6). The Methodologist also referenced Anderson-Rubin confidence intervals from outside `manuscript_text.md`; that part of the criticism is `[EXTERNAL]` to the audit corpus. Plausible but overstated as a WAIVE-specific issue. |
| "Empirical validation is not preregistered" (Methodologist R1 #5) | M R1 #5 | The criticism depends on supplementary material (S1-S6) not in the audit corpus. Cannot be verified without it. Moved out of synthesis; flagged in extraction caveats. |
| "The interpretive jump is the main weak point" (Contribution Skeptic R1 minority candidate) | C R1 minority | Not rejected — *promoted to surviving criticism #1 by consensus*. The Contribution Skeptic re-designated a different minority objection in Round 2 (see below). This is recorded here so the original R1 minority candidate is auditable. |

## Minority report

**Designated by the Contribution Skeptic stream in Round 2 (re-designating from R1).** WAIVE may double-count the same first-stage residual: MAIVE drops `π_i` from adjusted precision (WAIVE slide 12), then WAIVE reuses negative `π_i` to downweight observations (WAIVE slides 20-21). This deserves preservation even if the majority focuses on scale-invariance and validation, because it asks whether WAIVE has a coherent estimand-level rationale beyond "more aggressive downweighting looks better." The majority kept this as surviving criticism #5; the Contribution Skeptic wants it on the record as *the* structural objection — if MAIVE already neutralized this signal as precision, what does using it again as observation weight do that MAIVE didn't?

(Note: an earlier version of this memo mis-labeled the negative-residual interpretation as the minority report. It is in fact the consensus surviving criticism #1. Codex's critique of this memo caught the mis-label; this revision restores the actual minority designation from `round2/contribution_skeptic_R2.md`.)

## External considerations

The Methodologist stream (Claude reading the PDFs directly) drew on some material outside the shared `manuscript_text.md` corpus that Codex streams used. Specifically:
- The Anderson-Rubin confidence-interval recommendation in MAIVE (visible in the original MAIVE p. 7 but not transcribed into `manuscript_text.md`).
- The Stock-Yogo weak-instrument literature (background context for criticism #4 above and rejected criticism on F-thresholds).
- General econometric knowledge of two-stage IV inference and generated-regressor variance propagation.

These are tagged `[EXTERNAL]` in spirit. They informed the Methodologist's analysis but could not be verified by the Codex streams from the shared text alone. For the synthesis to be fully audit-trail-clean, future runs should ensure the manuscript text extraction includes everything the Methodologist will rely on, or label Methodologist-only points more visibly.

## Action list (prioritized)

1. **(Addresses #1)** Validate the negative-residual signal against held-out, hand-coded study features. Show that π_i < 0 correlates with markers of p-hacking (significance bunching, post-hoc covariate selection) rather than markers of honest design (panel data, clustered errors, rich controls). Report false-positive rate.
2. **(Addresses #2)** Either derive the exponential tilt from a structural model of selection, or report WAIVE under at least three alternative tilt functions (linear, hard-threshold, robust-loss). Standardize π_i to make weights scale-invariant.
3. **(Addresses #3)** Add a head-to-head MAIVE-vs.-WAIVE ablation panel in the next presentation/paper, using the same simulation grid and empirical sample as the MAIVE Nature Comms paper. **First check if slides 22-26 already do this; if so, this objection partially resolves.**
4. **(Addresses #4)** State explicitly why the downweight is one-sided (incentive argument), or symmetrize.
5. **(Addresses #5 — minority report)** Justify the two-step usage of `π_i`: explain why a residual that MAIVE *removes* from precision should also *reduce influence* in the second stage. The argument needs to be that negative `π_i` predicts biased estimates after MAIVE adjustment, not merely biased reported variances before it.
6. **(Addresses #6)** Run WAIVE under varied first-stage F-statistic conditions and show graceful degradation when sample-size exogeneity is plausibly violated (worked example: experimental literature where authors choose N based on power calculations against an expected effect size).
7. **(Framing)** In the next presentation/paper, label WAIVE as "an experimental sensitivity layer on MAIVE" rather than "a successor estimator" until the ablation in #3 is published.

## Audit trail

- **Session ID:** 20260524-1015-waive-mad-research
- **Mode:** default (Rounds 1 + 2 + synthesis; Round 3 skipped — no trigger)
- **Streams:** 3/3 ran successfully. Methodologist: Claude (Opus 4.7), reads PDFs directly. Evidence Auditor: Codex CLI 0.133.0, reads `input/manuscript_text.md`. Contribution Skeptic: Codex CLI 0.133.0, reads `input/manuscript_text.md`. **Independence caveat:** the two Codex streams are the same model with the same text input — diversity comes from role priors and lane boundaries, not from architectural difference. "Three independent streams" is therefore a stronger claim than the implementation supports; "three role-differentiated streams across two models" is more accurate.
- **Anonymization mapping (revealed for transparency post-synthesis):** in Round 2, Audit X / Audit Y / Audit Z mapped to {Methodologist, Evidence Auditor, Contribution Skeptic} according to which two each stream received. Each stream saw only the other two, never its own R1 output.
- **Total Codex calls:** 4 (2 R1 + 2 R2).
- **Total wall-clock time:** ~30 minutes.
- **Extraction caveats from all streams:**
  - **WAIVE slides 22-26** (results, applications, conclusions, Q&A) were truncated from text extraction. Several criticisms (especially #3 — ablation) might already be partly addressed in those slides; this audit cannot verify. Re-running with the full slides in scope is the first remediation.
  - **MAIVE Supplementary Information (S1-S6)** is referenced repeatedly in MAIVE pp. 5-9 but not in the audit corpus. Methodologist R1 #5 (preregistration) and parts of #4 (weak-instrument threshold derivation) depend on supplementary material.
- **Unverified references:** None flagged as fabricated. The MAIVE DOI, EasyMeta.org web app, and Bartos et al. dataset are real (verified by Methodologist directly from the PDF).
- **Synthesis blinding:** Honest framing — "blinded structured synthesizer," not "fresh judge." The synthesizer (Claude, this session) orchestrated all rounds and has seen the role mapping. Anonymization was applied to the *inputs to synthesis* (claim packets labeled Audit X / Y / Z) but the orchestrator's session memory contains the mapping. For high-stakes use, a user can copy the claim packets and `rubric.md` into a fresh Claude Code window for a truly fresh synthesis.
- **Self-critique pass:** after the first draft of this final memo, Codex was dispatched to critique the entire example (`build_notes/codex_critique_of_example.md`). It caught five real problems with the original synthesis: a fabricated reference (Anderson-Rubin CIs not in the shared corpus), an `External considerations: None` overclaim, a mis-labeled minority report (the negative-residual issue was consensus #1, not minority), a missing reproducibility objection, and overstated independence. This memo is the post-critique revision. The Codex critique is preserved verbatim in `build_notes/` so users can audit what was changed and why.

---

**Note for the WAIVE team.** This memo is the output of an automated MAD-research run executed by the `mad-research` Claude Code skill, used here as a worked example. The criticisms above are honest grounded objections produced by the protocol on a manuscript by people including the orchestrator's collaborators. We include this example deliberately: a usable adversarial audit tool must produce real critiques of friendly work, not just of hostile work.
