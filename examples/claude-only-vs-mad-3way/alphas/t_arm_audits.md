## Audit X
### Round 1
The paper ends at page 56 (the online appendix is institutional background, not additional methods). I now have full coverage of the estimand, identification, and diagnostics. Let me verify a couple of key facts in the publication-bias treatment and standard error handling by re-reading the relevant passages on pages 14, 19, and 32 closely â€” these are load-bearing for my critique.

I have what I need. The key methodological facts:
- Estimand: determinants of *reported* alphas + a "best-practice" predicted value.
- Publication bias handled only by including SE and SEÃ—IV as regressors (Table 3, Table A1), deferring the formal FAT-PET/selection treatment to the companion Yang et al. (2023).
- Dependence: 1,019 estimates from 74 studies; OLS uses study-clustered SE; BMA treats each estimate as an observation (PIPs computed on n=1,019).
- "Best practice" alpha is an out-of-sample extrapolation: data year and publication year set to sample *maxima*, plus other covariates at chosen values.
- Bear/bull market identification, gross-vs-net fee identification, and the time trend are all cross-study observational comparisons.

Let me write the audit.

# Peer audit (anonymized)

## Top 5 criticisms

### 1. The headline "current performance is zero" claim rests on extrapolating two collinear time variables to their joint sample maximum â€” outside the support where they were jointly observed
- Severity: High
- Evidence: The best-practice estimate is built by setting time variables to sample extremes: "we thus set both the data year and the year of publication to the maximum values these measures have in our sample" (p.37); and the model includes *both* "Data year" (PIP 1.000, coef âˆ’0.248) and "Publication year" (PIP 0.994, coef âˆ’0.126) as separate strongly-negative regressors (Table 3, p.32). The resulting central estimate is "âˆ’0.079" with CI "(âˆ’0.393, 0.235)" (p.38), reported as "we cannot reject the null hypothesis that hedge funds currently generate no abnormal after-fee return" (p.38).
- Why it matters: Data midpoint year and publication year are mechanically correlated (a study published in year *t* cannot use data centered after *t*), yet the design pushes *both* to their maxima simultaneously and adds their two negative slopes. Table 5 shows the maximum-change contribution of Data year is âˆ’0.802 (1,017% of the best-practice estimate) and Publication year âˆ’0.384 (487%) (p.40) â€” i.e., the entire negative result is manufactured by extrapolating two near-collinear regressors jointly beyond their observed joint range. The estimand silently shifts from "what do studies report" (interpolation) to "what would a study report at a covariate combination that may never have occurred" (extrapolation), and the linear-in-logs trend is imposed, not tested.
- Concrete fix: Report the predicted alpha at the most recent *jointly observed* (data-year, publication-year) pair rather than the independent maxima; show a partial-dependence / leverage plot for the time trend; and test non-linearity (e.g., spline or post-2015 indicator) instead of assuming the linear log trend continues to the boundary.
- Devil's-advocate check: The strongest defense is that Figure 1's raw scatter already shows the median-alpha trend line "crosses the horizontal axis around the year 2015" (p.1) without any regression, so the zero result is not purely an extrapolation artifact. This partly holds for the *direction* of the trend, but not for the *inference*: Figure 1 is a univariate fit on study medians (and explicitly "Outliers are omitted," Fig.1 notes, p.2), whereas the "not reliably different from zero" claim and its CI come entirely from the multivariate boundary prediction. The defense rescues "alphas have declined," not "current alpha is statistically zero."

### 2. Publication-selection bias â€” the central threat for a meta-analysis of a literature that rewards positive alphas â€” is not corrected in this paper; it is outsourced
- Severity: High
- Evidence: The only adjustment is including precision as a covariate: "we interact SE with an indicator variable equal to 1 for alpha coefficients estimated using IV" (p.20), with SE (PIP 0.107) and SEÃ—IV (PIP 0.107) carrying negligible inclusion probability in the main model (Table 3, p.32). The actual selection correction is deferred elsewhere: "we build on the companion paper Yang et al. (2023), who investigate the impact of the publication selection bias... we plug zero for the measure of a hedge fund alpha's standard error and also for the respective interaction term" (p.37, 39).
- Why it matters: The sample is explicitly the published record â€” "We consider only published estimates as these successfully cleared the peer-review process" (p.13) â€” exactly the population where significant positive alphas are over-represented. Putting SE in as one of 34 BMA regressors is not a funnel-asymmetry (FAT-PET) test; and because SE earns PIPâ‰ˆ0.11, the model essentially concludes precision does not matter and then "plugs zero" for it in the best-practice estimate. That move *assumes* the bias away rather than estimating it, so the magnitude of every reported alpha (the 0.36 unconditional mean and all conditional means) is left uncorrected within this paper's own analysis.
- Concrete fix: Run the standard meta-analysis selection diagnostics inside this paper â€” funnel plot, FAT-PET / PEESE, and at least one modern selection model (e.g., stem-based or selection-weight) â€” and report the bias-corrected mean alongside the raw mean, rather than referencing a separate paper.
- Devil's-advocate check: The defense is that a companion paper (Yang et al. 2023) does the selection analysis, so duplicating it here is redundant. This is weak: a reader cannot verify the headline "alphas are zero today" claim without the correction, the correction's adequacy is unchecked here, and "plug zero for SE" is a strong, untested identifying assumption that this paper relies on but does not defend.

### 3. The unit of analysis is treated as 1,019 independent estimates for the BMA, but the estimates are deeply clustered within 74 studies, funds, and overlapping data windows
- Severity: High
- Evidence: "From these research articles we collected 1,019 alpha estimates" (p.15); the BMA reports "Observations 1,019 / Studies 74" and computes PIPs on the estimate level, while only the OLS check uses "standard errors clustered at the study level" (Table 3 notes, p.32). The within-study dependence is visible in the data: Figure 4 shows wide interquartile ranges within single studies, and many studies contribute dozens of estimates.
- Why it matters: 1,019 estimates from 74 studies means ~14 correlated estimates per study, often from overlapping fund universes and the same database vintages. The BMA's posterior inclusion probabilities and posterior SDs (the entire basis for declaring nine variables "decisive"/"strong," p.31) treat these as independent draws, overstating effective sample size and thus the apparent precision/PIPs. Clustering is applied only to the frequentist robustness check, not to the headline Bayesian inference. This is a dependence-of-observations threat to the precision claims, not just the point estimates.
- Concrete fix: Report effective number of independent observations; cluster-bootstrap the BMA (or run study-level fixed effects / a multilevel meta-regression) and show PIPs are stable; at minimum, present the study-weighted ("WM") BMA as the primary result given 74 is the real sample size.
- Devil's-advocate check: The defense is that the right panel of Table 3 already clusters at the study level and the OLS coefficients track the BMA closely (e.g., Net-of-fee âˆ’0.439 in both, p.32â€“33), so dependence does not change conclusions. This holds for point estimates but not for the *PIP-based* inferential language ("decisive," PIP 1.000): PIPs are functions of model fit across n observations and are mechanically inflated by within-study correlation regardless of whether the OLS point estimate agrees.

