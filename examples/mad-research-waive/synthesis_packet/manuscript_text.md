# Manuscript text — extracted for Codex streams

This file contains structured text of two source documents that
together form the audit target:

- **MAIVE paper** — Irsova, Bom, Havranek, Rachinger, *Spurious precision
  in meta-analysis of observational research*, Nature Communications 16,
  Article number: 8454 (2025). (Pages MAIVE-1 to MAIVE-10.)
- **WAIVE slides** — Havranek et al., *WAIVE: Weighted Adjustment
  Instrumental Variable Estimator*, MAER-Net Annual Colloquium,
  University of Ottawa, 18 October 2025. (Pages WAIVE-1 to WAIVE-26.)

The audit target is **WAIVE**. MAIVE is the published baseline that
WAIVE proposes to extend. Use locators of the form `MAIVE p. N` or
`WAIVE slide N`.

## Extraction caveats

- Slides are text-light; bullets and figures dominate. Direct quotes
  available from MAIVE; for WAIVE, paraphrase the slide content but
  preserve the locator.
- The WAIVE slides have a final ~5 slides covering results and Q&A that
  were truncated from text extraction; key proposal content (estimator
  definition, motivation, and headline results) is captured from
  slides 1-21.

---

## MAIVE paper

<!-- MAIVE p. 1 -->

**Title:** Spurious precision in meta-analysis of observational research.

**Abstract:** "Meta-analysis assigns more weight to studies with smaller
standard errors to maximize the precision of the overall estimate. In
observational settings, however, standard errors are shaped by
methodological decisions. These decisions can interact with publication
bias and p-hacking, potentially leading to spuriously precise results
reported by primary studies. Here we show that such spurious precision
undermines standard meta-analytic techniques, including inverse-variance
weighting and bias corrections based on the funnel plot. Through
simulations and large-scale empirical applications, we find that
selection models do not resolve the issue. In some cases, a simple
unweighted mean of reported estimates outperforms widely used correction
methods. We introduce MAIVE (Meta-Analysis Instrumental Variable
Estimator), an approach that reduces bias by using sample size as an
instrument for reported precision. MAIVE offers a simple and robust
solution for improving the reliability of meta-analyses in the presence
of spurious precision."

Authors: Zuzana Irsova, Pedro R. D. Bom, Tomas Havranek, Heiko Rachinger.

Affiliations: Charles University (Irsova, Havranek); Meta-Research
Innovation Center at Stanford (Irsova); University of Deusto (Bom);
Centre for Economic Policy Research, London (Havranek); University of
the Balearic Islands (Rachinger).

Received 12 February 2025; Accepted 14 August 2025; Published 26
September 2025.

<!-- MAIVE p. 2 -->

**Spurious precision defined:** "Reported precision is defined as
spurious if it exceeds that in a correctly specified model with proper
functional form and error term properties. Correct precision, by
contrast, requires that the model yields an unbiased and consistent
estimate of [the coefficient], and that the computation of the standard
error accounts for clustering or small-sample issues when present."

**Two sources of spurious precision** (Figure 1, MAIVE p. 2):
(a) Selection on estimates — funnel asymmetry due to effect size inflation.
(b) Selection on standard errors — funnel asymmetry but NO effect size
inflation. P-hacking combines both.

**Key claim:** "Spurious precision is particularly detrimental to
meta-analysis when it co-occurs with a biased estimate (as we will see
in the p-hacking simulation scenario)."

<!-- MAIVE p. 3 -->

**Formalization** — Eq. (1): The object of meta-analysis is α₁, the
slope coefficient of a regression of outcome Y on predictor X₁ after
controlling for X₂, ..., Xₚ:
`Y_j = α₀ + α₁ X_1j + α₂ X_2j + ... + αₚ X_pj + u_j`

Eq. (2): SE(α̂₁) = √[s² / ((N−1) Var(X_1)(1−R_1²))], depending on N,
s², Var(X_1), and R_1².

