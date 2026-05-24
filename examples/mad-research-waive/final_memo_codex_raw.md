# MAD-research final memo — WAIVE proposal

## Verdict
Identification credibility: WAIVE is plausible as a MAIVE extension, but the extracted record does not yet defend the added identifying claim that negative MAIVE residuals identify estimates that should be downweighted. Statistical validity: the weight is stated clearly, but the residual scale, functional-form justification, and propagation of first-stage uncertainty into second-stage inference are not shown. Robustness coverage: the available text does not show the needed MAIVE-vs-WAIVE ablations, alternative weight functions, or failure-mode checks; the caveat is that WAIVE slides 22-26 are truncated. Numerical and citation accuracy: the main surviving claims are source-verified on WAIVE slides 11-21 and MAIVE p. 7, while several audit claims about pages beyond the extraction, preregistration, and external datasets failed verification. Transparency of limitations: MAIVE’s sample-size-selection warning is explicit in the baseline paper, but the WAIVE slides do not visibly restate what that limitation means once the same first-stage residual also drives weights. Severity-weighted issue count: several high-severity issues survive, but if the authors validate the residual classifier, normalize and justify the weight, and document direct gains over MAIVE, WAIVE could become a credible sensitivity extension rather than a top-5-ready successor estimator.

## Top surviving criticisms (5-7, ordered by severity)

- **1. Negative residuals are treated as a classifier before they are validated**
  - Severity: High
  - Evidence: "Negative π_i → reported SE too small relative to N → possibly p-hacking." (WAIVE slide 20); "Penalize only negative residuals (π_i < 0) — downweight spuriously precise estimates." (WAIVE slide 21)
  - Why it matters: The slide’s own wording is tentative, but the estimator applies a mechanical penalty. WAIVE needs evidence that negative residuals predict contaminated estimates, not just smaller-than-fitted reported variances.
  - Trajectory: Origin Audit X, Audit Y, Audit Z / Challenged by none / Status Survived / Reason The operational rule is stronger than the manuscript’s stated diagnostic evidence.
  - Supporting audits: X, Y, Z

- **2. The exponential weight is underjustified and underspecified**
  - Severity: High
  - Evidence: "ω_i = exp[ −max(0, −π_i) ]" (WAIVE slide 21); "In practice it's better to use logs in the first stage." (WAIVE slide 12)
  - Why it matters: The slides do not say why this exact exponential form is preferred, nor do they define which residual scale enters the exponent. Smoothness and boundedness are properties, not a derivation.
  - Trajectory: Origin Audit X, Audit Y, Audit Z / Challenged by none / Status Reformulated / Reason The verified core is lack of derivation and scale specification; broader rescaling claims are kept under external considerations.
  - Supporting audits: X, Y, Z

- **3. Incremental validity over MAIVE is not shown in the extracted record**
  - Severity: High
  - Evidence: "WAIVE first stage — same as MAIVE." (WAIVE slide 20); "Then run PEESE of Ê_i on SE(Ê)²_adj,i with weights ω_i." (WAIVE slide 21)
  - Why it matters: The novelty is the second-stage residual weight. A successor claim requires direct MAIVE-vs-WAIVE comparisons under the same simulations and applications, including cases where WAIVE does not improve on MAIVE.
  - Trajectory: Origin Audit X, Audit Y, Audit Z / Challenged by X, Z / Status Survived / Reason The pushback is only that truncated slides may contain missing evidence, not that the extracted text contains it.
  - Supporting audits: X, Y, Z

- **4. WAIVE inherits MAIVE’s sample-size exclusion restriction without visibly re-surfacing it**
  - Severity: Medium
  - Evidence: "WAIVE first stage — same as MAIVE." (WAIVE slide 20); "MAIVE assumes that sample size is not subject to selection." (MAIVE p. 7)
  - Why it matters: If sample size is endogenous to expected effects or study design choices, the first-stage residual can be contaminated before WAIVE turns it into a weight. The issue is inherited, but WAIVE’s extra residual-based action makes the failure mode more consequential.
  - Trajectory: Origin Audit Y / Challenged by X, Y / Status Downgraded from High → Medium / Reason MAIVE acknowledges the limitation, so the WAIVE-specific defect is failure to carry the warning into the new weighting step.
  - Supporting audits: Y, Z

- **5. Implementation defaults are too thin for reproducible use**
  - Severity: Medium
  - Evidence: "Can add controls. Plug MAIVE-adjusted SEs into other estimators. In practice it's better to use logs in the first stage." (WAIVE slide 12)
  - Why it matters: Controls, log transformation, clustering, multilevel dependence, missing sample sizes, and multiple estimates per study can all change residuals and thus weights. A user cannot reproduce WAIVE from the slides without hidden defaults.
  - Trajectory: Origin Audit Z / Challenged by none / Status Survived / Reason The manuscript advertises flexible implementation choices without specifying the algorithmic defaults.
  - Supporting audits: single audit