### 4. The "bear-market" coefficient is identified off a tiny, non-randomly-selected subsample and is interpreted as a behavioral fact about hedge funds rather than a property of which studies report it
- Severity: Medium
- Evidence: "hedge fund alphas for bear markets are by âˆ’0.246 lower relative to our benchmark case... hedge funds generate substantially lower alphas when stock market prices decline... by about âˆ’3.2% per annum" (p.33); the supporting cells are "Bear market" n=39 and "Bull market" n=39 out of 1,019 (Table 2, p.21), with bear/bull indicators each having mean 0.038 (Table A1, p.49).
- Why it matters: Only 78 of 1,019 estimates (7.6%) carry any market-condition label, and whether an estimate is tagged "bear" is a research-design choice (which authors chose to split their sample by regime), not random assignment of funds to market states. The paper draws a causal-flavored conclusion about hedge-fund market-neutrality ("despite their name, hedge funds do not seem to hold... market-neutral [positions]," p.33) from a selected 4% indicator. Confounding by which strategies/periods authors choose to study in bear regimes is uncontrolled, and the reference category is the other 92% of estimates that are unlabeled, not a clean bull comparison.
- Concrete fix: Restrict the bull-vs-bear comparison to within-study pairs that report both regimes (a study fixed effect), and flag the conclusion as descriptive of the labeled subsample; avoid the market-neutrality causal language given non-random labeling.
- Devil's-advocate check: The strongest defense is that the result has high PIP (0.931, Table 3) and survives BMA, so it is robust across specifications. But high PIP given n=39 labeled-bear estimates concentrated in few studies reflects consistency of *sign* across model specifications, not external validity; robustness to included covariates does not fix a 4%, self-selected identifying subsample.

### 5. Gross-vs-net "effective fee" is identified by comparing alphas across different studies/funds, but treated as a within-fund fee deduction
- Severity: Medium
- Evidence: "monthly alphas estimated on the net-of-fee basis are by âˆ’0.439 lower than those estimated gross of fees... we are able to quantify the effect of hedge fund fees after controlling for all other relevant characteristics" (p.33); the comparison is between 984 net-of-fee estimates and just "35" gross estimates (Table 2, p.21; Gross-returns mean 0.034, Table A1, p.49). The Abstract markets this as "our data allow us to estimate the mean management and performance fees charged by hedge funds."
- Why it matters: The netâˆ’gross difference is a between-estimate contrast across non-overlapping studies (only 35 gross estimates, likely from a handful of funds/papers), not the same funds measured both ways. Interpreting the âˆ’0.439 indicator as "the effective impact of hedge fund fees after controlling for all other relevant characteristics" (p.3) assumes the gross and net subsamples are otherwise comparable (same funds, periods, strategies) conditional on the covariates â€” a strong unconfoundedness assumption never tested. The 35 gross estimates could come from systematically different (e.g., older, equity-focused, single-database) studies, in which case the coefficient blends a fee effect with sample composition.
- Concrete fix: Identify the fee effect from studies that report *both* gross and net alphas for the same funds/periods (a paired/within-study design), and report how many of the 74 studies actually do so; show covariate balance between the gross and net subsamples.
- Devil's-advocate check: The defense is that the BMA conditions on all other design variables, so the gross/net indicator isolates fees ceteris paribus, and the magnitude (â‰ˆ5% annually) lines up with Ben-David et al. (2020)'s "two-thirds of excess return" external benchmark (p.2). Conditioning helps only if the 35 gross estimates share common support with the net sample on the confounders; with n=35 that overlap is implausible, and agreement with an external number does not establish that *this* coefficient is a clean fee effect rather than a composition artifact.

## Top 2 strengths

### 1. Model uncertainty over the design-choice covariates is handled transparently and stress-tested
The paper uses BMA with a dilution prior to address collinearity among 34 candidate moderators and then explicitly probes prior sensitivity: "we recompute them using several different priors... the use of different priors does not dramatically alter our main conclusions" (p.35, Figure 8). For a meta-regression where "the determinants of hedge fund performance are not a priori known" (p.19), averaging over specifications rather than reporting one hand-picked regression is the right way to keep the moderator-selection decision from driving the identification of which design choices matter.

### 2. The frequentist cross-check is reported alongside the Bayesian result and the two agree
Rather than relying on BMA alone, the authors report study-clustered OLS for the best model and the coefficients line up closely (e.g., Net-of-fee âˆ’0.439 BMA vs âˆ’0.439 OLS; Backfilling âˆ’0.196 vs âˆ’0.198; Data year âˆ’0.248 vs âˆ’0.239; Table 3, p.32). Showing that the moderator signs and magnitudes are not artifacts of the Bayesian machinery â€” "All of the nine variables included in the BMA model with the best fit are also significant at a better than 5% level in the OLS regression" (p.31) â€” materially strengthens the descriptive claim about which design choices move reported alphas.

## Biggest blind spot
The paper never cleanly separates two distinct estimands and lets the reader slide between them. The defensible estimand is descriptive: *what study-design and fund characteristics predict the alpha a paper reports*, for which the BMA/OLS moderator analysis is well suited. The marketed estimand is structural and forward-looking: *the true after-fee abnormal return an investor would earn from hedge funds today*, asserted via the "best-practice" prediction at the covariate boundary (p.37â€“38) and the Conclusion's "they do not do so anymore" (p.41). The bridge between them requires three identifying assumptions the paper states but does not defend â€” that "plug zero for SE" fully removes publication selection (p.37), that the log-linear time trend extrapolates to the joint maximum of two collinear year variables (p.37, Table 5), and that "best practice" design choices yield estimates representative of the investable universe rather than of the most rigorous *studies*. None of these is a property of fund returns; all are properties of the published-literature sample. The causal leap from "rigorous studies report lower numbers" to "hedge funds no longer create value" is the load-bearing inference, and it is the least examined.