**Spurious precision mechanism:** "If, for example, one or more of the
control variables X_2, ..., X_p are correlated with X_1 but are excluded
from (1), causing omitted-variable bias. Then R_1² decreases and s²
increases, affecting both the numerator and denominator of (2). We focus
on the more intuitive case in which the former effect dominates, so the
resulting standard error is smaller than the one obtained from the
well-specified model (1)."

**Seven benchmark estimators audited** (Table 1, MAIVE p. 3): Simple
average, FE/WLS, PET-PEESE, Endogenous Kink (EK), WAAP, Andrews-Kasy,
p-uniform*.

<!-- MAIVE p. 4 -->

**MAIVE definition** — Eq. (3): "SE(α̂_i)² = ψ_0 + ψ_1 (1/N_i) + ν_i"

"MAIVE relies on the following regression: [eq. (3)] where α̂ is the
effect size reported in a primary study, SE is its standard error, ψ_0
is a constant, N_i is the sample size of the primary study, and ν_i is
an error term that soaks up, among other things, the spurious elements
of the reported standard error related to p-hacking."

**Eq. (4):** Variant of Egger regression with the squared SE:
`α̂_i = α_0 + β SE(α̂_i)² + v_i`

"MAIVE replaces, in all meta-analytic contexts, the reported variance
with the portion that can be explained by the inverse of the total
sample size used in the primary study, regardless of the model
specification in the original studies (e.g., whether they use multilevel
or clustered designs)."

**Stated assumptions for MAIVE** (MAIVE p. 4-5): (i) the instrument
(sample size) is strong — first-stage F ≥ 10 (Stock-Yogo rule); (ii)
inverse sample size is reliably exogenous to the spurious component of
reported variance.

<!-- MAIVE p. 5 -->

**Bias under p-hacking selection** (Figure 2): "All meta estimators
biased upwards" — even sophisticated corrections (PET-PEESE, EK, WAAP,
Andrews-Kasy, p-uniform*) trend toward large positive bias as the
correlation between X₁ and X₂ (the manipulation lever) increases.

The simple unweighted mean (dashed line) is biased but *less* than the
sophisticated estimators when the correlation is high (≥ 0.7) — i.e.,
when p-hacking is severe, the sophisticated corrections perform worse
than just averaging.

**Panel (h):** All adjusted estimators using MAIVE-adjusted SEs perform
comparably and substantially better than their unadjusted counterparts
in the high-p-hacking regime.

<!-- MAIVE p. 6 -->

**Empirical application (Bartos et al. dataset):**
- 348 meta-analyses in economics, education, finance, business,
  political science, sociology.
- Restricted to M ≥ 30 estimates per meta-analysis, with available
  sample sizes for ≥ 10 estimates per meta-analysis.
- Final analysis sample: 310 meta-analyses (267 with F-stat > 10 in
  first stage; 239 with F > 100).

**Table 3 result:** "MAIVE estimates tend to be closer to 0 than
PET-PEESE in meta-analyses." Among meta-analyses with F > 100:
|MAIVE| > |PET-PEESE| in 29.2% of cases; |MAIVE| < |PET-PEESE| in 70.7%.
Among PET-PEESE statistically significant subset: |MAIVE| > |PET-PEESE|
in 22.4%; |MAIVE| < |PET-PEESE| in 77.6%.

<!-- MAIVE p. 7 -->

**Figure 3:** Histogram of percentage change of MAIVE relative to
PET-PEESE across 239 meta-analyses with F > 100. Distribution is
heavily left-shifted; dark bars (negative changes) dominate the
positive ones.

**Discussion — limitations of MAIVE** (acknowledged by authors):
1. "The instrument should be strong: the correlation between inverse
   sample size and reported variance needs to be substantial. ...
   Caution is warranted if the F-statistic from the first-stage
   regression of reported variance on inverse sample size falls below
   10."
