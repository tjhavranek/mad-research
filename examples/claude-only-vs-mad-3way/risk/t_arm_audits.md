## Audit X
### Round 1
I have the complete paper (pages 2315-2333, the main article). The Supplement is referenced but not in this PDF. I have everything needed: abstract, intro, data, publication bias section, heterogeneity (BMA) section, implied risk aversion, conclusion, and all main tables/figures. Let me now compose my methodological referee report.

Let me re-examine the key methodological passages carefully before writing. The core estimands are: (1) the publication-bias-corrected mean RRA, (2) the BMA coefficients on heterogeneity moderators, and (3) the "implied best practice" RRA in Table 6. Let me verify specific quotes I'll rely on.

Key passages I've confirmed:
- p.2317: "the corrected mean coefficient meta-regression... most techniques used to estimate Euler equations imply that estimates and standard errors should be statistically independent quantities in the absence of selective reporting."
- p.2320: "If we assume that publication bias is indeed a linear function of the standard error. (This is a strong assumption that we will later relax...)"
- p.2320: "We account for the obvious heteroskedasticity by weighting the regressions by inverse variance"
- p.2323: "Estimates and standard errors can be correlated because of publication bias but also because of heterogeneity. In other words, meta-regressions in the previous section may suffer from omitted-variable bias."
- p.2327: "we plug zero for the standard error to approximately correct for publication bias... This approach is inevitably subjective..."
- p.2328 conclusion: "we use more than one estimate from primary studies, which violates the standard meta-analysis assumption that all estimates are independent. We partially address this problem by clustering... and wild bootstrap."
- p.2318: "no upper threshold exists for risk aversion... it is difficult to identify the implausibly large estimates"

Now I have enough grounding. Writing the report.

# Peer audit (anonymized)

## Top 5 criticisms

### 1. The FAT-PET intercept is interpreted as the causal "bias-corrected mean RRA," but the identifying assumption (SE correlated with the estimate only through selection) is asserted, not tested against the heterogeneity the paper itself documents
- Severity: High
- Evidence: "If we assume that publication bias is indeed a linear function of the standard error. (This is a strong assumption that we will later relax...)" (p.2320); and the paper concedes the prior section is mis-specified: "Estimates and standard errors can be correlated because of publication bias but also because of heterogeneity. In other words, meta-regressions in the previous section may suffer from omitted-variable bias." (p.2323, Sec. 4)
- Why it matters: The headline estimand (mean RRA of 1 in economics, 2-7 in finance) is the FAT-PET *intercept*. That intercept is only the bias-free mean if the slope captures *selection* and nothing else. The paper admits the SE-estimate correlation can arise from heterogeneity, which means the Table 2 intercepts are potentially confounded — yet these intercepts are carried forward as the corrected means. The validity rests on an assumption the authors call "strong" and acknowledge is violated.
- Concrete fix: Report the multivariate-meta-regression intercept (Table 5 constant, after partialling out the 30 moderators) as the primary bias-corrected mean, not the bivariate Table 2 intercept; show the two side by side and reconcile the gap. State explicitly that the Table 2 numbers are upper-bound-style estimates conditional on the linearity assumption.
- Devil's-advocate check: Strongest defense — the authors *do* relax linearity via five non-linear correction models (Table 3), and the BMA constant in Table 5 (-8.84) plus the implied-RRA exercise (Table 6) is the "real" final estimand, so the linear intercept is just a stepping stone. This partly holds: Table 3 addresses the *functional form* of selection. But it does *not* address the omitted-variable confound — every Table 3 model is still bivariate (estimate on SE alone), so heterogeneity contamination of the slope/intercept survives all five "robustness" corrections. The defense rebuts the linearity charge but not the confounding charge, so the criticism stands.

### 2. The standard error is used simultaneously as the instrument for selection and as a substantive moderator, conflating the identification of publication bias with the identification of heterogeneity
- Severity: High
- Evidence: In Table 5 the variable "Standard error" has the highest posterior inclusion probability (PIP 1.000, Post. mean 0.980) and is described as "The single most important variable is the standard error, which corroborates our previous results concerning publication bias: The correlation between estimates and standard errors cannot be explained by heterogeneity, and publication bias thus remains the most plausible explanation." (p.2323)
- Why it matters: The same regressor (SE) cannot cleanly identify both "selection intensity" (its role in the FAT-PET) and a structural-heterogeneity channel inside the BMA. If imprecise studies systematically differ in design (e.g., use cross-section, finance journals, large-RRA settings), the SE coefficient absorbs both selection and design heterogeneity, and the partition between "publication bias" (SE) and "true heterogeneity" (the other moderators) is not identified — it is assumed by labeling. The claim that the SE correlation "cannot be explained by heterogeneity" is asserted from PIP dominance, not from a test that breaks the SE-design collinearity.
- Concrete fix: Report the correlation between SE and the main design moderators (cross-section, finance journal, stockholder), and run the BMA with SE excluded to show how much moderator coefficients move; alternatively instrument the SE with a precision proxy (e.g., sample size / inverse degrees of freedom) that is plausibly orthogonal to the design choices, as is now common in the meta-analysis literature [EXTERNAL].
- Devil's-advocate check: Strongest defense — a dominant SE PIP that survives conditioning on 30 design variables is exactly the evidence that selection is not just heterogeneity in disguise. This is a fair point and is the paper's best argument. But it only rules out heterogeneity *captured by the 30 included variables*; the inference "cannot be explained by heterogeneity" overreaches to unmodeled heterogeneity. The defense supports "selection is present," not the stronger "the SE coefficient is purely selection," which is what the corrected-mean estimand needs.

