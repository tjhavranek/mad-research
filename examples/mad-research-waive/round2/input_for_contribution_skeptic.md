# Round 1 — Audit X

## Top 5 criticisms

### 1. The exclusion restriction for sample size as an instrument is asserted, not defended
- Severity: High
- Evidence: "MAIVE assumes that sample size is not subject to selection. If meta-analysts suspect that authors systematically choose sample sizes based on expected effect sizes, MAIVE may not offer an advantage over conventional estimators." (MAIVE p. 7)
- Why it matters: This is the load-bearing assumption for the entire MAIVE→WAIVE family. The authors acknowledge the threat in one sentence and move on. In observational empirical work, researchers very plausibly *do* choose sample sizes endogenously: power calculations are written against expected effect sizes; convenience samples are kept or discarded based on whether they "work." If sample size is correlated with the unobserved component of precision (π_i in WAIVE slide 20), the first-stage instrument is correlated with the structural error and the whole apparatus is biased — not in a small way, but in the direction MAIVE/WAIVE are trying to correct for. The published MAIVE paper makes this disclosure; the WAIVE slides do not surface it at all (slides 11–14, 19–21 discuss the first stage without flagging the assumption). WAIVE inherits the assumption and adds new operations on top of it without defending it.
- Concrete fix: Provide a falsification battery for the exclusion restriction. Specifically: (a) check whether reported sample size correlates with reported effect size in the simulation; (b) compare MAIVE/WAIVE estimates to estimates that use *effective* sample size (clusters, degrees of freedom) where available; (c) provide a worked example where N is plausibly chosen ex post and show WAIVE fails gracefully or noisily, not silently.
- Devil's-advocate check: The author's strongest defense is "sample size is the closest we have to an exogenous instrument; the alternative is to abandon meta-analysis under p-hacking entirely." This is a real consideration. But the defense supports using MAIVE/WAIVE with humility, not without acknowledging the assumption. The criticism survives.

### 2. WAIVE's exponential downweight is tuned to a specific simulation, not derived
- Severity: High
- Evidence: "Exponential tilt: ω_i = exp[ −max(0, −π_i) ] ... Weight ∈ (0, 1], smooth and proportional decay." (WAIVE slide 21)
- Why it matters: The functional form `exp(−max(0, −π_i))` has no stated derivation. It is not implied by any model of the p-hacking process; it is a curve that produces the simulation results shown. There is no comparison to alternative tilts: `exp(−2·max(0,−π))`, `1/(1+max(0,−π))`, a hard-threshold rule, or simply dropping observations with π < 0. Without a justification, the reader cannot tell whether WAIVE is doing real work or whether the exponential happens to interact favorably with the specific p-hacking simulation in MAIVE Figure 2. This is the kind of choice that risks tuning to the calibration sample.
- Concrete fix: Either (a) derive ω_i from a structural model of selection (e.g., the probability that a given π_i represents hacking given a prior distribution), or (b) report WAIVE under three or more alternative tilt functions and show the bias-variance trade-off across them. Without this the contribution claim is "we found a curve that works in one simulation."
- Devil's-advocate check: One could argue the smooth exponential is just convenient — Occam's razor — and the results would be similar under any monotone decay. That defense should be testable; running it would either confirm robustness or expose tuning.