2. "MAIVE uses a two-stage estimator, it is relatively imprecise and
   performs better in larger meta-analyses."
3. "MAIVE assumes that sample size is not subject to selection. If
   meta-analysts suspect that authors systematically choose sample
   sizes based on expected effect sizes, MAIVE may not offer an
   advantage over conventional estimators."

**Recommendation:** "MAIVE be routinely reported alongside conventional
meta-analysis estimators."

---

## WAIVE slides

<!-- WAIVE slide 1 -->
Title: **WAIVE: Weighted Adjustment Instrumental Variable Estimator.**
Authors: T. Havranek, P. Bom, P. Cala, Z. Irsova, H. Rachinger.
MAER-Net Annual Colloquium, University of Ottawa, 18 October 2025.

<!-- WAIVE slide 2 -->
Slide title: "No selection, no bias." Funnel plot: estimates vs.
standard errors. No selection asymmetry. Mean coincides with true
effect at 1.0.

<!-- WAIVE slide 3 -->
Slide title: "Publication bias: mean too large." Funnel plot with
selection above |t| = 1.96 (some points removed below the threshold).
Mean shifts to ~1.1, above true effect.

<!-- WAIVE slide 4 -->
Slide title: "Publication bias: PEESE works." Same setup as slide 3.
PEESE curve fits the asymmetric funnel and correctly identifies the
true effect.

<!-- WAIVE slide 5 -->
Slide title: "p-hacking on estimates: PEESE works." Selection now via
estimate manipulation (not just standard errors). PEESE still
recovers the true effect.

<!-- WAIVE slide 6 -->
Slide title: "Back to no selection." (Recap.)

<!-- WAIVE slide 7 -->
Slide title: "Aggressive p-hacking: PEESE in trouble." Funnel plot
with severe p-hacking through standard errors (Lombard-like effect:
researchers shrink SE to cross |t| = 1.96). PEESE curve now
overshoots — predicts a positive effect substantially above 1.0.

<!-- WAIVE slide 8 -->
Slide title: "Could this happen? Meta of education premium."
"True model: Earnings_j = γ · Education_j + δ · Ability_j + v_j"
"Ability not observed."
Three primary-study choices:
1. Ignore ability → γ̂ too large, SE(γ̂) too small.
2. Include a proxy (IQ/PGS) → γ̂ smaller, SE(γ̂) larger.
3. Quasi-experiment (policy reform IV) → γ̂ even smaller, SE(γ̂)
   even larger.

<!-- WAIVE slide 9 -->
Slide title: "Some estimates spuriously large & precise."
Schematic: OLS, IV_bad (weak instrument), IV_good (clean) labelled.
"via misspecification or p-hacking" annotation pointing at the
spuriously precise cluster.

<!-- WAIVE slide 10 -->
Slide title: "All meta estimators biased upwards."
Same as MAIVE Figure 2(a). Shows all of UWLS, PET-PEESE, Endogenous
kink, WAAP, Andrews-Kasy, p-uniform* trending upward as p-hacking
potential rises.

<!-- WAIVE slide 11 -->
Slide title: "Why? Key meta assumption broken."
"Traditional meta: Ê_i = E_0 + β SE(Ê_i)² + u_i"
"Methods or p-hacking" arrow pointing at both E_0 and SE(Ê_i)².
"corr(SE, u) ≠ 0 ⇒ β̂ and Ê_0 biased."
"Natural solution: 1/N instrumenting SE(Ê_i)² → MAIVE."

<!-- WAIVE slide 12 -->
Slide title: "Meta-analysis instrumental variable estimator."
Three sub-slides showing the MAIVE intuition, first stage, and adjustment.
First stage: "SE(Ê_i)² = α_0 + α_1 (1/N_i) + π_i" — π_i absorbs hacking
or misspecifications.
Adjustment: "SE(Ê_i)²_adj,i = α̂_0 + α̂_1 (1/N_i) + π_i" — but with the
π_i term struck through (i.e., dropped). The MAIVE-adjusted SE uses
only the fitted part.