### 3. The "implied best practice" RRA (Table 6) plugs SE = 0 to remove bias, an out-of-sample extrapolation to a precision level no study attains, with no diagnostic for the extrapolation
- Severity: High
- Evidence: "We plug zero for the standard error to approximately correct for publication bias... This approach is inevitably subjective..." (p.2327); the SE distribution is heavily right-skewed and bounded away from zero (Table 4: Standard error mean 76.65, SD 730.63).
- Why it matters: Table 6 is the paper's deliverable for practitioners ("the values we recommend for calibration," p.2328). Setting SE = 0 evaluates the fitted meta-regression at the extreme left edge of, or beyond, the support of the data. With a mean SE of ~77 and SD of ~731, SE = 0 is far outside the bulk of observations, so the recommended numbers depend on linear extrapolation of the selection function to a region with no data — precisely where the linearity assumption (criticism 1) is least testable and the non-linear models (Table 3) disagree most.
- Concrete fix: Report Table 6 evaluated at a *feasible* high-precision benchmark (e.g., the 1st-percentile or minimum observed SE, or the SE implied by the largest sample) alongside SE = 0, and show how the recommended RRA changes; flag the rows where the corrected value lies outside the convex hull of the data.
- Devil's-advocate check: Strongest defense — SE = 0 is the textbook PET/PEESE convention for the bias-corrected mean, and the authors are transparent that it is "subjective." True, the convention is standard. But conventionality does not make it identified: the equity-premium literature offers no infinitely-precise RRA estimate, and the paper's own five correction models give materially different intercepts (Table 3, e.g., Furukawa finance 6.10 vs. Andrews-Kasy finance 11.20), which is direct evidence that the SE = 0 extrapolation is fragile. The defense explains the choice but does not neutralize the threat.

### 4. The calibration-vs-estimate "wedge" is presented as a substantive finding, but calibrations and estimates are drawn from non-overlapping, differently-sampled populations, so the comparison lacks a common estimand
- Severity: Medium
- Evidence: "Calibrations are often larger than estimates, especially in the economics literature. The wedge increases substantially when we correct the estimates of risk aversion for publication selection bias" (p.2328, Conclusion); the two samples are built by different search/selection rules: "we replace 'estimate' with 'calibration,' restrict our attention to published papers, and stop once we collect 200 usable studies (ranked by the order they appear in the Google Scholar reply to our query)" (p.2317).
- Why it matters: The wedge is the comparison of a publication-bias-*corrected* estimate distribution against an *uncorrected* calibration distribution that was itself assembled by a different (rank-truncated, published-only) sampling rule. Part of the "wedge" is therefore mechanical: one side is debiased, the other is not, and the two are not samples of the same underlying quantity (calibrations need not be unbiased point estimates of a population RRA at all). Attributing the gap to a real disconnect between calibration practice and evidence requires an estimand the design does not pin down.
- Concrete fix: Compare like with like — either contrast *uncorrected* estimates with calibrations, or articulate why calibrations should equal the bias-corrected estimate mean; and address the differing sampling rules (rank-truncation at 200, published-only) that make the calibration sample non-comparable to the 92-study estimate sample.
- Devil's-advocate check: Strongest defense — the wedge is descriptive and the paper's point is precisely that calibrations have "little basis in the distribution of the empirical estimates" (p.2318), so any gap is informative. Reasonable as a motivating observation. But the Conclusion elevates it to a quantitative finding ("the wedge increases substantially when we correct"), which imports a causal/structural reading that the mismatched sampling and the corrected-vs-uncorrected asymmetry cannot support. The descriptive framing survives; the quantitative-wedge claim does not.

### 5. Within-study dependence is acknowledged but only partially handled; the BMA treats 1021 non-independent estimates as the unit of analysis, threatening the posterior inclusion probabilities that drive every heterogeneity conclusion
- Severity: Medium
- Evidence: "we use more than one estimate from primary studies, which violates the standard meta-analysis assumption that all estimates are independent. We partially address this problem by clustering standard errors at the study level and using wild bootstrap." (p.2328, Conclusion); but the BMA is run on the full estimate-level sample: "Observations 1021 / Studies 92" (Table 5).
- Why it matters: Studies contribute very unequal numbers of estimates (1021 from 92 studies; some boxes in Figure 2 are single estimates, others many). Clustering fixes the *linear* SEs (Table 2 "Study" column), but the BMA likelihood and the PIPs in Table 5/Figure 4 — the basis for "eight variables with posterior inclusion probability above 0.5" and all the moderator claims — treat correlated within-study estimates as independent draws. This over-weights prolific studies and can inflate PIPs and shrink posterior SDs, so the headline heterogeneity findings (stockholder, finance journal, Epstein-Zin, cross-section) may rest on overstated precision.
- Concrete fix: Re-run the BMA with one estimate per study (e.g., study medians) or with study-level frequency weights inside the likelihood (as the Table 2 "Study" column does for the linear model), and report whether the >0.5 PIP set is stable.
- Devil's-advocate check: Strongest defense — the frequentist OLS check in Table 5 *does* cluster at the study level and broadly reproduces the BMA sign/significance pattern, so dependence is not driving the qualitative conclusions. This is a genuine mitigation for the OLS columns. However, the clustered frequentist check is run only on the variables already selected by the unclustered BMA (PIP > 0.5), so it cannot reveal whether dependence changed *which* variables crossed the 0.5 threshold in the first place. The defense covers post-selection inference, not the selection step, so the criticism is reduced to Medium but not eliminated.

## Top 2 strengths

### 1. The paper triangulates publication-bias correction across selection-on-significance, non-linear, and design-robust estimators rather than betting on one model
- Claim + evidence: It deploys six correction approaches with different identifying assumptions — "the selection model by Andrews and Kasy (2019), the weighted average of adequately powered estimates (Ioannidis et al. 2017), the stem-based technique (Furukawa 2021), the endogenous kink model (Bom and Rachinger 2019), and the p-uniform* technique (van Aert and van Assen 2021)" (p.2317), reported in full in Table 3 — and additionally tests p-hacking via Elliott et al. (2022) (p.2323). Why: because each estimator identifies the bias-free mean under a *different* assumption (linearity, selection-on-sign-and-significance, adequate-power thresholds), the convergence on a sub-2 economics mean is more credible than any single FAT-PET intercept, and the *disagreement* in finance (Table 3, 2.4 to 11.2) honestly bounds the residual identification uncertainty.

### 2. The heterogeneity analysis explicitly confronts model uncertainty and collinearity with priors designed for it, and reports prior-robustness
- Claim + evidence: "we use Bayesian model averaging... we also employ the dilution prior (George 2010), which accounts for collinearity by adding a weight that is proportional to the determinant of the correlation matrix of the variables" (p.2323), with prior sensitivity shown in Figure 5 ("Figure 5 shows that posterior inclusion probabilities do not change much when we apply alternative priors," p.2323). Why: with 30 correlated moderators, naive multiple regression would deliver unstable, collinearity-driven coefficients; addressing model uncertainty head-on and demonstrating PIP stability across UIP/dilution, BRIC, and HQ priors is the methodologically correct response and materially strengthens the credibility of the moderator estimands.

