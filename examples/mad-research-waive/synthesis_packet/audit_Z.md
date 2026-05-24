# Round 1 — Audit Z

## Top 5 criticisms
1.
- Claim: WAIVE’s incremental contribution over MAIVE is not yet evidenced.
- Severity: High
- Evidence: "WAIVE first stage — same as MAIVE." (WAIVE slide 20); "Then run PEESE of Ê_i on SE(Ê)²_adj,i with weights ω_i." (WAIVE slide 21)
- Why it matters: The proposal’s novelty is the added second-stage residual weight, but the extracted slides do not show a clean MAIVE-versus-WAIVE ablation. A top referee will ask whether WAIVE improves performance beyond MAIVE’s published first-stage adjustment.
- Concrete fix: Add tables and figures comparing unadjusted PEESE, MAIVE-PEESE, and WAIVE-PEESE under identical simulations and empirical meta-analyses. Report cases where WAIVE loses to MAIVE.
- Devil's-advocate check: Slide 19 visually says WAIVE further downweights relative to MAIVE, and slides 22-26 may contain results, but those slides are truncated in the extraction. In the available evidence, the incremental performance claim is not documented.

2.
- Claim: The negative-residual signal is treated as evidence of p-hacking without validation.
- Severity: High
- Evidence: "Residual from MAIVE first stage: SE(Ê_i)² = α_0 + α_1 (1/N_i) + π_i — hacking if negative?" (WAIVE slide 20); "Negative π_i → reported SE too small relative to N → possibly p-hacking." (WAIVE slide 20)
- Why it matters: The slide’s own language is tentative, but the estimator makes the penalty automatic for every negative residual. Negative residuals can also arise from design heterogeneity, legitimate precision-enhancing methods, measurement error, clustering choices, or outcome scaling.
- Concrete fix: Validate the residual classifier against hand-coded study features, placebo outcomes, no-hacking simulations with heterogeneous designs, and known high-quality replications.
- Devil's-advocate check: MAIVE states that the residual term can soak up "spurious elements" (MAIVE p. 4), and WAIVE says "Can add controls" (WAIVE slide 12). That helps, but it does not validate the sign of the residual as a reliable p-hacking indicator.

3.
- Claim: The exponential weight is scale-dependent and underspecified.
- Severity: High
- Evidence: "ω_i = exp[ −max(0, −π_i) ]" (WAIVE slide 21); "In practice it's better to use logs in the first stage." (WAIVE slide 12)
- Why it matters: In the displayed formula, π_i is a residual from a variance regression, so its units depend on the effect-size scale and whether SE, SE squared, or logs are used. Without normalization, the same evidence can receive different weights after rescaling.
- Concrete fix: Define the algorithm using a dimensionless residual, such as a standardized or log-variance residual, and pre-specify any winsorization or calibration. Include examples showing how weights change under common rescalings.
- Devil's-advocate check: The log-first-stage note may be intended to solve this problem. But the core formula shown to the audience is not rewritten for logs, and the implementation default is not stated.

4.
- Claim: Reproducibility details are too thin for implementation.
- Severity: Medium
- Evidence: "Can add controls. Plug MAIVE-adjusted SEs into other estimators. In practice it's better to use logs in the first stage." (WAIVE slide 12); "Supports MAIVE, WAIVE, multilevel, clustering." (WAIVE slide 17)
- Why it matters: These bullets leave open which controls are allowed, when logs are mandatory, how multilevel dependence and clustering enter the first stage, and how sample restrictions are handled. Small choices can change residuals, weights, and the final estimate.
- Concrete fix: Provide pseudocode and a replication script with defaults: inputs, transformations, first-stage controls, clustering level, treatment of missing N, multiple estimates per study, and exact weighted second-stage command.
- Devil's-advocate check: Slide 16 advertises "Web app: EasyMeta.org" (WAIVE slide 16), which may implement defaults. The slides still do not expose those defaults or make the procedure independently reproducible.

