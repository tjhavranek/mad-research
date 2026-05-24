# Round 1 — Evidence Auditor

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