## Biggest blind spot
The paper's entire identification of "true" risk aversion hinges on the proposition that, absent selection, the estimate and its standard error are statistically independent — yet it never confronts the structural reason they may *not* be in Euler-equation GMM: weak-instrument and finite-sample bias. The paper cites the weak-instruments concern (Ascari et al. 2021; Stock and Wright 2000 are in the reference list) but does not test whether large RRA point estimates travel mechanically with large standard errors because *both* are products of weak identification in the underlying GMM/Euler estimation. If so, the estimate-SE correlation that the paper attributes to "publication bias" (criticism 2) is partly an artifact of the primary-study estimator itself, and the FAT-PET slope is then neither pure selection nor pure heterogeneity but econometric bias propagating from the source studies. No moderator for instrument strength (first-stage F, weak-ID diagnostics) appears among the 30 variables in Table 4, leaving the single most important confounder of the headline estimand unmodeled.

## Bottom line
The estimand is reasonably stated (the bias-corrected mean RRA from Euler-equation studies that separate risk aversion from intertemporal substitution), and the publication-bias machinery is unusually thorough and honestly triangulated. The weakness is identification of the *corrected* mean: the headline numbers are FAT-PET-style intercepts whose validity rests on an SE-estimate independence assumption the authors themselves call strong and partly contradict, with the SE doing double duty as both selection instrument and heterogeneity channel, and the practitioner-facing Table 6 obtained by extrapolating to SE = 0 outside the data. These are correctable by re-centering the headline on the multivariate intercept, breaking the SE-design collinearity, and reporting feasible-precision benchmarks. None of these threats overturns the qualitative finding (strong upward selection; economics RRA near 1, finance higher), but they widen the honest uncertainty band around the specific recommended values.

## Extraction caveats
The Supplement (Section 1, Sections A/3.1/3.2, Tables B1-B2, C1-C3, D1-D2, Figures B1-B2, D1-D2) is referenced throughout but is not contained in this PDF; several identification-relevant details (the exact specification of the BMA likelihood, between-effects weighting, and the calibration sampling) may be addressed there and could not be verified. All quotes above are from the main article (pp. 2315-2333).

## Unverified references
None.
### Round 2
I have the key passages confirmed. Now I have enough grounding to write the cross-critique. Let me note the key methodological observations:

1. The estimand: "the underlying risk aversion" / "true mean effect" — assumes a single population mean exists across very heterogeneous estimation contexts.
2. The publication-bias correction rests on the linear funnel/FAT-PET assumption ("we will add controls... which will not affect our results regarding the relation between estimates and standard errors" p.2320; "If we assume that publication bias is indeed a linear function of the standard error. (This is a strong assumption that we will later relax)" p.2320).
3. Within-study dependence: "we use more than one estimate from primary studies, which violates the standard meta-analysis assumption that all estimates are independent. We partially address this problem by clustering standard errors... and using wild bootstrap" (p.2328) — but clustering fixes inference, not the point estimate, and BMA does not cluster.
4. The Table 6 plug-in exercise: extrapolation to a counterfactual "best practice" design that may not exist in-sample — out-of-support prediction.
5. BMA causal-language: heterogeneity correlates treated as identifying context but estimated by observational meta-regression.

The two audits cover much of the contribution-overclaim and reproducibility ground (out of my lane). My lane is identification. Let me write the output.

# Peer audit (anonymized)

## Two strongest peer arguments

**1. From Audit Y, #3 — Publication-bias language asserts a mechanism the design cannot observe.** Claim: the abstract's causal "because" overstates what funnel-based tests identify. Evidence quote+locator: "reported estimates are systematically larger than the underlying risk aversion because of publication bias" (Abstract, p.1, p.2315), against the paper's own admission that "publication bias and p-hacking are observationally equivalent" (Introduction, p.2316) and "All existing methods for the identification and reduction of publication bias have their limitations" (Section 3, p.2320). Why it survives: this is squarely an identification point, and it is grounded in the paper's own text. A correlation between estimates and standard errors (funnel asymmetry) is consistent with selective reporting, specification search, small-sample/heterogeneity-precision links, or a mechanical estimate-SE correlation in GMM Euler estimation — none of which the design distinguishes. The paper concedes the observational-equivalence problem yet the headline uses directional-causal wording ("because," "underlying risk aversion"). The estimand ("underlying"/"true mean") is asserted, not identified. Survives strongly.

**2. From Audit Y, #5 — "Driven by" overstates what an observational meta-regression supports.** Claim: the abstract attributes the variation causally to design characteristics. Evidence quote+locator: "The reported estimates are driven by the characteristics of data (frequency, dimension, country, stockholding) and utility (functional form, treatment of durables)" (Abstract, p.1, p.2315); the underlying evidence is BMA posterior inclusion probabilities plus an OLS check the paper itself calls only "broadly consistent... finds borderline statistical significance for several of the variables" (Section 4, p.2323). Why it survives: BMA quantifies association under model averaging conditional on the 30 collected covariates; it does not warrant a causal "driven by." This is an identification/lane-appropriate point because the covariates are themselves non-randomly chosen by primary authors (e.g., who studies stockholders, who uses Epstein–Zin), so coefficients confound design choice with the populations and samples those designs are applied to. The frequentist check finds several PIP-flagged variables (Epstein–Zin p=0.099, nonseparable durables p=0.135, stockholder p=0.115; Table 5, p.2326) non-significant, undercutting the causal framing for exactly the variables the abstract names.

## Two weakest or overstated peer arguments

**1. From Audit X, #2 — "Significance stars do not match the reported standard errors."** Why it fails (and is out of the identification lane): this is an arithmetic/typographic complaint, not a methodological one, and the paper pre-empts it. The Table 3 note states the bracketed/parenthetical quantities are not uniform across methods: "Standard errors are reported in parentheses; the p-uniform* technique due to van Aert and van Assen (2021) only yields p values, which we report in square brackets. For the Andrews and Kasy (2019) approach, we assume that underlying effects have a t-distribution and that 1.96 is the only threshold of t-statistics that matters for publication decisions" (Table 3 note, p.2322). Counter-quote: the same note shows the stars derive from method-specific statistics, not a uniform estimate/SE ratio, so a Furukawa entry such as "1.467*** (0.951)" (Table 3, p.2322) need not satisfy a naive 1.467/0.951 t-test. The audit's own devil's-advocate concedes this; the criticism should be dropped or routed to the arithmetic reviewer.