Bullets: "Can add controls. Plug MAIVE-adjusted SEs into other
estimators. In practice it's better to use logs in the first stage."

<!-- WAIVE slide 13 -->
Slide title: "Aggressive p-hacking: PEESE in trouble." (Repeat of
slide 7 — emphasizing the motivating problem.)

<!-- WAIVE slide 14 -->
Slide title: "Meta-Analysis Instrumental Variable Estimator." MAIVE
fit shown as a strong curve correcting the funnel asymmetry, with
red estimates (suspected hacked, low-SE high-estimate) downweighted
or relocated.

<!-- WAIVE slide 15 -->
Slide title: "MAIVE just published." Screenshot of MAIVE Nature
Communications page (DOI: 10.1038/s41467-025-63261-0). Already 1036
accesses, 15 Altmetric at presentation date.

<!-- WAIVE slide 16 -->
Slide title: "Web app: EasyMeta.org." Screenshot of an interface for
running MAIVE without coding.

<!-- WAIVE slide 17 -->
Slide title: "Supports MAIVE, WAIVE, multilevel, clustering." Funnel
plot with MAIVE = 2.21 (SE = 0.25), simple mean = 3.06. Shows the
MAIVE fit downweighting precise-but-large estimates.

<!-- WAIVE slide 18 -->
Slide title: "Meta-Analysis Instrumental Variable Estimator." Same
content as slide 14.

<!-- WAIVE slide 19 -->
Slide title: "Weighted Adjustment Instrumental Variable Estimator."
This is the WAIVE proposal. The funnel plot shows WAIVE further
downweighting (red dots become more aggressively penalized) compared
to MAIVE.

<!-- WAIVE slide 20 -->
Slide title: "WAIVE first stage — same as MAIVE."
"Residual from MAIVE first stage: SE(Ê_i)² = α_0 + α_1 (1/N_i) + π_i
— hacking if negative?"
Bullets: "Negative π_i → reported SE too small relative to N → possibly
p-hacking. Analogy: profit shifting in accounting (regress profits on
sales, look at residuals)."

<!-- WAIVE slide 21 -->
Slide title: "WAIVE weight for the second stage."
**Core WAIVE formula:** "ω_i = exp[ −max(0, −π_i) ]"
Bullets:
- "Penalize only negative residuals (π_i < 0) — downweight spuriously
  precise estimates."
- "Weight ∈ (0, 1], smooth and proportional decay."
- "Then run PEESE of Ê_i on SE(Ê)²_adj,i with weights ω_i."

<!-- WAIVE slides 22-26 -->
Final slides: results/applications/conclusions/Q&A (text-extraction
truncated; not used in this audit).

## Notes for auditors

- **WAIVE's claimed contribution over MAIVE:** WAIVE inherits MAIVE's
  first stage but ADDITIONALLY downweights estimates whose first-stage
  residual is negative (i.e., reported SE smaller than design-implied).
  The downweight is a smooth exponential tilt (slide 21, formula
  `ω_i = exp(−max(0, −π_i))`). MAIVE does not do this — it only
  instruments the reported SE; it does not penalize estimates whose
  reported SE looks suspiciously small.
- **Where to look for objections:** (a) Is the additional downweighting
  doing real work beyond what MAIVE's instrumentation already does?
  (b) Are negative first-stage residuals a credible signal of
  p-hacking, or could they reflect heteroskedasticity, design
  heterogeneity, or measurement noise? (c) Is the choice of an
  exponential tilt a tuned-to-fit-the-simulation choice or principled?
  (d) Has WAIVE been validated empirically (replication-anchored) or
  only in simulations?
- **What MAIVE already promises:** MAIVE's published version makes
  strong claims about reducing bias under p-hacking selection (MAIVE p.
  5-7). If WAIVE merely adds incremental robustness, the contribution
  claim should be measured.