## Bottom line
As a meta-regression of *what drives reported hedge-fund alphas*, the design is sound, transparent, and well cross-validated, and the declining-trend and design-sensitivity findings are credible in direction. As an estimate of *current investable hedge-fund alpha*, the headline "not reliably different from zero" is fragile: it depends on extrapolating two collinear time regressors to a joint maximum, on an uncorrected (outsourced) publication-selection problem, and on treating 1,019 within-study-correlated estimates as independent for the precision claims. The paper would be much stronger if it (a) reported the bias-corrected mean and time trend with explicit funnel/PET diagnostics inside this paper, (b) predicted at jointly observed covariate values, and (c) framed the "zero" result as a property of the recent published literature rather than of hedge funds per se.

## Extraction caveats
Figures 1, 4, 5, and 6 are reproduced as images; I read trend direction and relative dispersion from them but did not extract underlying point coordinates. Some Table 2 / Table A1 figures (e.g., n=35 gross estimates, bull/bear n=39) were read off table cells in a scanned page and are used qualitatively. I did not independently recompute any reported coefficient (arithmetic verification is out of my lane).

## Unverified references
None. (Reference-accuracy checking is out of my assigned lane; I quote in-text attributions as printed without verifying the cited sources.)
### Round 2
I now have the full paper through the conclusion, references, PRISMA diagram, and the start of Table A1. I have everything I need in my lane. Let me note the key methodological facts I've gathered before composing the cross-critique:

Key facts relevant to my lane (estimand, identifying assumptions, threats to identification, missing diagnostics):
- The estimand is "factors influencing published estimates of hedge fund performance" (Abstract), i.e., a meta-regression of reported alphas on study/fund characteristics. Conclusion makes a causal-sounding leap to "they do not do so anymore" (p.41).
- The "Data year" and "Publication year" are the two time trend regressors. Both are included simultaneously (Table 3). "Data year" is logged (Table A1, p.49). The time-decline is the headline.
- BMA + OLS with study-clustered SEs (Table 3 notes). The dependence structure: 1,019 estimates from 74 studies â€” within-study correlation is addressed only via clustering in the OLS frequentist check, but the BMA itself appears to treat the 1,019 estimates as the unit without an explicit within-study dependence model.
- Best-practice estimate (Table 4) is an out-of-sample extrapolation: setting Data year and Publication year to sample maxima, SE to zero, biases treated, net-of-fee, etc. This is the identification crux for the headline "current performance â‰ˆ 0" claim.
- Publication-bias correction is the SE term and SEÃ—IV; the funnel/FAT-PET design is in the companion Yang et al. (2023). 
- No leave-one-out / influential-study diagnostics, no test for whether the time trend is driven by compositional change (within vs. between-study decomposition).

Let me compose the Round 2 cross-critique within my methodological/causal lane.

# Peer audit (anonymized)

## Two strongest peer arguments

**1. From Audit Y â€” The headline conclusion asserts absence of value rather than non-detection (estimand drift).** Claim: the evidence supports "we cannot reliably document positive current net-of-fee alpha," but the paper concludes hedge funds "do not do so anymore." Evidence: "Our findings suggest that even though hedge funds used to generate positive value for investors in the past, on average, they do not do so anymore" (p.41, Conclusion) versus "our best practices alpha estimates of current hedge fund performance are not reliably different from zero" (p.41, Conclusion). Why it survives: this is squarely a methodological/estimand problem, and the paper's own arithmetic confirms it â€” every best-practice point estimate in Table 4 has a 95% CI that includes zero (e.g., "All strategies âˆ’0.079 (âˆ’0.393, 0.235)"; p.39, Table 4). A confidence interval spanning zero is a failure to reject the null, not acceptance of it; the paper itself uses the correct cautious framing one paragraph earlier ("we cannot reject the null hypothesis that hedge funds currently generate no abnormal after-fee return," p.38). The conclusion verb is therefore inconsistent with the estimator's own output. Survives strongly.

**2. From Audit Y â€” The "best practice" estimate is sold as more objective than its construction allows ("ensures â€¦ free of publication selection bias").** Claim: the paper concedes subjective parameter choices, then claims the procedure "ensures" freedom from publication selection bias. Evidence: "Even though the choice of these parameters inevitably involves a subjective judgment" (p.36) and "This treatment ensures that our best practices estimate is free of any publication selection bias" (p.37). Why it survives: in my lane this is an identifying-assumption overstatement. Setting the standard error to zero (and the SEÃ—IV interaction to zero) removes only the *modeled* linear small-study association the meta-regression has parameterized; it cannot "ensure" the absence of selection that operates through any other channel (e.g., outcome-dependent model/specification choice, selective reporting of which alphas enter a paper). "Ensures â€¦ free of" claims point-identification of a bias-free parameter from a sample of published estimates, which the design cannot deliver. Survives.

## Two weakest or overstated peer arguments

**1. From Audit X â€” "The time trend variable changes scale and wording" (logged Data year vs. one-year prose) rated High.** Why it largely fails in my lane: this is a units/interpretation-of-coefficient issue, not a threat to identification, and the substantive direction/significance of the trend is unaffected by the scale label. More importantly, Audit X's framing slightly misreads the design: the paper reports two distinct effects â€” "Increasing the data midpoint year by one â€¦ reduces reported alphas by âˆ’0.248" (p.34) for the logged Data year, and separately a *one-calendar-year* publication-year effect ("increasing the year of publication by one is associated with a reduction â€¦ by âˆ’0.126," p.34). The genuinely interesting methodological point Audit X gestures at but does not make is that Data year and Publication year are *both* in the model simultaneously (Table 3, p.32) and are mechanically collinear (a study published in year t can only use data up to ~t), so partialling the "time" effect between them is fragile â€” but that is a collinearity/identification concern, not the scale-wording concern X actually raises. As stated, the criticism is Medium at most and belongs to the arithmetic/exposition reviewer.