- **6. First-stage residual uncertainty is not shown in second-stage inference**
  - Severity: Medium
  - Evidence: "Residual from MAIVE first stage: SE(Ê_i)² = α_0 + α_1 (1/N_i) + π_i — hacking if negative?" (WAIVE slide 20); "Then run PEESE of Ê_i on SE(Ê)²_adj,i with weights ω_i." (WAIVE slide 21)
  - Why it matters: π_i is estimated, then transformed into a second-stage weight. The extracted slides do not show how uncertainty in that generated residual affects WAIVE standard errors or confidence intervals.
  - Trajectory: Origin Audit Y / Challenged by none / Status Survived / Reason The source defines a generated weight but gives no inference adjustment in the extracted text.
  - Supporting audits: single audit

## Minority report
Audit X’s designated minority objection is preserved: WAIVE may double-use the first-stage residual by dropping it from the adjusted standard error and then using its negative values to downweight observations. Evidence: "Adjustment: 'SE(Ê_i)²_adj,i = α̂_0 + α̂_1 (1/N_i) + π_i' — but with the π_i term struck through (i.e., dropped). The MAIVE-adjusted SE uses only the fitted part." (WAIVE slide 12); "Then run PEESE of Ê_i on SE(Ê)²_adj,i with weights ω_i." (WAIVE slide 21). It deserves preservation because it asks for an estimand-level rationale, not just a better simulation plot: if MAIVE already removes the suspicious residual from the precision regressor, WAIVE must explain why the same residual should also reduce the observation’s influence. The surviving counter-argument is that residuals might contain information about biased point estimates, not only reported variances, but the extracted slides do not demonstrate that link.

## Points rejected under scrutiny

| Claim | Origin | Reason rejected |
|---|---|---|
| The empirical validation is not preregistered. | Audit Y | Locator failed verification: the cited MAIVE p. 9-10 text is not present in `manuscript_text.md`, and absence of preregistration is not stated in the extracted source. |
| The first-stage F threshold of 10 is too low as a demonstrated WAIVE flaw. | Audit Y; discussed by X and Z | Rejected as stated: the source supports a caution below 10 on MAIVE p. 7, not a verified failure of the threshold. The inherited-instrument concern is reformulated as criticism 4. |
| Practical deployment language implies methodological readiness. | Audit X | Rejected as overread: "Supports MAIVE, WAIVE, multilevel, clustering." (WAIVE slide 17) verifies software capability, not a recommendation for routine use. |
| The exponential downweight was tuned to a specific simulation. | Audit Y | No locator for tuning. The source supports lack of derivation and sensitivity concerns, so the point is reformulated as criticism 2. |
| Kvarven/preregistered replication evidence anchors WAIVE validation. | Audit Y strength/context | Locator failed verification: the quoted Kvarven material is not present in the extracted manuscript text. |
| Package behavior, Anderson-Rubin implementation details, and external software defaults should enter the main critique. | Audit Y, Audit Z | Points rejected — no locator: these claims are not directly quoted from `manuscript_text.md`. |

## External considerations
Audit Z tagged as [EXTERNAL] the methodological implication that if π_i is measured in squared effect-size units, rescaling outcomes or changing effect metrics can change the magnitude of π_i and therefore the exponential weight unless the residual is standardized or made dimensionless. This is not used as manuscript evidence in the main criticism list; it motivates the requested normalization and invariance checks under criticism 2. No other stream used external literature as a basis for a surviving main criticism.

## Action list (prioritized)

1. Validate the negative-residual classifier against hand-coded study features, no-hacking heterogeneous-design simulations, and significance-bunching diagnostics. Tied to criticism 1.
2. Define the WAIVE weight algorithm on a fixed residual scale, preferably dimensionless, and report alternative monotone penalties. Tied to criticism 2.
3. Add direct MAIVE-PEESE versus WAIVE-PEESE ablations on identical simulation grids and empirical samples, including cases where WAIVE loses. Tied to criticism 3.
4. Restate MAIVE’s sample-size-selection assumption inside the WAIVE proposal and add diagnostics for sample-size endogeneity and first-stage fragility. Tied to criticism 4.
5. Publish pseudocode and replication defaults for logs, controls, clustering, multilevel dependence, missing N, and multiple estimates per study. Tied to criticism 5.
6. Propagate first-stage residual uncertainty into WAIVE inference with a bootstrap, stacked estimator, or explicitly justified sandwich procedure. Tied to criticism 6.

## Audit trail
- Session ID: 20260524-1015-waive-mad-research
- Rounds run: Round 1, Round 2, synthesis (Round 3 skipped — no trigger)
- Streams: 3/3
- Synthesis: fresh codex exec (this call)
- Extraction caveats from all streams: WAIVE slides 22-26 were truncated in text extraction; final results, applications, conclusions, and Q&A could not be audited; slides are text-light, so several figure claims depend on extracted descriptions rather than plotted values; exact plotted values were not recoverable; missing slides may contain ablations or empirical comparisons that alter the contribution assessment.
- Unverified references from all streams: MAIVE Supplementary Information S1-S6; Supplementary Tables S3-S5; EasyMeta.org; MAIVE DOI screenshot; Bartos et al. dataset details beyond the extraction; Kvarven et al. and preregistered replication claims; MAIVE pp. 8-10 material cited by audits but absent from `manuscript_text.md`; preregistration status; package behavior and external software defaults.