5.
- Claim: Robustness coverage does not yet address inherited MAIVE fragilities.
- Severity: Medium
- Evidence: "Caution is warranted if the F-statistic from the first-stage regression of reported variance on inverse sample size falls below 10." (MAIVE p. 7); "MAIVE assumes that sample size is not subject to selection." (MAIVE p. 7)
- Why it matters: WAIVE inherits MAIVE’s first stage and then uses its residuals to allocate penalties. If the first stage is weak, misspecified, or contaminated by sample-size selection, WAIVE can convert first-stage noise into second-stage downweighting.
- Concrete fix: Add robustness grids varying first-stage strength, meta-analysis size, sample-size selection, heteroskedasticity, clustered designs, and legitimate low-SE studies. Report diagnostics for when WAIVE should not be used.
- Devil's-advocate check: MAIVE’s published paper already acknowledges these limitations and recommends caution. WAIVE, however, adds an extra residual-based action, so it needs its own failure-mode evidence.

## Top 2 strengths
1.
- Claim: WAIVE targets a real evidence problem already documented in the MAIVE baseline.
- Evidence: "All meta estimators biased upwards" (MAIVE p. 5); "Aggressive p-hacking: PEESE in trouble." (WAIVE slide 7)
- Why it matters: The proposal is aimed at a concrete failure mode rather than a purely cosmetic extension.

2.
- Claim: The core estimator is at least stated compactly enough to audit.
- Evidence: "Penalize only negative residuals (π_i < 0) — downweight spuriously precise estimates." (WAIVE slide 21); "Weight ∈ (0, 1], smooth and proportional decay." (WAIVE slide 21)
- Why it matters: A clear weight rule makes the proposal testable once scaling and implementation defaults are fixed.

## Biggest evidence-quality blind spot
The easy-to-miss weakness is that WAIVE’s headline visual contribution, more aggressive downweighting of red suspicious points, can look like validation when it may only be the mechanical consequence of the chosen penalty. Readers may trust the intuition that low-SE positive outliers are hacked, but the extracted evidence does not establish that negative MAIVE residuals reliably identify hacking rather than legitimate design differences. The weight also appears sensitive to the residual scale, so an apparently strong correction could depend on a transformation choice. Because the results slides are truncated, the available audit record cannot verify whether WAIVE wins against MAIVE in systematic tests.

## Bottom line
WAIVE is a plausible extension, but the evidence package in the extracted slides is not yet strong enough to support a top-5 contribution claim over MAIVE. The proposal needs to show that residual weighting adds measurable value after MAIVE has already replaced reported variance with the sample-size-implied component. The most important fixes are ablation against MAIVE, validation of the negative-residual signal, scale-invariant weights, and a reproducible implementation. Until then, WAIVE reads more like a promising penalty heuristic than a fully audited estimator.

## Extraction caveats
WAIVE slides 22-26, described as results/applications/conclusions/Q&A, were truncated from the text extraction and not used in this audit. Several WAIVE figures are text-light funnel plots, so I could not extract exact plotted values beyond the captions and bullets in `manuscript_text.md`. I did not verify the PDFs because the instructions said to rely on the text file and warned that PDFs may not be reliably readable.

## Unverified references
The MAIVE DOI screenshot, EasyMeta.org web-app claim, and Bartos et al. dataset reference were not externally verified in this stream. I do not flag any of them as fabricated based on the provided text alone.


---

# Round 2 — Cross-critique — Audit Z

## Two strongest peer arguments
1.
- From: Audit X
- Claim: "Negative first-stage residuals are not a unique signal of p-hacking"
- Evidence: "Negative pi_i -> reported SE too small relative to N -> possibly p-hacking." (WAIVE slide 20)
- Why it survives: The slide's own word "possibly" (WAIVE slide 20) is weaker than WAIVE's operational rule, which penalizes all negative residuals in the second stage.

2.
- From: Audit Y
- Claim: "WAIVE is framed as a new estimator, but the extracted evidence makes it look like an incremental weighting layer on MAIVE."
- Evidence: "WAIVE first stage -- same as MAIVE." (WAIVE slide 20)
- Why it survives: The documented novelty is the residual-based weight, not a new first-stage identification strategy or distinct estimand.

