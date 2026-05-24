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
- Claim: WAIVE is framed as a new estimator, but the extracted evidence makes it look like an incremental weighting layer on MAIVE.
- Severity: High
- Evidence: [MANUSCRIPT] "WAIVE first stage — same as MAIVE." (WAIVE slide 20)
- Why it matters: If the first stage is explicitly the same as MAIVE, the contribution burden shifts to showing that the second-stage weighting rule adds something conceptually and empirically important.
- Concrete fix: State WAIVE as an extension of MAIVE unless the paper can show a distinct estimand, new identifying content, or material gains. Add a table separating inherited machinery from genuinely new elements.
- Devil's-advocate check: This is not merely a taste disagreement about naming. It is a failure of the claim's own evidentiary standard if "successor" is implied while the documented novelty is mainly an extra weight applied after the MAIVE first stage.

2.
- Claim: The residual interpretation moves from tentative signal to operational label too quickly.
- Severity: High
- Evidence: [MANUSCRIPT] "Negative π_i → reported SE too small relative to N → possibly p-hacking." (WAIVE slide 20)
- Why it matters: "Possibly p-hacking" is a weak evidentiary basis for treating all negative residuals as suspicious enough to penalize. As a contribution claim, WAIVE needs to show that the residual is a reliable diagnostic, not just a narratively plausible proxy.
- Concrete fix: Reframe the residual as a risk score rather than evidence of hacking, and document false-positive cases such as legitimate design efficiency or measurement differences. Report sensitivity to alternative residual interpretations.
- Devil's-advocate check: The criticism is not that negative residuals can never be informative. It is that the proposed method's evidentiary standard is stronger than the slide's own language supports.

3.
- Claim: The key weighting rule is presented as smooth and natural, but the deck does not show why this functional form is principled.
- Severity: Medium
- Evidence: [MANUSCRIPT] "Weight ∈ (0, 1], smooth and proportional decay." (WAIVE slide 21)
- Why it matters: Smoothness alone is not a methodological contribution. Without a principled argument, the exponential tilt risks looking tuned to downweight observations already suspected to be problematic.
- Concrete fix: Justify the exponential form against alternatives such as trimming, linear downweighting, symmetric penalties, or robust loss functions. If the choice is pragmatic, say so and report robustness.
- Devil's-advocate check: This is partly a disagreement about standards for methodological novelty, but it becomes a failure of WAIVE's own standard if the method is sold as an estimator rather than as a heuristic sensitivity analysis.

4.
- Claim: The motivation establishes that PEESE can fail, not that MAIVE fails enough to need WAIVE.
- Severity: High
- Evidence: [MANUSCRIPT] "Natural solution: 1/N instrumenting SE(Ê_i)² → MAIVE." (WAIVE slide 11)
- Why it matters: WAIVE's predecessor is MAIVE, which the slides present as the natural solution. A credible successor claim needs direct evidence that MAIVE leaves an important residual problem that WAIVE fixes.
- Concrete fix: Put MAIVE, WAIVE, and conventional estimators in the same validation panels and foreground the incremental gain of WAIVE over MAIVE. The abstract/conclusion should say "incremental robustness over MAIVE" unless the marginal evidence is large and stable across settings.
- Devil's-advocate check: This is a failure of the contribution claim's evidentiary standard. The slides motivate the problem, but the extracted text does not prove that MAIVE is insufficient.

5.
- Claim: Practical deployment language may imply readiness before the extracted evidence supports it.
- Severity: Medium
- Evidence: [MANUSCRIPT] "Supports MAIVE, WAIVE, multilevel, clustering." (WAIVE slide 17)
- Why it matters: Tool support can turn a methodological proposal into a practical recommendation. Presenting WAIVE alongside mature features risks overstating its validation status.
- Concrete fix: Label WAIVE as experimental until the results are documented and benchmarked against MAIVE. Add warnings about residual interpretation and sensitivity to the weight rule.
- Devil's-advocate check: This is not an objection to releasing software. It is an overclaim concern: the implementation signal should not outrun the evidentiary support for routine applied use.

## Top 2 strengths

1.
- Claim: WAIVE identifies a real gap in conventional funnel-plot corrections under spurious precision.
- Severity: High
- Evidence: [MANUSCRIPT] "Aggressive p-hacking: PEESE in trouble." (WAIVE slide 7)
- Why it matters: The core intuition is coherent: if researchers manipulate standard errors, methods using reported precision naively can be misled.
- Concrete fix: Keep this as the motivating problem, but tie it explicitly to MAIVE's remaining limitations rather than treating PEESE failure as enough.
- Devil's-advocate check: This is a genuine strength, not just rhetoric, because it aligns with the MAIVE paper's documented concern about spurious precision.

2.
- Claim: WAIVE is transparent about its mechanical novelty.
- Severity: Medium
- Evidence: [MANUSCRIPT] "Penalize only negative residuals (π_i < 0) — downweight spuriously precise estimates." (WAIVE slide 21)
- Why it matters: The proposed increment is easy to understand and audit. That transparency makes the method testable.
- Concrete fix: Preserve the simplicity, but present it as a transparent assumption requiring validation. Add sensitivity panels that show when the penalty helps, hurts, or does nothing relative to MAIVE.
- Devil's-advocate check: This is a strength even from a skeptical stance: the rule is exposed clearly enough that its contribution can be debated directly.

## Biggest interpretive blind spot

WAIVE's largest blind spot is that it treats "more aggressive penalization" as if it were naturally equivalent to "better correction." The extracted material shows a plausible suspicious pattern, but the interpretation of negative residuals carries more weight than the evidence shown for it. The successor claim leans on MAIVE's credibility while needing to prove incremental validity over MAIVE, not merely continuity with it. No [EXTERNAL] literature was used.

## Bottom line

WAIVE is credible as a proposed MAIVE extension, but the extracted record does not yet support presenting it as a clean methodological successor. The strongest contribution claim should be narrowed to: WAIVE adds a transparent residual-based downweighting sensitivity to MAIVE. A stronger claim requires visible evidence that the penalty improves performance beyond MAIVE and is not just a plausible but arbitrary tilt. In role, I would press hard against any abstract or conclusion language implying validated routine use.

## Candidate for minority report

I would defend Criticism 2 even if outvoted: the slide's own language says "possibly p-hacking" (WAIVE slide 20), while the proposed rule penalizes negative residuals as "spuriously precise estimates" (WAIVE slide 21). That interpretive jump is the main place where the contribution could exceed the evidence.

## Extraction caveats

The WAIVE extraction is incomplete: [MANUSCRIPT] "Final slides: results/applications/conclusions/Q&A (text-extraction truncated; not used in this audit)." (WAIVE slides 22-26) Therefore, this audit cannot evaluate detailed WAIVE results, applications, or conclusion language contained only there. Slides are also text-light, so some visual claims are represented through extracted descriptions rather than full slide text.

## Unverified references

None. No [EXTERNAL] sources were used.