**2. From Audit Y â€” "Database interpretation turns association into an overstatement claim" (CISDM 'overstate') rated Medium.** Why it is overstated: the verb "overstate" is loose, but the paper does not rest a causal claim on it â€” the sentence is explicitly *relative* in the same breath: "alpha estimates based on the CISDM database tend to overstate hedge fund performance relative to studies based on the other databases" (p.34). "Overstate â€¦ relative to other databases" is a comparative statement, which is exactly what Audit Y says the safe version should be; the word "relative to" already supplies the benchmark-free framing. This is a single-word style nit, not a threat to identification, and is better left to the contribution/wording reviewer. Counter-quote: "researchers should be aware that alpha estimates based on the CISDM database tend to overstate hedge fund performance relative to studies based on the other databases" (p.34, emphasis on the relative clause).

## One issue that neither X nor Y raised

**Claim:** The headline time-decline is interpreted as a structural change in hedge-fund ability ("their ability to do so has diminished substantially," Abstract; "the abnormal returns generated by hedge funds decreased over time," p.39), but the meta-regression cannot separate a genuine decline in performance from a change in the *composition of the published literature* over calendar time â€” i.e., the Data-year coefficient is confounded with co-evolving study-design choices. **Severity:** High. **Evidence:** the dependent variable is reported alphas, and the design choices that depress alphas are themselves time-correlated by the paper's own account: net-of-fee reporting, backfilling treatment, and number of databases all carry large negative coefficients ("Net-of-fee returns âˆ’0.439 â€¦ PIP 1.000"; "Backfilling treated âˆ’0.196 â€¦ PIP 1.000"; "Number of databases âˆ’0.085 â€¦ PIP 0.999"; Table 3, p.32), and the paper states "the new regulation may entail a significant compliance cost" and that bias-correction practices have tightened over time (pp.5â€“6, 10). Because BMA/OLS enter these controls *linearly and additively*, any nonlinearity or interaction between Data year and the rising prevalence of bias-correction/net-of-fee reporting loads onto the linear Data-year coefficient, which the paper then reads as eroded fund skill. **Why it matters:** the central estimand the reader is sold ("ability â€¦ has diminished," Abstract) requires that the time trend be net of compositional drift in research practice; the paper controls for the *levels* of those practices but never tests whether the trend is robust to their changing *mix over time* (e.g., interacting Data year with backfilling-treated/net-of-fee, or restricting to a within-study-design subsample). **Fix:** add a Data-year Ã— design-control interaction specification and a subsample holding research design fixed (e.g., net-of-fee, backfilling-treated, 7-factor only); report whether the âˆ’0.248 trend survives. If it attenuates, reframe the trend as partly a maturation of measurement practice rather than purely declining ability.

## Top 3 surviving criticisms after Round 2

**1. [Source: Y, sharpened by me] Estimand drift in the headline â€” "absence of value" vs. "failure to detect."** Evidence: "they do not do so anymore" (p.41) contradicted by the paper's own "All strategies âˆ’0.079 (âˆ’0.393, 0.235)" (Table 4, p.39) and "we cannot reject the null hypothesis that hedge funds currently generate no abnormal after-fee return" (p.38). Severity: High. Why: a CI containing zero licenses non-rejection, not the affirmative claim of zero; the abstract/conclusion verb overstates the estimator's output and the paper is internally inconsistent.

**2. [Source: new] Compositional-drift confound in the time trend.** Evidence: time-correlated design controls with PIPâ‰ˆ1.0 (Net-of-fee âˆ’0.439, Backfilling treated âˆ’0.196, Number of databases âˆ’0.085; Table 3, p.32) enter only additively, while the trend "âˆ’0.248" (p.34) is read as declining ability ("ability â€¦ has diminished," Abstract). Severity: High. Why: the structural-decline interpretation is not identified from a between-study trend that co-moves with evolving measurement practice unless the trend is shown to survive holding design fixed; no such test is reported.

**3. [Source: Y, in my lane] Over-claimed identification of the best-practice/"current" parameter.** Evidence: "This treatment ensures that our best practices estimate is free of any publication selection bias" (p.37), built by extrapolating to sample maxima of Data year and Publication year and SE=0 (pp.37â€“38). Severity: Medium-High. Why: the "current" alpha is an out-of-sample extrapolation under a maintained linear model, not an observed estimand; "ensures â€¦ free of" asserts point-identification of a bias-free quantity that the design can at best *mitigate under its own model*, and it inherits all linearity/extrapolation risk from criticism 2.

## Devil's-advocate check on emerging consensus
The strongest defense of the paper is that all three points share one rebuttal: the authors are transparent â€” they print the CIs (Table 4), label the parameter choices "subjective" (p.36), and call for "more research to distinguish between these potential underlying causes" (p.42), so a careful reader is not misled. That defense blunts the language criticisms (1 and the wording half of 3) but does not rescue criticism 2: transparency about *levels* of controls is not a test of whether the *trend* is compositional, and no leave-one-out, interaction, or fixed-design subsample diagnostic is reported anywhere in Section 4 or the sensitivity analysis (which varies only BMA priors, p.35). So the identification gap survives even the most charitable reading; only the verb-choice complaints are softened by the authors' own caveats.

## Bottom line after Round 2
The defensible result is narrow and real: *reported* hedge-fund alphas decline across studies with later data and publication years, and best-practice extrapolations are not statistically distinguishable from zero. The paper's central liability in my lane is the slide from this meta-regression estimand to a causal industry claim ("ability â€¦ has diminished"; "they do not do so anymore") without a diagnostic separating a genuine performance decline from co-evolving measurement practice, and without acknowledging that a zero-spanning CI is non-detection, not absence. The fixes are cheap (interaction/fixed-design subsamples, softened verbs, an explicit best-practice input table) and would not overturn the contribution.

## Extraction caveats and unverified references
Equations and some author names render with mojibake in the extracted text (e.g., "R_p âˆ’ R_f" and "Î±_p" in Equation 1, p.12; "1.0036^12 âˆ’ 1" in the intro, p.2); I relied on the rendered page images, which were legible. Figures are bitmap plots (Figures 1, 3â€“8); figure-level numeric claims (e.g., the Figure 1 trend crossing zero "around the year 2015," p.1) were read from the image, not from underlying data. Table A1 continues beyond p.49 (the strategy/publication-characteristic rows and the full variable list past "Strategy: all funds" were not all captured); the precise definitions of Publication year, Impact factor, and Citations were inferred from the main text (p.26) rather than the full Table A1. No external bibliographic metadata or the meta-analysis.cz/alphas data link were verified.