### 3. Negative first-stage residuals are not a unique signal of p-hacking
- Severity: High
- Evidence: "Negative π_i → reported SE too small relative to N → possibly p-hacking." (WAIVE slide 20)
- Why it matters: π_i is a residual from `SE(Ê)² = α₀ + α₁(1/N) + π`. A negative residual means the reported variance is smaller than the design-implied benchmark. WAIVE interprets this as a fingerprint of selective SE manipulation. But there are at least four other explanations: (i) heteroskedasticity — some primary studies genuinely have lower-variance error terms; (ii) covariate selection that *legitimately* increases R² and lowers SE; (iii) clustering structure (a study with effective clusters > N gives smaller SE; conversely, fewer clusters give larger SE); (iv) measurement of the dependent variable on a different scale (standardized vs. raw). Penalizing every negative residual as "possibly p-hacking" mis-classifies honest studies as suspicious. The published MAIVE paper acknowledges that misspecification can cause both up- and down-biased SE (MAIVE p. 3); WAIVE's downweight applies only to one direction without explaining why the symmetric concern does not apply.
- Concrete fix: WAIVE should report what fraction of estimates in real meta-analyses have π_i < 0 and check whether that fraction is correlated with markers of honest design heterogeneity (panel data, clustered designs, controls) rather than with markers of p-hacking (significance bunching at |t|=1.96). If it correlates with honest design features, WAIVE penalizes the wrong things.
- Devil's-advocate check: The author's strongest defense is that the downweight is *smooth*: a small negative residual gets a small penalty, only large negatives get large penalties. That's fair. But it doesn't address mis-classification at the tails: a genuinely well-identified study with strong controls and tight design can have π_i very negative for honest reasons and get heavily downweighted. The criticism stands.

### 4. The first-stage F-statistic threshold of 10 is too low, given Stock-Yogo on weak instruments
- Severity: Medium
- Evidence: "[We require] that the F-statistic from the first-stage regression, regressing reported variance on inverse sample size, is larger than 100." (MAIVE p. 8 — note this is the *empirical application* threshold, the more lenient default elsewhere is F > 10.)
- Why it matters: The Stock-Yogo critical values (≈10) for weak-instrument inference were derived for single-equation IV with one endogenous regressor under specific assumptions about the structural error. MAIVE's IV setting is different (meta-analytic, two-stage on aggregates), and the appropriate weak-instrument bound is not obviously 10. The Anderson-Rubin alternative the authors recommend in the maive R package is a partial fix but does not generalize the F-stat threshold; users with F between 10 and 100 may believe they have a strong instrument when in fact they don't.
- Concrete fix: Derive or simulate the appropriate weak-instrument threshold for the MAIVE setting, or document explicitly that F > 10 is provisional and recommend F > 100 as the practical default (which is what the MAIVE application section already does, but not the headline guidance).
- Devil's-advocate check: The Anderson-Rubin CI is robust to weak instruments by design, so users running maive::maive() with AR get valid inference even at F < 10 — just very wide. The criticism is about the headline guidance being too permissive, not about the toolkit being broken.

### 5. The empirical validation is not preregistered
- Severity: Medium
- Evidence: "We restrict our analysis to (1) meta-analyses with at least 30 estimates (M ≥ 30), (2) estimates based on at least 10 observations, (3) estimates with available sample sizes. Doing so reduces the number of usable meta-analyses to 348. We further censor the data at the 1% and 99% percentiles to limit the influence of extreme outliers. For each of the 348 meta-analyses, we apply both PET-PEESE and its MAIVE version. In 38 meta-analyses, a negative slope estimate appears in the first-stage regression; these meta-analyses are also discarded, resulting in a final set of 310 meta-analyses." (MAIVE p. 9-10)
- Why it matters: Each filtering step (M ≥ 30, ≥ 10 obs per estimate, 1%/99% censoring, drop negative first-stage slopes) is reasonable individually but together create researcher degrees of freedom. The 38 meta-analyses dropped for negative first-stage slopes are exactly the cases where MAIVE's mechanism does not apply — by construction. The remaining sample is selected on instrument validity, which is the property MAIVE is supposed to assume. The headline result "MAIVE reduces PET-PEESE estimates in 70-77% of meta-analyses" is conditional on the surviving sample. The MAIVE paper does not state whether the analysis was preregistered, and the supplement (which I cannot inspect from the manuscript_text) would be the place to check.
- Concrete fix: Report the headline percentages on the unrestricted sample with negative-F cases included (perhaps coded as "MAIVE undefined" or coded against a baseline). State preregistration status explicitly. Sensitivity to each filter step.
- Devil's-advocate check: The defense is that some of these filters are necessary to run MAIVE at all — you can't compute a first-stage F on M=5. That's true, and the M ≥ 30 filter is publicly defensible. The negative-first-stage-slope filter is harder to defend without seeing the sensitivity.