## Two weakest or overstated peer arguments
1.
- From: Audit X
- Claim: "The first-stage F-statistic threshold of 10 is too low, given Stock-Yogo on weak instruments"
- Why it fails: The peer audit quotes material at "MAIVE p. 8" that is not present in the extracted manuscript, so the quoted support cannot be verified here. The criticism may be methodologically plausible, but under the grounding rules it is overstated because the available source only supports caution below 10 and reports a stricter empirical subset, not a demonstrated failure of the threshold.
- Counter-quote (if any): "Caution is warranted if the F-statistic from the first-stage regression of reported variance on inverse sample size falls below 10." (MAIVE p. 7)

2.
- From: Audit Y
- Claim: "Practical deployment language may imply readiness before the extracted evidence supports it."
- Why it fails: This overreads the EasyMeta/software screenshot. "Supports MAIVE, WAIVE, multilevel, clustering" (WAIVE slide 17) shows implementation availability, but the extracted text does not show recommendation language, routine-use language, or conclusion language; slides 22-26 are truncated and could alter that judgment.
- Counter-quote (if any): None in the extracted WAIVE text.

## One issue neither X nor Y raised
- Claim: WAIVE's exponential weight appears scale-dependent because it exponentiates the raw first-stage residual.
- Severity: High
- Evidence: "Residual from MAIVE first stage: SE(E_i)^2 = alpha_0 + alpha_1 (1/N_i) + pi_i." (WAIVE slide 20); "omega_i = exp[ -max(0, -pi_i) ]." (WAIVE slide 21)
- Why it matters: [EXTERNAL] If pi_i is measured in squared effect-size units, then rescaling the dependent variable, changing from raw to standardized effects, or using a different effect metric changes the magnitude of pi_i and therefore the penalty. That makes WAIVE weights partly a function of measurement units unless pi_i is standardized, studentized, or otherwise made dimensionless before entering the exponential.
- Concrete fix: Define the weight using a relative or standardized residual, such as pi_i divided by the fitted variance, the residual standard deviation, or a meta-analysis-specific scale parameter; then show that WAIVE estimates are invariant, or nearly invariant, to effect-size rescaling.

## Top 3 surviving criticisms after Round 2
1. Original source: X/Y. Short claim: Negative first-stage residuals are an ambiguous diagnostic, not direct evidence of p-hacking. Evidence locator: WAIVE slide 20. Severity: High. Why it survives: The manuscript says "possibly p-hacking" (WAIVE slide 20), while the estimator treats the sign of the residual as sufficient to trigger a penalty.

2. Original source: X/Y, strengthened here. Short claim: The exponential tilt needs a principled derivation, sensitivity analysis, and scale normalization. Evidence locator: WAIVE slide 21. Severity: High. Why it survives: Smoothness and boundedness do not justify the exact functional form, and the raw-residual exponent raises an additional invariance problem.

3. Original source: Y. Short claim: WAIVE needs to prove incremental validity over MAIVE, not just over PEESE or conventional funnel corrections. Evidence locator: WAIVE slides 11 and 20. Severity: High. Why it survives: The slides identify MAIVE as the "Natural solution" (WAIVE slide 11) and then state that WAIVE's first stage is the same as MAIVE (WAIVE slide 20), so the contribution burden rests entirely on the second-stage weight.

## Devil's-advocate check on emerging consensus
X, Y, and my own Round 1 converge on the view that WAIVE is a plausible MAIVE extension whose residual penalty is not yet sufficiently validated. That agreement is grounded where it points to slides 20-21, because the slides explicitly move from a tentative residual interpretation to an automatic penalty. The possible herd-driven element is the incremental-validation criticism: because WAIVE slides 22-26 are truncated, the available record may understate evidence that appears later in the deck.

## Extraction caveats and unverified references
The WAIVE extraction is incomplete: slides 22-26, covering final results, applications, conclusions, and Q&A, are truncated. I therefore cannot verify any quantitative WAIVE result, applied comparison, or conclusion language that appears only there.

The slides are text-light, so several WAIVE claims are mediated through extracted descriptions of figures rather than full underlying data. I used only the provided manuscript extraction and the two anonymized peer audits; no outside sources were consulted. [EXTERNAL] marks only general methodological reasoning, not an outside reference.

Several peer-audit claims cite material not present in `input/manuscript_text.md`, including claims about Kvarven et al., pages beyond the extracted locators, supplementary materials, preregistration, and package behavior. Those references remain unverified under the current evidence rules and should not enter synthesis unless the missing source material is supplied.