## Audit Y
### Round 1
# Peer audit (anonymized)
## Top 5 criticisms
### 1. The time trend variable changes scale and wording across the paper.
- Severity: High
- Evidence: "Data year: The logarithm of the mean year of the data used minus the earliest average year in our data (base = 1990)." (Table A1, p.48); "Increasing the data midpoint year by one, on average, reduces reported alphas by -0.248" (Section 4.2, p.34)
- Why it matters: The coefficient is interpreted as if it were a one-year change, but the variable definition says it is a logged transformation. This affects the reproducibility and interpretation of the paper's central time-decline result.
- Concrete fix: State coefficients in the transformed scale and add marginal effects for calendar-year changes. Harmonize "mean year," "median year," and "midpoint year" terminology.
- Devil's-advocate check: The authors may intend "by one" to mean one unit of the transformed regressor. That defense does not fully hold because the prose repeatedly uses year-based language without warning readers that the regressor is logged.

### 2. The best-practice estimate is not reproducible from the text alone.
- Severity: High
- Evidence: "We thus set both the data year and the year of publication to the maximum values these measures have in our sample." (Section 4.4, p.37); "we plug in our sample maxima for the impact factor and the number of citations. We set the remaining variables to their sample means." (Section 4.4, p.38)
- Why it matters: The exact input vector behind Table 4 is not printed, and the robustness section varies BMA priors rather than these subjective scenario choices. Readers cannot reconstruct the headline estimate without the underlying files.
- Concrete fix: Add a table listing every best-practice input value for every Table A1 variable. Add sensitivity variants for maxima vs recent-year windows, means vs medians, and alternative fee/bias settings.
- Devil's-advocate check: The paper declares data and code availability, which may allow full replication. That helps, but the manuscript itself still does not document the key scenario inputs or robustness around them.

### 3. Monthly-to-annual return language is inconsistent.
- Severity: Medium
- Evidence: "0.36% on a monthly basis, which corresponds to the annual abnormal return of 4.4% (= 1.0036^12 Ã¢Ë†â€™ 1)" (Introduction, p.2); "a mean monthly alpha of 0.36%, which corresponds to an annual risk-adjusted return of 4.32%" (Figure 3 discussion, p.16)
- Why it matters: The paper alternates between compounded and simple annualization without labeling the convention. It also states that global funds generate "abnormal annual returns between 0.2% and 0.6%" (Figure 5 discussion, p.17), which appears to use monthly magnitudes.
- Concrete fix: Define one annualization convention and apply it consistently. Correct annual/monthly labels in descriptive sections.
- Devil's-advocate check: The 4.32% and 4.4% figures are close and can reflect simple vs compounded annualization. That defense only holds if the convention is explicitly stated, which it is not.

### 4. Precision-variable construction is underspecified after alpha normalization.
- Severity: Medium
- Evidence: "we normalize them by dividing annual and quarterly alphas by twelve and three, respectively" (Section 3, p.13); "When standard errors are reported, we compute the t-statistic by dividing the alpha by its standard error." (Section 3, p.14)
- Why it matters: The text does not say whether standard errors were rescaled in parallel with annual or quarterly alphas. Because SE enters the regression as a publication-bias variable, inconsistent scaling would change the analysis.
- Concrete fix: Provide formulas for converting alphas, standard errors, t-statistics, and p-values to the common monthly scale. Report how p-values were converted when degrees of freedom or test tails were unclear.
- Devil's-advocate check: Directly collected t-statistics are scale-invariant, so some cases are unaffected. That does not cover observations where SE is used as a regressor or where p-values require conversion.

### 5. Traceability is weakened by duplicate citation suffixes and a bare code/data URL.
- Severity: Medium
- Evidence: "Data and code are available in an online appendix at meta-analysis.cz/alphas" (title page, p.1); "Buraschi, A., R. Kosowski, & F. Trojani (2014a): 'When There Is No Place to Hide: Correlation Risk and the Cross-Section of Hedge Fund Returns.'" (References, p.43); "Buraschi, A., R. Kosowski, & F. Trojani (2014b): 'When there is no place to hide: Correlation risk and the cross-section of hedge fund returns.'" (References, p.43)
- Why it matters: The reference list appears to assign different suffixes to the same work, which makes citation trails harder to audit. The code/data statement gives no version, archive, checksum, or file description in the manuscript text.
- Concrete fix: Deduplicate identical reference entries and verify all a/b suffixes. Add a versioned archive link and a short inventory of replication files.
- Devil's-advocate check: The duplicate references may be a bibliography export artifact, and the URL may contain complete files. That does not solve the textual traceability problem.

## Top 2 strengths
### 1. The sample funnel is clearly counted.
Evidence: "Studies identified through a Google Scholar query (n=750), top 5 finance journals (n=174) and Portfolio Management Research website (n=171)." (Figure A1, p.47); "Studies satisfying all inclusion criteria (n = 74)." (Figure A1, p.47)  
Why: These counts make the study selection funnel auditable at a high level.

### 2. The paper provides a variable dictionary and some robustness coverage.
Evidence: "The table provides the definition, the (unweighted) mean value (Mean), the standard deviation (SD), and the mean weighted by the inverse of the number of estimates reported per study (WM)" (Table A1 notes, p.49); "we recompute them using several different priors proposed in prior literature" (Section 4.3, p.35)  
Why: The variable definitions and BMA-prior checks give readers a concrete starting point for replication.

## Biggest blind spot
The biggest blind spot is the Table 4 best-practice scenario. The paper says it uses "maximum values" for time variables (p.37), "sample maxima" for impact factor and citations (p.38), and "sample means" for the remaining variables (p.38), but it does not print the exact scenario vector. The robustness section focuses on "different priors" (p.35), not on alternative best-practice choices. That leaves the headline current-performance estimate under-audited from the text.

## Bottom line
The paper is unusually transparent about the search funnel, study count, variable definitions, and BMA prior sensitivity. The main reproducibility issues are not about the broad setup, but about exact arithmetic, variable scaling, scenario construction, and traceability. The highest-priority fix is to add a compact replication table for the best-practice input vector and align all time and return-unit language with the actual variables used.

## Extraction caveats
The supplied `manuscript_text.md` contains mojibake in equations and references, including strings such as "Ã¢Ë†â€™" and "ÃŽÂ±". Figures are represented by captions/notes rather than visible plots, so figure-level claims could not be visually audited. The page marker "[p.44]" appears twice in the references section.