**2. From Audit X, #1 — "Finance corrected range is compressed relative to the reported estimates."** Why it is overstated: the audit frames excluding the p-uniform* (0.625) and Andrews–Kasy (11.196) finance results as a transparency failure, but the paper explicitly characterizes the 2–7 range as the consensus after acknowledging method dispersion: "All tests corroborate strong publication bias... But the individual results vary. The p-uniform* technique yields values of risk aversion below 1 for both economics and finance. The selection model yields a large estimate for finance, 11. The remaining results are more consistent and suggest relative risk aversion around 1 in economics and 2–6 in finance" (Section 3, p.2317–2318). Counter-quote: the abstract itself qualifies — "2–7 in finance contexts (where different correction techniques give quantitatively different results, but all agree that publication bias is strong)" (Section 3 text, p.2317). The substantive methodological problem here is not "compression" (a range-reporting choice) but that the recommended range is a researcher-chosen subset of mutually inconsistent estimators with no stated decision rule — which is better captured as the estimand/extrapolation problem in my surviving list than as a "looks more settled" complaint. The audit's chosen framing partly fails on its own evidence.

## One issue that neither X nor Y raised

**Claim:** The publication-bias correction (Table 2) and the entire heterogeneity model (Table 5) rest on the assumption that the estimate–standard-error relationship is informative about selection, yet many of the included Euler-equation estimates are GMM/structural estimates in which the point estimate and its standard error are **mechanically correlated by construction** (delta-method / weak-instrument), independent of any reporting selection. The paper neither tests for nor discusses this confound of the funnel design.

**Severity:** High.

**Evidence quote+locator:** "Most techniques used to estimate Euler equations imply that estimates and standard errors should be statistically independent quantities in the absence of selective reporting. However, as shown by Andrews and Kasy (2019) and Stanley and Doucouliagos (2014), the problem with such a strategy is that publication bias can be a nonlinear function of the standard error" (Introduction, p.2316–2317). The paper acknowledges the independence assumption is needed and that nonlinearity can break it, but it treats the only threat as the *functional form* of selection. It does not address that GMM Euler estimation with weak instruments (the literature it samples — see "Stock and Wright 2000... GMM With Weak Identification," References, p.2331, and "Ascari et al. (2021)... robust to but weak instruments... the potential range for relative risk aversion is wide," Introduction, p.2316) routinely produces estimates whose magnitude and standard error rise together for reasons internal to the estimator, not selection.

**Why it matters:** If a non-trivial share of the estimate–SE slope in Table 2 (e.g., WLS 1.865***, FE 2.287***, p.2321) is a within-estimator artifact rather than reporting selection, the "corrected mean" intercept (the claimed estimand, "mean corrected RRA") is biased toward whatever value the precise-but-mechanically-correlated estimates happen to take, and the "seven-fold exaggeration" (Section 1, p.2317) is partly mismeasured. The 0.5 PIP frequentist check cannot diagnose this because standard error enters the meta-regression as the leading covariate (PIP 1.000, Table 5, p.2326) and absorbs both selection and mechanical correlation. The same threat contaminates the heterogeneity inference, since GMM is present in 59% of estimates ("GMM... 0.59," Table 4, p.2324) and its mechanical estimate-SE link is not a modeled covariate.

**Fix:** (a) Add a diagnostic splitting the sample by estimator class (GMM vs. OLS/experimental, both already coded in Table 4) and show the funnel slope and corrected intercept within each, demonstrating the asymmetry is not an artifact of weak-instrument estimators. (b) Report a placebo/specification check using only directly reported (non-"implied," non-GMM) estimates. (c) State explicitly that the FAT-PET/selection estimand is the "mean conditional on maximum precision" and is interpretable as "true RRA" only under the assumption that precise estimates are unbiased — an assumption weak-instrument GMM violates.

**Devil's-advocate on my own point:** The strongest defense is that the paper runs five non-linear/selection-model alternatives (Table 3) precisely to avoid leaning on the linear funnel, and that the wild-bootstrap and study-level clustering ("All specifications... report standard errors clustered at the study level," p.2320) absorb some within-estimator dependence. That defense partly holds for *inference robustness*, but it does not hold for the *point estimate*: every method in Tables 2–3 still maps the estimate–SE relationship to a corrected mean, so a mechanical (non-selection) component of that relationship biases all of them in the same direction, and clustering corrects standard errors, not the estimand. The point survives.

## Top 3 surviving criticisms after Round 2

**1. [Source: new] The corrected mean is identified only under a no-mechanical-correlation assumption that weak-instrument GMM Euler estimation violates; not tested.** Evidence: independence assumption stated at p.2316–2317 ("estimates and standard errors should be statistically independent... in the absence of selective reporting"); GMM share 0.59 (Table 4, p.2324); weak-IV literature in-sample (Stock and Wright 2000; Ascari et al. 2021, "the potential range... is wide," p.2316). Severity: High. Why: the claimed estimand ("underlying"/"mean corrected RRA," Abstract p.1; Table 2 p.2321) requires precise estimates to be unbiased; if precision and magnitude co-move for estimator-internal reasons, the correction and the "seven-fold" exaggeration claim are mismeasured, and no within-estimator diagnostic is shown.