## Top 2 strengths

### 1. The diagnostic separation of "selection on estimates" vs. "selection on standard errors" is methodologically novel and useful
- Evidence: "Two flavors of selection and repercussions for meta-analysis. ... (a) Selection on estimates ... (b) Selection on standard errors. ... A realistic scenario of p-hacking combines both types of selection, so the p-hacked estimates move not strictly east or north (as in the figure) but northeast." (MAIVE p. 2, Figure 1)
- Why it matters: The literature on publication bias has historically treated bias as a single channel (selection on significance through effect sizes). Separating SE-selection out as an analytically distinct mechanism with different implications for funnel-based corrections is a real conceptual contribution. It motivates why MAIVE/WAIVE add value over PEESE-style corrections and is the kind of clean insight that should travel beyond economics.

### 2. The empirical-replication anchor (Kvarven et al. dataset) is the right kind of external validation
- Evidence: "Our strategy is based on Kvarven et al., who compare meta-analysis results to later preregistered multiple-laboratory replications of the same effect. ... Kvarven et al. find that common meta-analysis techniques, including those that explicitly correct for publication bias, yield estimates substantially larger than those of replications." (MAIVE p. 7)
- Why it matters: Calibrating bias-correction methods against preregistered replications is the gold standard for external validity in this literature. Simulations alone always risk circularity. The fact that MAIVE moves estimates closer to replication values in 6 of 8 cases (MAIVE p. 9) is a real empirical anchor that no purely simulation-based method can claim. WAIVE inherits the MAIVE empirical strategy and stands or falls on whether its additional downweight further improves replication agreement — which the slides do not yet report.

## Biggest methodological blind spot

WAIVE's downweight applies only at the second stage, after the first-stage MAIVE-instrumented standard errors are computed. But the first-stage residual π_i, which drives the WAIVE downweight, is itself estimated with noise — both sampling noise (small meta-analyses produce noisy first-stage residuals) and specification noise (the first stage assumes the SE² = α₀ + α₁(1/N) form is correct). WAIVE treats π_i as a known quantity, but in practice it is a generated regressor, and the downstream weights ω_i inherit that noise. The downstream inference (confidence intervals on the WAIVE-PEESE point estimate) needs to propagate first-stage uncertainty into the weights, not just into the instrumented variance. The slides do not address this. A typical reader will see "WAIVE downweights spurious-looking estimates" and miss that the "spurious-looking" classification is itself uncertain.

## Bottom line

WAIVE is a plausible incremental extension of MAIVE that adds an additional layer of downweighting based on a quantity (negative first-stage residual) interpreted as a p-hacking fingerprint. The exponential downweight is unmotivated, the interpretation of negative residuals as p-hacking is not unique, and the validation depends on the same MAIVE simulation calibration that the downweight curve appears to have been chosen against. Until WAIVE is validated against preregistered replications independently of the MAIVE calibration, the contribution claim should be stated as "an additional sensitivity layer for MAIVE in high-p-hacking environments" rather than as a successor estimator.

## Extraction caveats

The final ~5 slides of the WAIVE presentation (slides 22-26) were truncated in text extraction and likely contain results comparing WAIVE to MAIVE on real or simulated data. My audit cannot evaluate any quantitative claim WAIVE makes that lives only in those slides. If the audited material includes those slides, I would need to re-read them before standing behind this assessment.

## Unverified references

The MAIVE Supplementary Information (S1-S6) and Supplementary Tables (S3-S5) are referenced repeatedly in MAIVE pp. 5-9 but not included in the audit material. Several methodological claims (the "key assumption broken" condition, the "implied relative degree of SE-selection") depend on supplementary content I could not verify.

---
# Round 1 — Audit Y

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