## Unverified references
External bibliographic metadata and URLs were not verified. The `meta-analysis.cz/alphas` availability claim and the footnote source "https://www.azquotes.com/quotes/topics/hedge-fund.html" (p.6) were not checked outside the manuscript text.
### Round 2
# Peer audit (anonymized)
## Two strongest peer arguments
- From Audit X, claim: the publication-selection adjustment is not reproducible within this paper. Evidence: â€œwe build on the companion paper Yang et al. (2023), who investigate the impact of the publication selection biasâ€ (p.14, Section 3); â€œwe plug zero for the measure of a hedge fund alpha's standard errorâ€ and â€œThis treatment ensures that our best practices estimate is free of any publication selection biasâ€ (p.37, Section 4.4). Why it survives: the paper relies on a zero-SE plug-in and an external companion paper, but does not show the selection model or in-paper sensitivity checks that would justify â€œensures.â€
- From Audit Y, claim: the fee result should be labeled as a reported gross-net alpha gap, not as a direct estimate of fees charged. Evidence: â€œour data allow us to estimate the mean management and performance fees charged by hedge fundsâ€ (p.1, Abstract); â€œGross returns: 35 | 0.757 (0.534, 0.980)â€ (p.21, Table 2); â€œHedge funds seem to charge their investors more than 5.0% annuallyâ€ (p.33, Section 4.2). Why it survives: the gross-return base is small, and the text does not report within-study gross/net pairs or balance checks showing that the gross and net subsamples are comparable.

## Two weakest or overstated peer arguments
- From Audit Y, claim: regulatory implications outrun the evidence. Why it fails: this is mostly mechanism/contribution framing, outside this auditor lane. Counter-quote: the paper itself says, â€œWe call for more research to distinguish between these potential underlying causesâ€ (p.42, Section 5), which partly softens the earlier sentence that the finding is â€œrelevant for regulatorsâ€ (p.41, Section 5).
- From Audit X, claim: the current-performance result is â€œmanufacturedâ€ by extrapolating two time variables outside joint support. Why it fails: the concern about robustness to setting â€œboth the data year and the year of publication to the maximum valuesâ€ (p.37, Section 4.4) survives, but Audit X does not quote evidence that this exact covariate combination is outside observed joint support. Counter-quote: the paper also reports a raw declining pattern, where the â€œdashed line showing the downward-sloping trend crosses the horizontal axis around the year 2015â€ (p.2, Introduction), so the trend is not only a boundary-prediction artifact.

## One issue that neither X nor Y raised
Claim: the search and screening packet is not textually reconstructible. Severity: Medium. Evidence: the paper says it screened â€œthe first 750 articles in the Google Scholar listâ€ and stopped because relevance â€œsubstantially decreasesâ€ (p.14, Section 3); Figure A1 reports â€œStudies screened (n = 1,095)â€ and â€œStudies excluded due to lack of correspondence or data (n = 87)â€ (p.47, Appendix); Table 1 provides only â€œthe list of 74 primary studiesâ€ included (p.15, Table 1). Why: without a search date, exported search results, excluded-record list, and reason-level exclusion log, readers cannot reproduce the 1,095-to-74 flow from the manuscript alone. Fix: add a PRISMA supplement with query dates, result exports, deduplication rules, screened titles/IDs, exclusion reasons, and the correspondence/data-failure log.

## Top 3 surviving criticisms after Round 2
- Source X, claim: publication-selection correction is under-documented in the paper. Evidence: â€œwe plug zero for the measure of a hedge fund alpha's standard errorâ€ and â€œensuresâ€ freedom from publication selection bias (p.37, Section 4.4). Severity: High. Why: this is a load-bearing adjustment for the best-practice estimate, but the manuscript does not show the correction model or robustness.
- Source Y, claim: the fee estimate overstates what the 35 gross-return observations can support. Evidence: â€œGross returns: 35â€ (p.21, Table 2) and â€œHedge funds seem to charge their investors more than 5.0% annuallyâ€ (p.33, Section 4.2). Severity: High. Why: the reported coefficient is reproducibly a gross/net reported-alpha contrast, but not reproducibly a direct fee estimate without paired comparisons or common-support checks.
- Source X, claim: dependence handling is incomplete for BMA/PIP inference. Evidence: Table 3 reports â€œObservations: 1,019â€ and â€œStudies: 74,â€ while the note says only the â€œfrequentist check (OLS)â€ uses â€œstandard errors clustered at the study levelâ€ (p.32, Table 3). Severity: Medium. Why: clustered OLS helps the coefficient check, but the headline BMA inclusion probabilities are not shown under study-level dependence or study-weighted alternatives.

## Devil's-advocate check on emerging consensus
A fair defense is that the authors disclose many inputs, report study-weighted descriptive means, and state that OLS confidence intervals are â€œclustered at the study levelâ€ (p.38, Table 4 notes). They also report prior sensitivity for BMA, concluding that â€œdifferent priors does not dramatically alter our main conclusionsâ€ (p.35, Section 4.3). That narrows the surviving critique: the signs may be stable, but the paper still needs better reproducibility for selection correction, dependence-aware BMA inference, and fee-labeling.

## Bottom line after Round 2
The strongest reproducibility concerns are not about the existence of a declining reported-alpha pattern; they are about how the best-practice and fee quantities are constructed and labeled. The paper should add in-paper publication-selection diagnostics, cluster/study-weighted BMA sensitivity, gross/net support checks, and a reproducible screening log.

## Extraction caveats and unverified references
The extracted text contains mojibake in equations and references, including Greek letters in Equation 1. Figures are image-like in the text packet, so no underlying coordinates were extracted. I did not run code or verify the declared â€œData and codeâ€ URL (p.1). Apparent duplicate reference entries were visible in the printed list, e.g., Agarwal et al. (2017a)/(2017b) with the same title and outlet (p.42), and Brodeur et al. (2020a)/(2020b) with the same AER volume/pages (p.43); I did not externally verify which keys are correct.