**2. [Source: Y, #3, sharpened to identification] The estimand "underlying risk aversion" is asserted causally though the design only identifies funnel asymmetry consistent with multiple mechanisms.** Evidence: "because of publication bias" (Abstract, p.1); "publication bias and p-hacking are observationally equivalent" (p.2316); "All existing methods... have their limitations" (p.2320). Severity: High. Why: observational equivalence (selection vs. p-hacking vs. heterogeneity-precision links vs. mechanical correlation) means the "corrected mean" is a model-dependent benchmark, not a directly identified structural parameter; the directional-causal wording is stronger than the identification permits.

**3. [Source: new, adjacent to X #4 / Y blind spot] The Table 6 calibration recommendation is an out-of-support extrapolation to a counterfactual "best-practice" design that may not exist in the data.** Evidence: "We plug zero for the standard error... we plug in sample maxima for the time span and midpoint... We plug in 0.5 for Epstein–Zin... All other variables... set to their sample means" (Section 4, p.2327); the note frames Table 6 as "what the mean risk aversion would look like if the literature was free of publication bias and all studies used the same strategy as the one we prefer" (Table 6 note, p.2327); the paper concedes "This approach is inevitably subjective" and "yields quite imprecise results" (Section 4, p.2323/p.2327). Severity: Medium-High. Why: plugging SE = 0 jointly with sample-maximum time span and a chosen preference mix predicts at a covariate combination that need not be represented among the 92 studies; combined with the wide intervals (Finance [4.28, 10.33], Economics [−2.11, 4.30], Table 6, p.2327, the latter straddling zero), the prescriptive "values we recommend for calibration" (Conclusion, p.2328) extrapolates beyond the estimation support without a leverage/common-support check.

## Devil's-advocate check on emerging consensus

The consensus across both audits and this review is that the paper's empirical synthesis is sound but its *interpretive language* (causal "because," "driven by," "we recommend") outruns identification. The strongest counter is that the authors visibly hedge in the body text — they label Table 6 "inevitably subjective," call the field split "crude," and run five correction methods plus prior-robustness, which is more transparency than most meta-analyses offer; a reader of the full paper is not actually misled. That defense blunts the *contribution-overclaim* version of the complaint but not the *identification* version: hedging in Section 4 does not repair an estimand that is only conditionally identified (my points 1–2), and disclosed subjectivity in plug-ins does not establish common support for the Table 6 extrapolation (point 3). The methodological objections stand independent of how candidly they are flagged.

## Bottom line after Round 2

In my lane, the paper's central vulnerability is the gap between a directly identified object (funnel asymmetry / a precision-conditional mean under model averaging) and the claimed estimands ("underlying risk aversion," causal "drivers," prescriptive calibration values). The single most consequential under-examined threat — raised by neither prior audit — is that a large GMM/weak-instrument share can generate a mechanical estimate–standard-error correlation that the bias correction cannot separate from reporting selection, which would bias the corrected mean and the "seven-fold exaggeration" claim. The fixes are diagnostic, not structural: estimator-split funnels, a common-support/leverage check for Table 6, and reframing the estimand as a conditional benchmark rather than a structural parameter.

## Extraction caveats and unverified references

Full text (pp.2315–2333), all six tables, Figure A1/PRISMA, and the reference list extracted cleanly. Main-text Figures 1–5 render as images without machine-readable captions beyond the printed captions visible on the page; I relied on body-text descriptions and the numerical tables, not figure pixels, for all claims above. I did not access the online appendix or code at meta-analysis.cz/risk, the Supplement (Sections 1, 3.1, 3.2; Tables B1–B2, C1–C3, D1–D2; Figures B1–B2, D1–D2), or external bibliographic records; the Supplement may contain estimator-split or common-support diagnostics I could not verify. No fabricated quotes or locators. Unverified references: none material to the identification points above (the contribution-overclaim and missing-reference issues flagged by Audit X are out of my lane and left to the citation/contribution reviewers).

## Audit Y
### Round 1
# Peer audit (anonymized)
## Top 5 criticisms
### 1. Finance corrected range is compressed relative to the reported estimates
- Severity: High
- Evidence: "2–7 in finance contexts" (Abstract, p.1); "11.196*** (1.212)" and "0.625*** [0.008]" (Table 3, p.7); "Finance | 7.31 | [4.28, 10.33]" (Table 6, p.11)
- Why it matters: The headline recommendation excludes or downplays reported correction estimates below 1 and above 7. This makes the finance recommendation look more settled than the tables show.
- Concrete fix: State explicitly which correction methods define the recommended range and why the p-uniform* and Andrews-Kasy finance results are excluded or discounted. Reword the abstract/conclusion to match the full set of reported estimates or add a sensitivity summary.
- Devil's-advocate check: The authors do acknowledge that "the individual results vary" (Section 3, p.8) and describe 2–6 as the "remaining" results after outliers. That defense partly holds, but the abstract and conclusion still present 2–7 as the literature's answer without flagging the exclusions.

### 2. Some significance stars do not match the reported standard errors
- Severity: Medium
- Evidence: "Standard errors are reported in parentheses" and "* p < 0.10, ** p < 0.05, *** p < 0.01" (Table 3 note, p.7); "1.467*** (0.951)" and "0.474*** (0.390)" (Table 3, p.7)
- Why it matters: If parentheses are standard errors, the Furukawa entries marked with *** do not support p < 0.01 under the table's own convention. This can overstate the strength of one of the robustness corrections.
- Concrete fix: Recompute and report exact p values for Table 3, or clarify if the stars come from a method-specific statistic rather than the displayed standard errors. Audit Table 2 as well, since its star pattern also looks uneven.
- Devil's-advocate check: A method-specific test statistic could justify stars that differ from estimate/SE ratios. That defense does not hold unless the table note is revised, because the note currently instructs readers to interpret parentheses as standard errors.

### 3. Winsorization and outlier handling are under-specified
- Severity: Medium
- Evidence: "we winsorize estimates at the 5% level" (Table 1 note, p.5); "the mean for the standard error is driven by outliers; the median value is 2" (Table 4 note, p.10)
- Why it matters: Outliers are central here, with very large means and standard deviations. The text does not say what the winsorization thresholds are, whether they differ by subsample, or whether key conclusions survive other cutoffs.
- Concrete fix: Report raw and winsorized summaries, the exact cut points, and sensitivity checks for no winsorization and alternative cutoffs. Clarify whether standard errors or only RRA estimates are winsorized.
- Devil's-advocate check: A 5% cutoff is a common convention and the code may reveal the details. That helps replication with code, but not textual reproducibility or reader assessment of robustness.

### 4. Robustness varies priors more clearly than subjective classification and plug-in choices
- Severity: Medium
- Evidence: "robust to alternative priors" (Section 1, p.4); "This approach is inevitably subjective" (Section 4, p.9); "classification of studies into economics and finance fields is crude" (Conclusion, p.11)
- Why it matters: The headline economics/finance split and Table 6 fitted values depend on choices the paper itself labels crude or subjective. Robustness to BMA priors does not cover those non-prior choices.
- Concrete fix: Add sensitivity tables for alternative economics/finance classifications, different "best practice" plug-in values, and different treatment of Epstein-Zin versus other preferences. Report how the recommended calibration ranges move.
- Devil's-advocate check: The authors acknowledge these qualifications and may provide more in the Supplement. The defense is incomplete in the main text because the reported robustness claim is mainly about priors.

### 5. Search and calibration sampling are hard to reconstruct from text alone
- Severity: Medium
- Evidence: "we only inspect the first 1500 studies" (Section 2, p.4); "stop once we collect 200 usable studies" and "ranked by the order they appear in the Google Scholar reply" (Section 2, p.4)
- Why it matters: The sample depends on Google Scholar ordering and a stopping rule, but the manuscript text does not provide the ranked result lists or exclusion log. A reader cannot reconstruct which studies sat just inside or outside the cutoff.
- Concrete fix: Archive the full ranked search outputs, dates, settings, and exclusion reasons for both estimates and calibrations. Add sensitivity to larger cutoffs or show that included estimates stabilize before the cutoff.
- Devil's-advocate check: Appendix A gives a PRISMA flow and exact query, which helps. It still does not make the ranked search path reproducible from the text.

## Top 2 strengths
### 1. The PRISMA counts reconcile
Evidence: "Studies screened... (n = 1,500)" and "Studies excluded... (n = 786)" and "Studies assessed... (n = 714)" and "Included... (n = 92)" (Appendix A, Figure A1, p.16). Why: The screening arithmetic is internally consistent and gives readers a clear sample-flow baseline.

### 2. The data collection process includes redundancy and declared availability
Evidence: "two of the co-authors collect the data independently" and "clean dataset is available... together with the code" (Section 2, p.4). Why: Independent coding plus a code/data declaration materially improves auditability if the online files are accessible.

## Biggest blind spot
The largest reproducibility gap is the chain from search ordering, classification, winsorization, and subjective fitted-value choices to the final calibration recommendations. The manuscript says the Table 6 exercise is "inevitably subjective" (Section 4, p.9) and the field split is "crude" (Conclusion, p.11), while the clearest robustness claim is "alternative priors" (Section 1, p.4). That leaves readers unable to judge how much the 1 versus 2–7 conclusion depends on those choices. The online appendix or code may close this gap, but I did not verify them.

## Bottom line
The paper is strong on basic N accounting and unusually transparent in declaring data/code availability. The main reproducibility concerns are concentrated in table annotations, outlier handling, and robustness to non-prior choices. The finance recommendation should be aligned more tightly with the full range of reported correction estimates. A concise sensitivity appendix would address most of the issues above.

## Extraction caveats
Main figure images/captions for Figures 1–5 are not present in `manuscript_text.md`; only Figure A1 text is extracted. I did not run code, inspect the online appendix, access `meta-analysis.cz/risk`, or externally verify bibliographic records. Some reference typography may reflect extraction artifacts, so I did not treat accent/spacing glitches alone as substantive.

## Unverified references
"Christensen and Miguel 2018" is cited in Section 1 (p.2), but I found no matching reference entry in `manuscript_text.md`.

"Gechert et al. Forthcoming" is cited in Section 1 (p.2), but I found no matching reference entry in `manuscript_text.md`.

"Cho, and Y. Dokko. 1993" appears as a reference entry (References, p.13) and looks incomplete; I did not externally verify the correct author details.
### Round 2
# Peer audit (anonymized)

## Two strongest peer arguments

- From Audit X, claim: the SE-based publication-bias correction is not fully reconciled with heterogeneity. Evidence: “Estimates and standard errors can be correlated because of publication bias but also because of heterogeneity” (p.8, Section 4), while the paper later says “The correlation between estimates and standard errors cannot be explained by heterogeneity” (p.8, Section 4). Why it survives: this is an internal-consistency and robustness issue; the paper acknowledges the confound and then states it away after BMA without showing enough diagnostics in the main text.

- From Audit Y, claim: the calibration recommendation outruns the imprecision and subjectivity of the fitted-value exercise. Evidence: “This approach is inevitably subjective” and “Table 6 shows that such an exercise yields quite imprecise results” (p.9, Section 4), but the conclusion says “which are the values we recommend for calibration” (p.11, Conclusion). Why it survives: the recommendation is stronger than the paper’s own uncertainty language supports.

## Two weakest or overstated peer arguments

- From Audit Y, claim: the “first meta-analysis” contribution is too broad. Why it fails: this is mostly contribution framing, outside this lane, and the paper immediately narrows the scope. Counter-quote: “We focus on studies that use the consumption Euler equation and that break the link... between risk aversion and intertemporal substitution” (p.11, Conclusion).

- From Audit Y, claim: “driven by” badly overstates the heterogeneity evidence. Why it fails: the wording should be softened, but the paper also uses association language in the main conclusion. Counter-quote: “Our results also suggest that the estimates are systematically correlated with the context in which they are obtained” (p.11, Conclusion).

## One issue that neither X nor Y raised

Claim: the winsorization rule is under-specified despite being central for a highly skewed outcome. Severity: Medium. Evidence: “In the meta-analysis, we winsorize estimates at the 5% level” (p.5, Table 1 note), and the paper notes that “no upper threshold exists for risk aversion” (p.6, Section 3). Why: the main text does not say whether winsorization is one- or two-sided, by full sample or field subsample, whether SEs are affected, or how Table 2/Table 5 change without it. Fix: report exact cutoffs and add no-winsorization or alternative-winsorization robustness.

## Top 3 surviving criticisms after Round 2

1. Source X, claim: SE-based corrected means remain vulnerable to heterogeneity contamination. Evidence: “Estimates and standard errors can be correlated because of publication bias but also because of heterogeneity” (p.8, Section 4). Severity: High. Why: the paper’s headline corrected means depend heavily on interpreting the SE relationship as bias rather than remaining design heterogeneity.

2. Source Y, claim: calibration guidance is too prescriptive relative to the subjective and imprecise fitted-value procedure. Evidence: “This approach is inevitably subjective” (p.9, Section 4); “which are the values we recommend for calibration” (p.11, Conclusion). Severity: High. Why: the fix is straightforward: present conditional benchmark ranges rather than direct calibration prescriptions.

3. Source new, claim: winsorization implementation and robustness are not reproducible from the main text. Evidence: “we winsorize estimates at the 5% level” (p.5, Table 1 note). Severity: Medium. Why: with Table 1 showing large dispersion, especially finance SD 144.71, this choice can materially affect means, slopes, and fitted recommendations.

## Devil's-advocate check on emerging consensus

The strongest defense is that the paper does not rely on one correction: “We use five alternative techniques for publication bias correction” (p.6, Section 3), and Table 3 broadly keeps economics near 1 and finance above economics. That weakens any claim that the qualitative publication-bias result collapses, but it does not remove the need to narrow the calibration recommendation and document sensitivity to SE, dependence, and winsorization choices.

## Bottom line after Round 2

The qualitative result of upward selective reporting survives Round 2, and the PRISMA arithmetic in the extracted text checks out. The exact recommended calibration values remain less reproducible than the headline suggests because key choices are subjective, SE-based, and incompletely stress-tested in the main text.

## Extraction caveats and unverified references

The extracted manuscript has encoding artifacts; figures are not embedded; the Supplement was not available, so cited Supplement tables/figures could not be checked. I did not access the declared URL or run code. Potential missing reference: “Gechert et al. Forthcoming” is cited (p.2, Introduction), but I found no matching entry in the extracted References.

## Audit Z
### Round 1
# Peer audit (anonymized)
## Top 5 criticisms
### 1. Calibration recommendation outruns the evidence
- Severity: High
- Evidence: "The corrected mean estimate is 1 for economics and 2–7 for finance, which are the values we recommend for calibration." (p.11, Conclusion); "This approach is inevitably subjective but we still believe it adds value to meta-analysis" (p.9, Section 4); "Table 6 shows that such an exercise yields quite imprecise results" (p.9, Section 4)
- Why it matters: The conclusion converts a subjective fitted-value exercise into a prescriptive calibration rule. That is too strong when the paper itself stresses imprecision and discretion in setting "best practice" inputs.
- Concrete fix: Recast the recommendation as conditional benchmark ranges from the Euler-equation literature. State explicitly that calibration choices should depend on model target, setting, and whether the author accepts the preferred reliability inputs.
- Devil's-advocate check: The strongest defense is that multiple correction methods point to similar ranges, so a recommendation is useful. That defense partly holds, but it supports a conditional benchmark, not an unqualified "we recommend" sentence.

### 2. Economics-versus-finance claims rely on a crude journal proxy
- Severity: High
- Evidence: "After correction for the bias, the literature suggests a mean risk aversion of 1 in economics and 2–7 in finance contexts." (p.1, Abstract); "The precise boundary is hard to draw" (p.5, Section 2); "our classification of studies into economics and finance fields is crude and follows the classification of journals" (p.11, Conclusion)
- Why it matters: The paper treats the economics/finance split as substantive context, but the assignment rule is publication venue. A reader may infer differences in agents, markets, or identification settings when the evidence is closer to a journal-category contrast.
- Concrete fix: Rename these throughout as journal-category estimates unless alternative classifications confirm the same split. Put more weight on directly observed design features than on the economics/finance label when giving calibration guidance.
- Devil's-advocate check: The authors note that "The sharp difference between the distribution of estimates in economics and finance according to our definition suggests that the classification we use is informative" (p.11, Conclusion). That helps justify reporting the split, but not presenting it as fully identified economics-versus-finance context.

### 3. Publication-bias language states mechanism more firmly than the design permits
- Severity: High
- Evidence: "reported estimates are systematically larger than the underlying risk aversion because of publication bias" (p.1, Abstract); "publication bias and p-hacking are observationally equivalent" (p.2, Introduction); "All existing methods for the identification and reduction of publication bias have their limitations" (p.6, Section 3)
- Why it matters: The abstract's "because" asserts a mechanism and an underlying value more strongly than the paper can directly observe. The evidence supports patterns consistent with selective reporting or specification search, not direct observation of publication decisions.
- Concrete fix: Replace causal wording with "consistent with selective reporting or specification search" and distinguish corrected meta-analytic means from "underlying risk aversion." Keep the strong empirical result, but remove the mechanism certainty.
- Devil's-advocate check: The strongest defense is that "All tests corroborate strong publication bias" (p.8, Section 3). That supports strong concern about bias, but the mechanism remains model-inferred.

### 4. The "first meta-analysis" contribution is broader than the actual scope
- Severity: Medium
- Evidence: "We provide the first meta-analysis of the literature estimating relative risk aversion." (p.11, Conclusion); "Risk aversion can be estimated using lab experiments, surveys, labor-supply behavior, auction behavior, choices in insurance contracts, option prices, and game show contestant behavior" (p.1, Introduction); "We focus on the consumption Euler equation approach" (p.1, Introduction)
- Why it matters: The paper's novelty is real, but the headline claim reads as covering the full empirical risk-aversion literature. The actual contribution is narrower: Euler-equation estimates that disentangle risk aversion from intertemporal substitution.
- Concrete fix: Use the narrower claim in the abstract and conclusion: first meta-analysis of Euler-equation estimates of relative risk aversion that separate RRA from EIS. That keeps the novelty without overclaiming coverage.
- Devil's-advocate check: The abstract does state the sample restriction immediately after the broad claim. Still, the conclusion repeats the broader framing, so the scope should be tightened.

### 5. "Driven by" overstates observational heterogeneity evidence
- Severity: Medium
- Evidence: "The reported estimates are driven by the characteristics of data (frequency, dimension, country, stockholding) and utility (functional form, treatment of durables)." (p.1, Abstract); "Epstein–Zin | 5.488 | 1.370 | 0.991 | 5.592 | 3.390 | 0.099" (p.10, Table 5); "Nonseparable durables | 4.834 | 1.372 | 0.979 | 5.008 | 3.354 | 0.135" (p.10, Table 5)
- Why it matters: "Driven by" implies causal or design-effect language, while the evidence is observational meta-regression and some frequentist checks are weak. The result is better framed as association under model averaging.
- Concrete fix: Change "driven by" to "associated with" or "systematically related to." Separate the most robust correlates from variables whose support comes mainly from posterior inclusion probabilities.
- Devil's-advocate check: The BMA evidence is strong for several variables, and the paper says the OLS check is "broadly consistent" (p.9, Section 4). That defense supports reporting the associations, not causal phrasing.

## Top 2 strengths
### 1. The scope restriction improves interpretability
- Evidence: "Third, the study must separate risk aversion from intertemporal substitution." (p.4, Section 2)
- Why: This restriction directly addresses the main interpretive problem in power-utility Euler-equation estimates and gives the synthesis a coherent target.

### 2. The paper is unusually transparent about model uncertainty
- Evidence: "we use Bayesian model averaging techniques to account for model uncertainty" (p.1, Abstract); "The results of Bayesian model averaging can be sensitive to the priors used, but Figure 5 shows that posterior inclusion probabilities do not change much when we apply many alternative priors" (p.8, Section 4)
- Why: The contribution is stronger because the authors do not rely on a single preferred meta-regression. The robustness framing makes the heterogeneity analysis more credible.

## Biggest blind spot
The paper wants to "help reconnect calibrations of risk aversion to empirical estimates thereof" (p.5, Section 2), but it does not sufficiently model what calibration is trying to accomplish. It acknowledges that "large risk aversion is often sought for calibration (e.g., to help explain the equity premium puzzle)" (p.5, Section 2), yet the final recommendation treats the corrected empirical mean as the main calibration object. Table 6 is explicitly an answer to "what the mean risk aversion would look like if the literature was free of publication bias and all studies used the same strategy as the one we prefer" (p.11, Table 6 note). That is useful as a benchmark, but it is not the same as showing which calibration values should be used for particular model targets.

## Bottom line
This is a valuable focused meta-analysis, but its contribution language should be narrower and more conditional. The strongest results support a corrected benchmark for a restricted Euler-equation literature, not a general rule that "1 for economics and 2–7 for finance" are "the values we recommend for calibration" (p.11, Conclusion). The paper should keep the empirical synthesis and soften the prescriptive and causal language around calibration, publication bias, and field differences.

## Designated minority objection
Even after wording revisions, I would object to retaining a prescriptive calibration recommendation unless the authors add a separate validation exercise, because their own preferred values depend on "the same strategy as the one we prefer" (p.11, Table 6 note) and an exercise they call "inevitably subjective" (p.9, Section 4).

## Extraction caveats
The extracted manuscript contains encoding artifacts in some characters and dashes, but the relevant wording and page markers are legible. Figures are not embedded in `manuscript_text.md`; only textual figure descriptions, tables, Appendix A, and references are available. Supporting Information beyond the extracted Appendix A was referenced but not available in the file.

## Unverified references
None.
### Round 2
# Peer audit (anonymized)
## Two strongest peer arguments
- From Audit Y: The finance recommendation compresses the uncertainty in the paper’s own correction estimates. Evidence: “After correction for the bias, the literature suggests a mean risk aversion of 1 in economics and 2–7 in finance contexts” (p.1, Abstract), but Table 3 reports finance corrected RRA values including “11.196*** (1.212)” and “0.625*** [0.008]” (p.7, Table 3). Why it survives: the paper does later say “the individual results vary” and that the selection model gives “finance, 11” (p.8, Section 3), but the abstract and conclusion still convert a wider set of reported results into a narrower calibration message.

- From Audit X: The calibration-estimate “wedge” is promoted beyond what the two differently sampled objects can support. Evidence: the paper concludes, “The wedge increases substantially when we correct the estimates of risk aversion for publication selection bias: The corrected mean estimate is 1 for economics and 2–7 for finance, which are the values we recommend for calibration” (p.11, Conclusion), while the calibration sample is built differently: “restrict our attention to published papers, and stop once we collect 200 usable studies” (p.4, Section 2). Why it survives: the descriptive contrast is useful, but the conclusion uses it to recommend calibration values even though calibrations and estimates are not parallel samples and only the estimate side is corrected for publication bias.

## Two weakest or overstated peer arguments
- From Audit Y: The significance-star mismatch in Table 3 is not a contribution-skeptic criticism. Why it fails: it may be a table-annotation or arithmetic issue, but it does not by itself show that the paper overstates its contribution or policy implication. Counter-quote: the substantive textual claim is already hedged as “All tests corroborate strong publication bias” while “the individual results vary” (p.8, Section 3), so the contribution issue is the compressed interpretation of that variation, not the stars.

- From Audit X: The within-study dependence point is overstated as a threat to “every heterogeneity conclusion.” Why it fails: this is mainly an inference-mechanics criticism outside this lane, and the paper explicitly discloses the limitation. Counter-quote: “we use more than one estimate from primary studies, which violates the standard meta-analysis assumption that all estimates are independent. We partially address this problem by clustering standard errors at the study level and using wild bootstrap” (p.11, Conclusion).

## One issue that neither X nor Y raised
Claim/severity: The abstract overstates the calibration-estimate relationship as field-general. Severity: Medium.

Evidence quote+locator: The abstract says, “We show that calibrations of risk aversion are systematically larger than estimates thereof” (p.1, Abstract). But Section 2 states, “In finance, the opposite is the case: estimates overtop calibrations” (p.5, Section 2).

Why: This is a direct abstract-vs-evidence overclaim. The paper’s more careful body text says the wedge differs by field, but the abstract presents the calibration pattern as systematic without the finance exception.

Fix: Rewrite the abstract to say that calibrations exceed estimates in economics, while finance estimates exceed calibrations before publication-bias correction; then state the corrected economics/finance ranges separately.

## Top 3 surviving criticisms after Round 2
- Source Y: The finance calibration recommendation narrows the reported correction evidence. Evidence: “2–7 in finance contexts” (p.1, Abstract) versus finance corrected RRA values “2.535,” “11.196,” “6.100,” “2.390,” and “0.625” (p.7, Table 3). Severity: High. Why: the recommendation is a central policy-facing implication, and the excluded tails are reported in the paper itself.

- Source X: The calibration-estimate wedge should remain descriptive, not the basis for strong recommended values. Evidence: “which are the values we recommend for calibration” (p.11, Conclusion) and calibration sampling that “restrict[s] our attention to published papers” and stops at “200 usable studies” (p.4, Section 2). Severity: Medium. Why: the contribution is valuable, but the comparison joins differently sampled and differently corrected objects.

- Source new: The abstract’s “systematically larger” calibration claim conflicts with the finance evidence. Evidence: “calibrations of risk aversion are systematically larger than estimates” (p.1, Abstract) versus “In finance, the opposite is the case” (p.5, Section 2). Severity: Medium. Why: this is an avoidable abstract-level overclaim that the body already knows how to qualify.

## Devil's-advocate check on emerging consensus
The authors do acknowledge that the alternative corrections vary and explicitly identify the finance estimate of 11 and the p-uniform* value below 1, so the main text is more careful than the abstract. Still, the conclusion translates selected values into “the values we recommend for calibration” (p.11, Conclusion), so the overclaim survives at the level most readers will retain.

## Bottom line after Round 2
The paper’s contribution is real: it builds a large, focused meta-analysis and connects estimates to calibration practice. The main contribution-skeptic problem is compression: broad abstract claims and calibration recommendations smooth over field-specific reversals, heterogeneous correction results, and non-parallel calibration versus estimate samples.

## Extraction caveats and unverified references
Supplementary materials, online appendix, code, and figure images/captions were not verified from `manuscript_text.md`. Audit Y flags citations to “Christensen and Miguel 2018” and “Gechert et al. Forthcoming” (p.2, Section 1) without matching reference entries in the extracted manuscript, and the reference entry “Cho, and Y. Dokko. 1993” appears incomplete (p.13, References); I did not externally verify these references.

## Designated minority objection