## Audit Z
### Round 1
# Peer audit (anonymized)
## Top 5 criticisms
### 1. The headline conclusion asserts absence rather than non-detection
- Severity: High
- Evidence: "Our findings suggest that even though hedge funds used to generate positive value for investors in the past, on average, they do not do so anymore." (p.41, Section 5 Conclusion); "not reliably different from zero" (p.41, Section 5 Conclusion)
- Why it matters: The evidence supports a cautious statement about failure to document positive current alpha, not a definitive claim that hedge funds no longer create value. This distinction matters because the paperâ€™s central contribution is framed as an industry-level conclusion.
- Concrete fix: Replace "they do not do so anymore" with "we cannot reliably document positive current net-of-fee alpha in the published evidence." Make this wording consistent across the abstract, introduction, and conclusion.
- Devil's-advocate check: The strongest defense is that all best-practice point estimates are negative and confidence intervals include zero. That supports skepticism about positive alpha, but it still does not establish absence of value.

### 2. Regulatory implications outrun the paperâ€™s mechanism evidence
- Severity: High
- Evidence: "This finding is also relevant for regulators as it is likely related to the intensity of competition in the hedge fund market and the impact of the regulatory requirements." (p.41, Section 5 Conclusion)
- Why it matters: The paper estimates patterns in reported alphas, but the conclusion moves toward regulator relevance and regulatory causes without separating competition, scale, and regulation. That makes the implications sound more identified than the design can support.
- Concrete fix: Recast the regulator paragraph as hypotheses for future work. Keep the implication to "reported alphas have declined" unless mechanism-specific evidence is added.
- Devil's-advocate check: The conclusion later says, "We call for more research to distinguish between these potential underlying causes" (p.42, Section 5 Conclusion), which is a useful caveat. It does not fully offset the prior claim that the result is "likely related" to regulation and competition.

### 3. The fee contribution overstates what the gross-net alpha gap measures
- Severity: High
- Evidence: "Hedge funds seem to charge their investors more than 5.0% annually." (p.33, Section 4.2 Main Regression Results); "Gross returns: 35 | 0.757 (0.534, 0.980) | 0.817 (0.655, 0.979)" (p.21, Table 2)
- Why it matters: The paperâ€™s coefficient compares published gross and net alpha estimates, but only 35 observations are gross-return estimates. Calling the difference "fees charged" risks presenting a cross-study reporting contrast as a direct estimate of fee schedules or effective investor payments.
- Concrete fix: Rename the estimand "gross-net reported alpha gap" and reserve "fees charged" for a robustness design with within-study gross/net pairs or direct fee data.
- Devil's-advocate check: The gross-net distinction is economically meaningful, and the coefficient is large. Still, the sparse gross-return base makes the fee interpretation too strong.

### 4. The "best practice" estimate is sold as more objective than its construction allows
- Severity: Medium
- Evidence: "Even though the choice of these parameters inevitably involves a subjective judgment" (p.36, Section 4.4 Best Practice Estimate); "This treatment ensures that our best practices estimate is free of any publication selection bias" (p.37, Section 4.4 Best Practice Estimate)
- Why it matters: The paper acknowledges subjective choices, then states that the procedure "ensures" freedom from publication selection bias. That wording overstates what a model-based adjustment can guarantee, especially when the source sample is published estimates.
- Concrete fix: Change "ensures" to "is intended to mitigate" or "adjusts under the maintained model." Present alternative "best practice" profiles that do not set all quality/currentness variables to sample maxima.
- Devil's-advocate check: A transparent preferred-specification exercise is valuable and expected in meta-analysis. The problem is the certainty of the language, not the exercise itself.

### 5. Database interpretation turns association into an overstatement claim
- Severity: Medium
- Evidence: "researchers should be aware that alpha estimates based on the CISDM database tend to overstate hedge fund performance relative to studies based on the other databases." (p.34, Section 4.2 Main Regression Results)
- Why it matters: The evidence shows CISDM-based estimates are higher conditional on controls; "overstate" implies a benchmark for true performance. Without validating databases against an external or superior benchmark, the safer claim is comparative, not corrective.
- Concrete fix: Replace "overstate" with "are higher than estimates based on other databases." If the authors want "overstate," add evidence that non-CISDM databases better approximate the target hedge-fund population.
- Devil's-advocate check: The coefficient is robust and practically useful for readers comparing studies. It still supports "higher reported alphas," not necessarily "overstatement."

## Top 2 strengths
### 1. The evidence base is clearly scoped
Evidence: "We collect our sample of hedge fund alphas from peer-reviewed research articles published between January 1, 2001, and September 1, 2021." (p.13, Section 3 Data) Why: This makes the contribution legible as a synthesis of published alpha estimates.

### 2. The paper sometimes exercises good interpretive restraint
Evidence: "Thus, these findings should be considered preliminary." (p.17, Section 3 Data) Why: The descriptive sections appropriately defer stronger claims until the multivariate analysis; that discipline should be carried into the abstract and conclusion.

## Biggest blind spot
The main blind spot is the bridge from published academic alpha estimates to live investor and regulator conclusions. The paper says, "We consider only published estimates" (p.13, Section 3 Data), but later claims "important implications for investors" and "regulators" (p.41, Section 5 Conclusion). Its "current" estimate is also constructed by setting "both the data year and the year of publication to the maximum values these measures have in our sample" (p.37, Section 4.4 Best Practice Estimate). That is a useful synthetic benchmark, but it is not the same object as current investable hedge-fund performance.

## Bottom line
The paper has a valuable contribution as a meta-analysis of reported hedge-fund alphas. Its strongest defensible message is that "reported hedge fund alphas" decline over time and that best-practice current estimates are "not reliably different from zero" (p.41, Section 5 Conclusion). The manuscript should stop short of saying hedge funds "do not do so anymore" (p.41, Section 5 Conclusion) unless it reframes that as a failure to document positive alpha in the published literature. The investor, regulator, fee, and database implications need softer verbs and sharper estimand labels.

## Designated minority objection
A stricter minority view is that the abstract-level contribution should be narrowed to published estimates, because the paper itself says it studies "factors influencing published estimates of hedge fund performance" (Abstract) but concludes about hedge fundsâ€™ actual ability to create value.

## Extraction caveats
The extracted text contains mojibake in mathematical notation, for example "R_p Ã¢Ë†â€™ R_f" and "ÃŽÂ±_p" (p.12, Equation 1). I treated page markers, section headings, and table labels as reliable.

## Unverified references
None
### Round 2
# Peer audit (anonymized)

## Two strongest peer arguments

From Audit X, claim: the headline slides from a meta-analysis of reported alphas to an investor-facing claim about current hedge-fund value. Evidence quote+locator: â€œWe examine the factors influencing published estimates of hedge fund performanceâ€ (Abstract, p.1), but Table 4 is interpreted as â€œhedge funds currently generate no abnormal after-fee return for their investorsâ€ (Section 4.4, p.38) and â€œno type of hedge funds generates reliably positive after-fee abnormal returns for investorsâ€ (Section 4.4, p.38). Why it survives: the paperâ€™s defensible contribution is about reported estimates and research designs; the investor-level â€œcurrently generateâ€ language reaches beyond that descriptive estimand. Severity: High.

From Audit Y, claim: the â€œbest practiceâ€ scenario supporting the current-performance claim is underdocumented and too central to remain implicit. Evidence quote+locator: â€œwe thus set both the data year and the year of publication to the maximum values these measures have in our sampleâ€ (Section 4.4, p.37), and â€œwe plug in our sample maxima for the impact factor and the number of citations. We set the remaining variables to their sample meansâ€ (Section 4.4, p.37). Table 4 then says the â€œmean return represents the expected alpha coefficient conditional on the inputted valuesâ€ (Table 4 notes, p.39). Why it survives: without a printed input vector and scenario sensitivity, readers cannot see how much of the abstractâ€™s â€œcurrent performanceâ€ claim comes from judgment calls rather than the published-estimate evidence. Severity: Medium.

## Two weakest or overstated peer arguments

From Audit Y, claim: monthly-to-annual wording is a major flaw. Why it fails: this is a presentation/units cleanup, not a surviving contribution-scope objection. The cited inconsistency is real enough in wording: â€œ0.36% on a monthly basis, which corresponds to the annual abnormal return of 4.4%â€ (Introduction, p.2) versus â€œa mean monthly alpha of 0.36%, which corresponds to an annual risk-adjusted return of 4.32%â€ (Figure 3 discussion, p.16). Counter-quote if any: the descriptive figure discussion itself warns that stronger conclusions are delayed because â€œthese findings should be considered preliminaryâ€ (Figure 5 discussion, p.17). Severity: Low.

From Audit Y, claim: duplicate citation suffixes and a bare replication URL materially weaken traceability. Why it fails: the duplicate is a reference-list defect, but it does not bear much on the paperâ€™s contribution or interpretation claims. Evidence quote+locator: the paper states â€œData and code are available in an online appendix at meta-analysis.cz/alphasâ€ (title page, p.1), while the reference list duplicates Buraschi et al. as â€œ2014aâ€ and â€œ2014bâ€ with the same title and outlet (References, p.43). Counter-quote if any: none; the issue should be corrected, but it is not a strong contribution critique. Severity: Low.

## One issue that neither X nor Y raised

Claim: the regulator/market-efficiency implications outrun the paperâ€™s evidence about mechanisms. Severity: Medium. Evidence quote+locator: the paper frames the trend with â€œFigure 1: Are markets getting more efficient?â€ (Figure 1, p.2), then says the findings have â€œimportant implications for investors... for regulators who seek the optimal design of the regulatory frameworkâ€ and that the result is â€œlikely related to the intensity of competition in the hedge fund market and the impact of the regulatory requirementsâ€ (Conclusion, pp.41-42). It then concedes: â€œWe call for more research to distinguish between these potential underlying causesâ€ (Conclusion, p.42). Why: the paper estimates a time trend and design moderators in published alpha estimates, but it does not distinguish competition, regulation, transparency, or market efficiency as causes. Fix: recast these as hypotheses for future work, remove â€œimportant implications... for regulators,â€ or add mechanism-specific evidence before making regulatory or efficiency claims.

## Top 3 surviving criticisms after Round 2

1. Source X, claim: the abstract/conclusion overstate current hedge-fund underperformance relative to the published-estimate design. Evidence: â€œOverall, our findings indicate that... their ability to do so has diminished substantiallyâ€ (Abstract, p.1), versus the narrower object â€œreported hedge fund alphasâ€ and â€œresearch design choices in the primary studiesâ€ (Conclusion, p.41). Severity: High. Why: the paper can support a decline in reported alphas more cleanly than a broad claim that hedge funds no longer create value for investors.

2. Source X, claim: the fee contribution is marketed as estimating mean hedge-fund fees, but the evidence is a gross-vs-net alpha contrast. Evidence: â€œour data allow us to estimate the mean management and performance fees charged by hedge fundsâ€ (Abstract, p.1), while the model uses â€œboth alphas estimated using gross returns and alphas estimated net of feesâ€ (Introduction, p.2) and Table 2 has â€œGross returns: 35â€ versus â€œNet-of-fee returns: 984â€ (Table 2, p.21). Severity: Medium. Why: the contribution should be framed as an estimated net/gross reporting difference, not as direct measurement of mean management and performance fees.

3. Source new, claim: policy and mechanism language exceeds the paperâ€™s demonstrated contribution. Evidence: â€œimportant implications... for regulatorsâ€ and â€œlikely related to the intensity of competition... and the impact of the regulatory requirementsâ€ (Conclusion, pp.41-42), followed by â€œmore research to distinguish between these potential underlying causesâ€ (Conclusion, p.42). Severity: Medium. Why: the paper itself acknowledges that the causal channels remain unresolved.

## Devil's-advocate check on emerging consensus

The authors do often use careful descriptive wording, especially â€œreported alpha estimatesâ€ and â€œresearch design choices in the primary studiesâ€ (Conclusion, p.41), so the core meta-regression contribution survives. The strongest defense is that Table 4 explicitly reports wide confidence intervals including zero (Table 4, p.39), but that does not fully rescue the stronger abstract and conclusion language about hedge fundsâ€™ actual current ability to generate value.

## Bottom line after Round 2

The strongest contribution is a systematic meta-regression of published hedge-fund alpha estimates and the study features that move them. The paper should soften investor, fee, regulator, and market-efficiency claims so they track the evidence: â€œreported alpha estimatesâ€ and â€œresearch design choicesâ€ (Conclusion, p.41), not direct causal claims about current hedge-fund value or regulation.

## Extraction caveats and unverified references

Figures were available as captions/notes rather than extractable plotted data, so I did not verify figure coordinates. I did not verify external URLs, bibliographic metadata, or the online appendix. Some manuscript text contained encoding artifacts in extraction.

## Designated minority objection
