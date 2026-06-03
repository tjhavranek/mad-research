## Audit X
### Round 1
I now have the full methods, priors, analytical framework, and references. The paper ends at the supplementary figures. I have everything I need. Let me note one discrepancy worth checking: page 23 says "necessary information was available for 217 out of the 286 meta-analyses (with 2,312 estimates)" while page 4 says "215 out of 286 (74.4%)" — but that's an arithmetic/extraction matter for another reviewer, not my lane. Let me focus on the methodological/causal issues.

I have enough to write the review. Let me compile my analysis focusing strictly on the estimand, identifying assumptions, threats to identification, and missing diagnostics.

# Peer audit (anonymized)

## Top 5 criticisms

### 1. The estimand is never defined; a single SMD is pooled across constructs, populations, exercise types, and comparator types that are not exchangeable
- Severity: High
- Evidence: The PICOS comparator criterion admits non-equivalent control conditions — "reviews were eligible if ≥ 75% of the included RCTs compared exercise to no intervention, waitlist, usual care, nothing, a sham intervention, an equal attention non-exercise intervention arm or a lower/lesser exercise intervention" (p. 22). The intervention criterion likewise pools heterogeneous modalities (p. 22), and the overall pooled estimates (e.g., "SMD = 0.227 [0.116, 0.330]", p. 7) are reported as single population parameters.
- Why it matters: A waitlist contrast estimates exercise-plus-expectancy versus nothing, whereas an attention-matched or "lower/lesser exercise" contrast estimates a dose contrast; these are different causal estimands, so the pooled mean is a weighted average over incommensurable contrasts with no defined target. The paper itself flags expectancy ("placebo-like effects", p. 3, citing Ciria et al.) yet does not condition on comparator type at any level.
- Concrete fix: State the target estimand explicitly (which contrast, which construct, which population) and add comparator type (passive vs. active/attention-matched vs. dose-response) as a moderator/subgroup, since the active-control distinction is exactly what drove the attenuation in Ciria et al. (SMD 0.22 → 0.13, quoted on p. 3).
- Devil's-advocate check: The strongest defense is that the subgroup analyses by population and exercise category (Figs. 2–4) already partition the heterogeneity, so a global estimand is not the real claim. But the subgroups are cut on population and exercise type only — never on comparator type or baseline-difference adjustment, the two moderators the paper credits (p. 3) with collapsing the Ciria et al. effect. So the defense does not hold: the dimension most relevant to the causal contrast is left uncontrolled at every level.

### 2. Publication-bias adjustment is reported as a causal correction, but the paper's own diagnostics show the identifying assumptions of the selection model are violated
- Severity: High
- Evidence: For memory, "RoBMA_PSMA finds strong evidence for publication bias operating via inflated results in small studies (BF_bias = 19.4; consistent with small-study effects), RoBMA_WF finds extreme evidence against publication bias operating via selection for significance or direction of the results (BF_bias = 1/45.5)" (p. 9). The discussion concedes "the observed distribution of test statistics indicated a higher skew near the significance criterion than expected under publication bias alone. This may indicate questionable research practices (QRPs)... which can result in over- or under-adjusted pooled effect size estimates" (p. 20).
- Why it matters: The headline "adjusted" SMDs are presented as the bias-free truth, yet for memory the two adjustment engines disagree on the sign of the bias mechanism, and for all outcomes the paper says QRPs make the adjustment direction indeterminate. An adjustment whose own sign is not identified cannot license the strong claim that true effects "diminish to zero" (p. 17).
- Concrete fix: Frame the adjusted estimates as bounds/sensitivity under competing selection mechanisms rather than point corrections, and report how much the construct-level conclusions move between RoBMA_PSMA and RoBMA_WF (which the abstract does not disclose: memory adjusted ranges 0.027 to 0.225 across the two; executive function 0.012 to 0.116).
- Devil's-advocate check: The defense is that model-averaging across 36 models "retains model uncertainty" (p. 24) and therefore already integrates over the mechanism. But model-averaging within an ensemble does not rescue identification when the diagnostic (z-skew beyond what selection predicts, p. 20) indicates the data-generating process lies outside the entire model space; KL-minimization to the nearest specified model (p. 25) gives a number, not an unbiased estimand. The defense weakens but does not eliminate the criticism — it is a reason to widen intervals, which the paper does not do.

### 3. Dependence among effect sizes from the same trial and the same original meta-analysis is incompletely handled, threatening interval calibration
- Severity: High
- Evidence: The unit is the study-level estimate, and many estimates share a parent: "835 effect sizes from 82 (out of 107) meta-analyses" for general cognition (p. 5); subgroups report both k (estimates) and m (meta-analyses), e.g., "Other (k = 280, m = 29)" (Fig. 2). Dependence is addressed only by extending "to ... multilevel versions when dealing with estimates from different meta-analyses" (p. 27). Overlap across parent meta-analyses is asserted, not modeled: "it is possible some estimates were included multiple times, the degree of overlap is likely less than 1%" (p. 20).
- Why it matters: A three-level model (estimate within meta-analysis) does not capture that the same primary RCT can appear in several different parent meta-analyses, nor that one trial contributes multiple outcomes/arms; unmodeled positive correlation deflates standard errors and inflates the precision of the pooled SMD and the Bayes factors, biasing the central inferential quantities. The subgroups most relied upon are the thinnest (e.g., "exergaming ... based on only 9 studies from a single meta-analysis", p. 9; Fig. 2 "Resistance (k = 24, m = 1)").
- Concrete fix: Cluster at the primary-study level (not the meta-analysis level), deduplicate overlapping RCTs explicitly rather than assuming <1%, and apply robust variance estimation or a crossed random-effects structure; report effective sample size per subgroup.
- Devil's-advocate check: The defense is the cited <1% overlap and the multilevel extension. But the <1% figure is borrowed from Singh et al.'s assessment of meta-analysis-level overlap (p. 20), not verified at the RCT level by these authors, and a single duplicated large trial in a k=9 subgroup is material. The defense is too weak to cover the small subgroups that carry the paper's positive findings.

### 4. The "promising" subgroup findings are selected post hoc from ~36 population×exercise cells with no multiplicity control, inviting selection on the outcome
- Severity: Medium
- Evidence: "The moderator analysis did not show evidence of moderation by any of our moderators ... most likely due to variation in between-study heterogeneity across the moderator levels. Therefore, we performed subgroup analyses by population type, population age, and exercise category" (p. 7). The abstract then elevates specific cells: "Subgroup analyses identify specific population-intervention combinations with more consistent benefits" (p. 2), and the discussion lists them as "promising evidence" (p. 18).
- Why it matters: Subgroup analysis was triggered *because* the pre-specified moderation tests were null; scanning dozens of cells (Figs. 2–4) and reporting the ones with BF_10 > 3 is selection on the realized estimate, the same mechanism the paper indicts in the primary literature. Without correction the highlighted cells are expected to include false positives and to overstate the effects (winner's-curse within the meta-meta-analysis).
- Concrete fix: Pre-register or at minimum hierarchically shrink the subgroup estimates (partial pooling across cells), report the number of cells tested, and treat surviving cells as hypothesis-generating rather than as recommendations.
- Devil's-advocate check: The defense is that BMA/Bayesian estimation with the τ prior already shrinks each cell, so no separate multiplicity penalty is needed. Partially valid — but the cells are fit as independent subgroups (separate RoBMA runs, Figs. 2–4), not as one hierarchical model that borrows strength across cells, so there is no cross-cell shrinkage; the within-cell prior does not address selecting which cell to feature. Defense holds only weakly.

### 5. Standard errors for the large majority of estimates are reconstructed under approximations whose failure would bias the bias-detection itself
- Severity: Medium
- Evidence: "For 1,828 studies, standard errors were computed using a normal approximation based on the confidence interval width. For 62 studies, standard errors were computed from the effect size and total sample size under the assumption of equal group sizes (Equation 4.20 Borenstein et al., 2009)" (p. 24); directly available for only "422 studies." Effect sizes are a mix: "either Cohen's d or Hedge's g as reported in the original meta-analysis" (p. 24).
- Why it matters: Every device in the paper — funnel plots, Egger tests, z-curves, PET-PEESE, and selection weights — is a function of the effect-size/standard-error pair. If reconstructed SEs are systematically off (unequal group sizes, t- vs z-based CIs, or d/g not converted to a common metric with its variance), the small-study/selection signal the paper treats as publication bias can be partly an artifact of SE mismeasurement, and z = effect/SE near the significance threshold (the QRP signal on p. 20) is exactly where this distortion concentrates.
- Concrete fix: Report a sensitivity analysis restricted to the 422 estimates with directly reported SEs, harmonize d vs g (with the correction-factor variance) before pooling, and show that the BF_bias and adjusted SMDs are stable to the SE-reconstruction method.
- Devil's-advocate check: The defense is that CI-width SEs are standard and any noise is mean-zero. But the concern is not random noise; it is *systematic* curvature in the SE–effect relationship, which PET-PEESE reads as small-study bias — and the paper already notes RoBMA_PSMA "results in stronger adjustment driven by potential small-study effects" diverging from RoBMA_WF (p. 12, memory). That divergence is consistent with an SE-construction artifact, so the defense does not fully neutralize the point.

## Top 2 strengths

### 1. Honest, foregrounded treatment of heterogeneity via prediction intervals rather than just I²/CIs
- The paper repeatedly converts the pooled estimate into a prediction interval and refuses to over-read the mean: "the prediction interval ... of the publication bias-adjusted overall estimate covers effect sizes consistent with both large harm and large benefit-sized effects (PI[−1.052, 1.500])" (p. 7), and recommends τ or prediction intervals over I² because "the commonly reported relative metric I² often conceals the true degree of between-study heterogeneity" (p. 19). This is the correct estimand-level humility for a heterogeneous evidence base and is methodologically sound.

### 2. Construct-specific diagnosis that the bias mechanism differs by outcome, instead of one blanket correction
- Rather than asserting uniform selection, the authors read the z-statistic distributions per outcome: "While the distribution of z-statistics of memory does not reveal any suspicious patterns, the distribution of z-statistics of general cognition and executive function reveals severe selection for positive effects" (p. 5), and "publication bias seems to predominantly target negative effect sizes [which] explains why ... 3PSM by Ciria et al. ... did not show a noticeable effect size adjustment" (p. 5). Diagnosing the selection mechanism per construct, and tying it to why an earlier correction failed, is a genuine identification-aware contribution.

## Biggest blind spot
Comparator heterogeneity is the paper's central uncontrolled confounder and it is never operationalized. The eligibility rule explicitly mixes passive controls (waitlist, usual care, "nothing"), attention/sham controls, and active dose-response controls ("a lower/lesser exercise intervention") within the same pool (p. 22), yet comparator type appears in no moderator list, subgroup cut, or sensitivity analysis — even though the paper credits exactly this distinction (active control groups) with collapsing Ciria et al.'s effect from 0.22 to 0.13 (p. 3). Because the comparator defines the causal contrast, the "true effect" the paper claims to recover via publication-bias adjustment is actually an average over a mixture of distinct estimands, and shrinkage toward zero could reflect dilution by dose-response contrasts as much as bias correction. A meta-meta-analysis that re-frames itself around identification, but then stratifies only on population and exercise modality, leaves the most decision-relevant source of confounding unexamined.

## Bottom line
Methodologically this is a careful, heterogeneity-honest re-analysis whose Bayesian model-averaging and prediction-interval framing are appropriate for a messy evidence base, and its per-construct diagnosis of differing selection mechanisms is a real strength. But it presents publication-bias-adjusted SMDs as recovered causal truth while its own diagnostics show the selection model's assumptions are violated (sign-discordant BF_bias for memory; QRP-driven z-skew the model cannot represent), and it never defines a single estimand or controls the comparator-type confounding that its own framing identifies as decisive. The dependence structure (overlapping RCTs across parent meta-analyses, multiple estimates per trial) is handled only at the meta-analysis level, and the featured subgroup "benefits" are selected post hoc from dozens of cells without multiplicity control. The negative headline (effects smaller than reported, swamped by heterogeneity) is robust to all of this; the positive subgroup recommendations and the precise adjusted point estimates are not.

## Extraction caveats
The paper is APA-style with section names but no numbered sections or equations of its own (it cites others' equation numbers), so locators are by page and named subsection/figure. Note one internal count discrepancy I observed (p. 4 "215 out of 286 (74.4%)" vs. p. 23 "217 out of the 286 ... (with 2,312 estimates)"); I flag it only because it touched extraction, but the arithmetic/reconciliation belongs to the arithmetic reviewer, not this lane.

## Unverified references
None — all in-text citations used above (Singh et al. 2025; Ciria et al. 2023; Bartoš et al. 2022; Borenstein et al. 2009; Egger et al. 1997) appear in the reference list (pp. 29–35). I did not independently verify the external content of any cited work; no claim above depends on [EXTERNAL] knowledge.
### Round 2
The PDF is not text-searchable via Grep (image-based). I have the rendered text from the Read calls, which is sufficient. Let me compose the cross-critique. I have verified the key quotes visually from the page images:

- p.5 "A three-level publication bias-unadjusted Bayesian model-averaged (BMA) meta-analysis" (General cognition)
- p.20 "we were unable to track the origin of all extracted study-level estimates. While it is possible some estimates were included multiple times, the degree of overlap is likely less than 1% based on the assessment by Singh et al."
- p.20 "the observed distribution of test statistics indicated a higher skew near the significance criterion than expected under publication bias alone. This may indicate questionable research practices (QRPs...), which can result in over- or under-adjusted pooled effect size estimates"
- p.26 "smaller studies might be conducted on clinical populations and result in a larger benefit, while larger studies might be conducted on the general population and result in a smaller benefit. This pattern of results might be inadvertently identified as small-study effects and consequently over-adjusted for by PET-PEESE"
- p.7 moderator analysis "did not show evidence of moderation by any of our moderators... most likely due to variation in between-study heterogeneity across the moderator levels. Therefore, we performed subgroup analyses"

Now I'll write the structured output, staying strictly in the methodological/causal lane.

# Peer audit (anonymized)

## Two strongest peer arguments

**1. From Audit X — Dependence and overlap in the retained study-level data are the central unaddressed identification threat.**
Claim: Because the analysis treats extracted rows as study-level inputs, duplicate or dependent estimates can distort weights, heterogeneity, and the publication-bias diagnostics; the paper's own overlap figure is unverified for the retained sample. Evidence: the paper concedes "we were unable to track the origin of all extracted study-level estimates. While it is possible some estimates were included multiple times, the degree of overlap is likely less than 1% based on the assessment by Singh et al. (2025)" (p.20, Limitations). Why it survives: this is squarely a methodological/identification problem, and it is grounded in the authors' own admission. The "less than 1%" figure is *borrowed* from Singh et al., who pooled at the meta-analysis level and therefore never measured row-level duplication in *this* study-level dataset — so the number does not bound the dependence that actually matters here. The same trial appearing in several of the 286 source meta-analyses (a known feature of overlapping umbrella reviews) inflates effective sample size and biases the z-curve/weight-function estimates that the whole "extreme publication bias" conclusion rests on (p.7: "BF_bias > 1.00 × 10^6"). This survives because the estimand — what population of independent units the SMDs represent — is never pinned down.

**2. From Audit Y — The abstract converts exploratory subgroup signals into recommendation language too quickly.**
Claim: "Identify... combinations" and "supports... recommendations" overstate what subgroup contrasts in a heterogeneous meta-meta-analysis can license. Evidence: "Subgroup analyses identify specific population-intervention combinations with more consistent benefits... the evidence supports targeted, population- and intervention-specific recommendations" (p.2, Abstract), against the body's own "the majority of subgroup analyses resulted in the absence of evidence, several subgroups showed promising evidence in favor of an effect" (p.18, Discussion). Why it survives in my lane: this is not only a tone issue — it is an estimand/multiplicity problem. The subgroup BFs in Figures 2–4 are read off dozens of simultaneous comparisons with no multiplicity adjustment, and the "promising" cells are frequently powered by a *single* meta-analysis (e.g., exergaming, non-healthy older adults, "k = 9, m = 1," Figure 2; exergaming, healthy children/adolescents, "k = 9, m = 3," Figure 4). A target parameter selected *post hoc* as the largest of many noisy subgroup estimates is a textbook winner's-curse estimand; calling it a basis for "recommendations" misstates what was identified.

## Two weakest or overstated peer arguments

**1. From Audit X (Criticism #3) — "The extraction validation can mask discrepancies by choosing the closest model."**
Why it is overstated *in the causal/identification lane*: this is an extraction-fidelity and reproducibility complaint, not a threat to the estimand or to identification, and it partly misreads the procedure. The text shows the closest-model rule is a *tolerance screen*, not a silent best-case selection: "All meta-analyses with absolute differences larger than SMD = 0.05 were re-examined. Following the re-examination, 2 meta-analyses were completely removed due to inconsistent reporting" (p.23). The authors are matching their re-computed pool to the *source* pool to detect transcription errors, and only 11 of 215 exceed 0.02; trying RE (REML/ML/DL) and FE estimators is appropriate when the source's own pooling method is undocumented. Counter-quote: "The absolute difference between the reported and the closest re-computed pooled estimate was lower than SMD = 0.02 for 201 meta-analyses" (p.23). The residual concern (which two were dropped) is a transparency gap for another reviewer, not a methodological flaw I would advance.

**2. From Audit Y (Minority objection) — the paper "may lean too hard against general cognitive benefit" given its positive adjusted general-cognition estimate.**
Why it fails: this objection ignores the paper's actual estimand for that outcome. The point estimate "SMD = 0.227 [0.116, 0.330]... BF_10 = 400.6" (p.7) is explicitly paired with a heterogeneity parameter and prediction interval the authors foreground: "τ = 0.647 [0.596, 0.704]... the prediction interval... covers effect sizes consistent with both large harm and large benefit-sized effects (PI[−1.052, 1.500])" (p.7). When τ is roughly three times the pooled mean and the prediction interval straddles zero by a wide margin, a posterior-mean BF on μ does not identify a benefit a typical trial would realize; the relevant estimand is the *distribution* of true effects, not μ. The cautious framing ("strong recommendations... are not warranted," p.17) is the methodologically correct reading, so the minority objection does not survive.

## One issue that neither X nor Y raised

**Claim:** The publication-bias adjustment and the heterogeneity model share the same standard errors, but for the overwhelming majority of estimates those standard errors are *reconstructed*, not observed — and the reconstruction is exactly the input the bias-adjustment machinery is most sensitive to. This is a measurement-error-in-the-running-variable threat to the identification of "publication bias," not merely a reproducibility gap.
**Severity:** High.
**Evidence:** "For 422 studies, both effect sizes and standard errors were directly available. For 1,828 studies, standard errors were computed using a normal approximation based on the confidence interval width. For 62 studies, standard errors were computed from the effect size and total sample size under the assumption of equal group sizes" (p.24, Statistical analysis). The diagnostics that drive every headline run on those SEs: funnel plots with "all Egger tests p < .0001" (p.5), z-curves built from z = SMD/SE (Figure 1, second row), PET-PEESE meta-regressions on the SE term (p.26), and selection weight functions keyed to the implied significance threshold (p.7).
**Why it matters (lane-specific):** RoBMA's selection models and PET-PEESE identify "publication bias" from the *joint* position of estimates relative to the significance boundary in (SMD, SE) space. If 82% of SEs are imputed from rounded CI widths or from an equal-group-size assumption that fails whenever allocation is unequal (common in waitlist/usual-care RCTs, the comparators defined on p.22), the z-statistics are systematically mismeasured precisely near z ≈ 1.96 — which is the discontinuity the method reads as selection. Differential SE error by SE source can manufacture or erase the "sharp discontinuities at the statistical significance criterion" (p.5) the paper interprets as bias. The authors never test sensitivity to SE provenance, and the borrowed Borenstein Eq. 4.20 equal-n assumption is asserted, not checked against reported allocation ratios.
**Fix:** Re-estimate the BF_bias, weight functions, and adjusted μ on the directly-reported-SE subset (the 422) alone, and report whether the "extreme publication bias" conclusion holds without the 1,890 reconstructed SEs. Add a flag for the equal-group-size cases and probe robustness to plausible allocation imbalance.

## Top 3 surviving criticisms after Round 2

**1. (Source: new/mine) Imputed standard errors are load-bearing for the publication-bias estimand and untested.**
Evidence: "For 1,828 studies, standard errors were computed using a normal approximation... For 62 studies, standard errors were computed from the effect size and total sample size under the assumption of equal group sizes" (p.24); diagnostics keyed to these SEs at "all Egger tests p < .0001" (p.5) and ω_{p<0.50} weight functions (p.7). Severity: High. Why: identification of selection at the significance boundary depends on SEs that are 82% reconstructed and never stress-tested; differential SE error sits exactly where the method infers bias.

**2. (Source: X, sharpened) Unit-of-analysis / dependence is undefined and the overlap bound is borrowed, not measured here.**
Evidence: "we were unable to track the origin of all extracted study-level estimates... the degree of overlap is likely less than 1% based on the assessment by Singh et al. (2025)" (p.20); models described as "three-level... BMA" clustering by meta-analysis (p.5, p.9, p.10), not by originating RCT. Severity: High. Why: the estimand presumes independent study-level units, but the same trial can recur across overlapping source meta-analyses; uncorrected dependence inflates effective N and biases the heterogeneity (τ) and bias diagnostics that carry the conclusions.

**3. (Source: Y, relocated to estimand/multiplicity) Subgroup "promising" cells are a post-hoc, multiplicity-uncontrolled, often single-meta-analysis estimand recast as recommendations.**
Evidence: "the evidence supports targeted, population- and intervention-specific recommendations" (p.2) vs. "the majority of subgroup analyses resulted in the absence of evidence" (p.18); the moderator analysis "did not show evidence of moderation by any of our moderators... Therefore, we performed subgroup analyses" (p.7), with subgroups such as "k = 9, m = 1" (exergaming, non-healthy older adults, Figure 2). Severity: Medium. Why: selecting the largest of dozens of noisy subgroup BFs after a null omnibus moderation test is winner's-curse selection; the parameter recommended is not the parameter that was validly identified.

## Devil's-advocate check on emerging consensus

The strongest defense of the paper against my top item is that RoBMA was designed to be robust to model uncertainty and the conclusion is *directional* (effects shrink toward zero), so even moderately mismeasured SEs would not flip a "shrinks to near-zero" finding for memory and executive function. That partly holds for the negative conclusions — but it does not rescue the affirmative claims (the BF_bias > 10^6 magnitude, the specific weight-function estimates, and the "promising" subgroups), which are quantitative and SE-sensitive. The consensus across X, Y, and me is therefore narrower than "the paper overclaims everywhere": the *negative*, heterogeneity-centered message is well-identified and the paper defends it carefully (p.7, p.17, p.18); the *positive* and *magnitude* claims rest on an untested SE-reconstruction and an undefined dependence structure.

## Bottom line after Round 2

The methodologically durable problems are about what is identified, not about tone: an undefined unit of analysis with borrowed (not measured) overlap, and a publication-bias estimand resting on standard errors that are 82% reconstructed and never stress-tested. These threaten the affirmative and magnitude claims (BF_bias, weight functions, "promising" subgroups), while the paper's core negative finding — that pooled means are uninformative under extreme heterogeneity — is correctly identified and appropriately hedged (p.7, p.17). The fix is sensitivity analysis on SE provenance and a row-level provenance/clustering-by-RCT audit, not a change of conclusion.

## Extraction caveats and unverified references

The PDF is image-based and not text-searchable (Grep returned no matches); all quotes were transcribed visually from the rendered pages and carry minor OCR risk in symbols/Greek letters (τ, ω, subscripts). Supplementary Figures S6–S9 were not individually read but appear to be the executive-function counterparts of S1–S5 and add no new methodological text. External methods references (RoBMA/Bartoš et al. 2022; Stanley & Doucouliagos PET-PEESE; van Erp et al. heterogeneity prior; Borenstein Eq. 4.20) and the OSF link (https://osf.io/egfzt) were not independently verified. I did not inspect the OSF repository or run any code. [EXTERNAL] tags: the claims that overlapping umbrella reviews commonly reuse the same trials, and that waitlist/usual-care RCTs often have unequal allocation, are general domain knowledge, not drawn from the paper.

## Audit Y
### Round 1
# Peer audit (anonymized)

## Top 5 criticisms

### 1. Retained sample counts do not reconcile across sections
- Severity: High
- Evidence: "215 out of 286" (p.4, Results); "82 (out of 107)" (p.5, General cognition), "40 (out of 62)" (p.9, Memory), and "91 (out of 117)" (p.10, Executive function); "2,239 effect size estimates" (p.23, Data extraction); "422", "1,828", and "62" (p.24, Statistical analysis).
- Why it matters: The outcome-specific extracted meta-analysis counts sum to 213, not 215, while the outcome denominators sum to 286. The standard-error provenance counts sum to 2,312, not the retained 2,239 estimates, so readers cannot tell which rows powered the reported analyses.
- Concrete fix: Add a reconciliation table from Singh et al. units to retained units by outcome, removal reason, and standard-error source. Label pre-removal and post-removal counts consistently.
- Devil's-advocate check: The strongest defense is that some counts refer to pre-cleaning data. That does not hold without explicit labeling, because the text presents these counts near the retained analysis sample.

### 2. Standard-error reconstruction and SMD harmonization are under-specified
- Severity: Medium
- Evidence: "Cohen's d or Hedge's g" (p.24, Statistical analysis); "normal approximation" (p.24, Statistical analysis); "equal group sizes" (p.24, Statistical analysis).
- Why it matters: The z-curves, weights, and publication-bias adjustments depend directly on standard errors and SMD definitions. The text does not specify exact formulas, conversion rules, CI handling, or rounding decisions.
- Concrete fix: Provide formulas, decision rules, and a row-level flag for each SE source and SMD type. Add sensitivity checks excluding reconstructed SEs and harmonizing Cohen's d to Hedges' g.
- Devil's-advocate check: The OSF may contain implementation code. Textual reproducibility still needs enough detail to understand which analytic choices were made.

### 3. The extraction validation can mask discrepancies by choosing the closest model
- Severity: Medium
- Evidence: "closest pooled estimate" (p.23, Data extraction); "2 meta-analyses were completely removed" (p.23, Data extraction).
- Why it matters: Selecting the closest result among several fixed/random-effects estimators can understate extraction or model-matching errors. The removed meta-analyses are not identified, so exclusion decisions are hard to audit.
- Concrete fix: Report, for every source meta-analysis, the source estimator, matched replication estimator, absolute discrepancy, and removal reason. Put the two removed cases in a supplement.
- Devil's-advocate check: Trying multiple estimators is pragmatic when source methods are unclear. It only becomes persuasive if the chosen match and residual discrepancy are disclosed.

### 4. The largest exercise category is a catch-all that limits subgroup reproducibility
- Severity: Medium
- Evidence: "attempted to extract" (p.23, Data extraction); "other ... 1278 estimates" (p.24, Data extraction); "other physical activity" (p.4, Introduction).
- Why it matters: More than half of estimates fall into a residual category, yet some conclusions refer to that category as if it were substantively interpretable. Without coding rules and original labels, readers cannot reproduce or understand those subgroup results.
- Concrete fix: Publish the coding codebook, original exercise labels, multi-label handling rules, and coder agreement. Break down the "other" category or avoid substantive claims about it.
- Devil's-advocate check: Broad bins preserve statistical power. That defense weakens when the paper moves from broad bins to targeted intervention recommendations.

### 5. Robustness and availability reporting stop short of text-level reproducibility
- Severity: Medium
- Evidence: "As a sensitivity analysis" (p.26, Meta-analytic ensembles); "default prior distributions" (p.27, Prior distributions); "Code and data are available" (p.20, Data Availability Statement).
- Why it matters: The manuscript varies the publication-bias model, but not visibly the priors, SE reconstruction, duplicate handling, extraction uncertainty, or subgroup coding. The availability statement gives a link but no manifest, version, script order, or expected outputs.
- Concrete fix: Add a reproducibility appendix with a file manifest, computational environment, script order, seeded outputs, and robustness table. Include prior, SE-source, coding, and leave-one-meta-analysis-out checks.
- Devil's-advocate check: The OSF repository may contain these details. The manuscript should still describe enough to let readers verify what was varied versus held fixed.

## Top 2 strengths

### 1. The paper openly reports extraction attrition and validation
Evidence: "missing study-information tables" (p.5, Results); "re-examined" (p.23, Data extraction); "retained for the analysis" (p.23, Data extraction). Why: This transparency helps readers see where the dataset narrows and where extraction checks occurred.

### 2. The paper reports heterogeneity and prediction intervals alongside pooled effects
Evidence: "prediction interval" (p.7, General cognition); "large harm and large benefit" (p.7, General cognition); "RoBMA_PSMA and RoBMA_WF" (p.8, Figure 2 note). Why: Presenting heterogeneity, prediction intervals, and two bias-adjustment variants reduces reliance on pooled means alone.

## Biggest blind spot

The biggest blind spot is dependence and overlap in the retained study-level data. The paper states that the authors were "unable to track" all origins (p.20, Discussion) and that overlap is "less than 1%" (p.20, Discussion), but the text does not show how that figure maps onto the retained estimates. Because the analysis treats extracted rows as study-level inputs, duplicate or dependent estimates can affect weights, heterogeneity, and publication-bias diagnostics. A row-level provenance table and overlap audit would make this limitation assessable.

## Bottom line

The paper is unusually strong in reporting publication-bias adjustments, prediction intervals, and extraction limitations. The main reproducibility weakness is accounting: several N and provenance counts do not reconcile, and key reconstruction/coding choices are not sufficiently specified in the text. The conclusions may still be directionally supported, but the manuscript needs a clearer audit trail from extracted rows to reported figures and subgroup claims.

## Extraction caveats

The markdown extraction contains mojibake in names and symbols, and figures appear as text descriptions rather than original graphics. I used the embedded page markers as locators. I did not run code, inspect the OSF repository, or verify the PDF against this extracted text.

## Unverified references

All external references and the OSF availability link were not independently verified.
### Round 2
# Peer audit (anonymized)
## Two strongest peer arguments
From Audit X, claim: Standard errors were mostly reconstructed, yet no SE-source sensitivity is reported. Evidence: “For 422 studies, both effect sizes and standard errors were directly available. For 1,828 studies, standard errors were computed using a normal approximation based on the confidence interval width. For 62 studies, standard errors were computed from the effect size and total sample size under the assumption of equal group sizes” (p.24, Statistical analysis). Why it survives: publication-bias diagnostics depend directly on effect/SE pairs, so the reconstructed-SE majority needs a restricted/direct-SE or reconstruction-method sensitivity.

From Audit Y, claim: The paper overstates robustness. Evidence: the manuscript says the methods “ensure the robustness of our results” (p.4, Introduction), but later concedes that QRPs “can result in over- or under-adjusted pooled effect size estimates” (p.20, Limitations). Why it survives: those statements conflict; the methods probe robustness across selected models but do not guarantee it.

## Two weakest or overstated peer arguments
From Audit X, claim: the 217/2,312 versus 215/2,239 counts are a discrepancy. Why it fails: p.23 describes a pre-removal and post-removal sequence, not necessarily an inconsistency. Counter-quote: “The necessary information was available for 217 out of the 286 meta-analyses (with 2,312 estimates)” and, after re-examination, “2 meta-analyses were completely removed… In total, 215 meta-analyses containing 2,239 effect size estimates were retained” (p.23, Data extraction).

From Audit Y, claim: the critique of Singh et al. is too categorical because the paper says heterogeneity and publication bias “cannot be evaluated.” Why it is overstated: the same paragraph narrows the point to study-level capture. Counter-quote: “Their meta-analysis-level synthesis provided a valuable overview of the literature but, by design, could not fully capture variability and publication bias among individual trials” (p.18, Discussion).

## One issue that neither X nor Y raised
Claim/severity: Medium — the SE-provenance counts do not reconcile with the retained analysis sample. Evidence: after exclusions, “215 meta-analyses containing 2,239 effect size estimates were retained for the analysis” (p.23, Data extraction), but the later SE-source categories are “422,” “1,828,” and “62” studies (p.24, Statistical analysis), summing to 2,312. Why: readers cannot tell whether SE-source proportions describe the analyzed dataset or the pre-removal extraction set; this matters because SE construction underlies z-curves, funnel plots, PET-PEESE, and RoBMA inputs. Fix: update the SE-source counts for the retained 2,239 estimates, or label them as pre-exclusion counts and provide post-exclusion counts by outcome.

## Top 3 surviving criticisms after Round 2
1. source new, claim: SE-source counts conflict with retained N. Evidence: “2,239 effect size estimates were retained” (p.23) versus “422,” “1,828,” and “62” SE-source counts (p.24). Severity: Medium. Why: this is a direct arithmetic/reproducibility gap in the methods.
2. source X, claim: reconstructed SEs and mixed SMD metrics need sensitivity checks. Evidence: “either Cohen’s d or Hedge’s g as reported” and only “422” direct SEs versus “1,828” CI-derived and “62” equal-group-assumption SEs (p.24). Severity: Medium. Why: the main bias-adjustment evidence is SE-dependent.
3. source Y, claim: robustness language should be narrowed. Evidence: “ensure the robustness of our results” (p.4) versus “over- or under-adjusted pooled effect size estimates” (p.20). Severity: Medium. Why: the limitation undercuts a guarantee-style robustness claim.

## Devil's-advocate check on emerging consensus
The manuscript does report a useful extraction validation: “The correctness of the extracted study-level estimates was assessed by comparing” reported and recomputed pooled estimates (p.23), and it includes RoBMA_WF as “a sensitivity analysis” (p.26). That softens the critique, but it does not resolve the unreconciled SE-source N or show that conclusions are stable across SE reconstruction choices.

## Bottom line after Round 2
The broad evidence pattern is not overturned by the peer critiques, but the exact adjusted estimates are less reproducible than the manuscript implies. The revision should reconcile all N counts, add SE-construction sensitivity checks, and replace guarantee-style robustness wording with model-specific sensitivity language.

## Extraction caveats and unverified references
The manuscript text contains mojibake in names and symbols; page locators use the bracketed `[p.N]` markers. Figures are textual descriptions, not inspectable images. I did not verify the OSF link or external references.

## Audit Z
### Round 1
# Peer audit (anonymized)
## Top 5 criticisms
### 1. The abstract converts subgroup signals into recommendation language too quickly
- Severity: High
- Evidence: "Subgroup analyses identify specific population-intervention combinations with more consistent benefits... the evidence supports targeted, population- and intervention-specific recommendations" (p.2, Abstract); "While the majority of subgroup analyses resulted in the absence of evidence, several subgroups showed promising evidence in favor of an effect" (p.18, Discussion).
- Why it matters: "Identify" and "supports recommendations" read stronger than the paper's own "promising evidence" framing. [EXTERNAL] Recommendation claims usually require a defined decision context, threshold, and replication standard, not only subgroup contrasts in a heterogeneous meta-meta-analysis.
- Concrete fix: Recast as "subgroup analyses generate/prioritize candidate population-intervention combinations for future testing or cautious evidence summaries." Reserve "recommendations" for combinations with stable evidence across multiple meta-analyses and prediction intervals that do not point both ways.
- Devil's-advocate check: The authors do qualify the claim by rejecting "generalized" enhancement and emphasizing targeted recommendations. That defense partly holds, but the abstract still upgrades exploratory subgroup evidence into prescriptive language.

### 2. The paper overstates robustness
- Severity: Medium
- Evidence: "by combining study-level re-analysis with advanced meta-analytic methodology that explicitly accounts for model uncertainty, between-study heterogeneity, and publication bias, we ensure the robustness of our results" (p.4, Introduction); "This may indicate questionable research practices... which can result in over- or under-adjusted pooled effect size estimates" (p.20, Limitations).
- Why it matters: The limitations concede that the adjustment can misfire under plausible data-generating processes. "Ensure" therefore overclaims what the methods establish.
- Concrete fix: Replace "ensure the robustness" with "probe robustness across bias-adjustment models" or "strengthen sensitivity to publication bias and heterogeneity." Keep robustness as an empirical result only where RoBMA_PSMA and RoBMA_WF agree.

### 3. The claimed "comprehensive" contribution outruns the extractable evidence base
- Severity: Medium
- Evidence: "This study advances the exercise-cognition literature by providing a comprehensive, study-level perspective that earlier meta-meta-analyses and umbrella reviews could not offer" (p.4, Introduction); "We successfully extracted 2,239 study-level estimates and characteristics from 215 out of 286 (74.4%) individual meta-analyses" (p.4-p.5, Results).
- Why it matters: A large extractable subset is valuable, but "comprehensive" suggests fuller coverage than the design delivers. The missing quarter of meta-analyses could matter especially for claims about the field-wide evidentiary map.
- Concrete fix: Call it "the largest study-level re-analysis of extractable meta-analyses from Singh et al. (2025)" and explicitly state that the contribution is conditional on available study tables, forest plots, and supplements.

### 4. The critique of prior meta-meta-analysis is too categorical
- Severity: Medium
- Evidence: "The extent of between-study heterogeneity and publication bias cannot be evaluated when combining meta-analytic summary estimates rather than study-level estimates, as done in Singh et al. (2025)" (p.18, Discussion); "We find that each meta-analysis rarely results in strong evidence for either the presence or absence of publication bias, especially when examining the smaller meta-analyses" (p.14-p.15, Meta-analysis-level re-analysis).
- Why it matters: The manuscript has a strong point about trial-level variation, but "cannot be evaluated" overstates the contrast with prior work. Its own re-analysis shows evidence limits even after returning to individual meta-analyses.
- Concrete fix: Narrow the claim to "cannot be evaluated at the trial level with the same bias-adjustment and prediction-interval framework." This preserves the contribution without making the prior literature sound methodologically void.

### 5. The policy framing can be read as broader than the measured outcome scope
- Severity: Low
- Evidence: "major public health agencies promote exercise for brain health, including the Centers for Disease Control and Prevention... and the World Health Organization" (p.3, Introduction); "this extensive study-level meta-meta-analysis demonstrates insufficient evidence to recommend exercise unconditionally for cognitive benefits" (p.20, Discussion).
- Why it matters: The paper studies cognitive-test outcomes in RCT meta-analyses, while the framing invokes public-health agencies and "brain health." That juxtaposition could invite readers to overread the results as a broader challenge to exercise guidance.
- Concrete fix: State explicitly that the implication concerns claims of cognitive enhancement on the analyzed outcomes, not recommendations based on physiological health, dementia prevention, mental health, or other brain-health endpoints.

## Top 2 strengths
### 1. The paper directly confronts generalized exercise-cognition claims with study-level evidence. Evidence: "This granularity allows us to move beyond the assumption of a generalized effect" (p.4, Introduction). Why: That is a real contribution because the paper targets the exact level at which broad claims become fragile.
### 2. The discussion repeatedly reins in pooled-effect interpretation. Evidence: "the pooled effects provide little information on their own and should be interpreted with extreme caution" (p.18, Discussion). Why: This strengthens the paper's contribution by making heterogeneity central rather than treating it as a technical footnote.

## Biggest blind spot
The biggest blind spot is the bridge from evidence synthesis to action language. The manuscript says "the evidence supports targeted, population- and intervention-specific recommendations" (p.2, Abstract) and later that "more specific recommendations regarding exercise categories for specific populations are needed" (p.20, Discussion). But it also says "the majority of subgroup analyses resulted in the absence of evidence" (p.18, Discussion), which leaves the recommendation threshold unstated. The paper would be stronger if it separated evidence-ranking claims from policy or practice recommendations.

## Bottom line
The paper makes a useful corrective contribution by showing that broad exercise-cognition claims weaken after publication-bias adjustment and heterogeneity accounting. Its main overclaim is not the negative conclusion; it is the move from "promising evidence" (p.18, Discussion) to "targeted... recommendations" (p.2, Abstract). The manuscript should describe its contribution as a re-analysis that narrows and prioritizes claims, not as a basis that already supports specific recommendations. Tightening "comprehensive," "robust," and policy-facing language would make the contribution more credible.

## Designated minority objection
The minority objection is that the paper may lean too hard against general cognitive benefit despite its own finding that the adjusted general-cognition estimate is positive: "SMD = 0.227 [0.116, 0.330]... BF10 = 400.6" (p.7, General cognition). A reader could argue that "strong recommendations that exercise improves cognition are not warranted" (p.17, Discussion) needs more nuance for general cognition specifically.

## Extraction caveats
The extracted manuscript text contains mojibake in names, dashes, Greek letters, and some symbols. Page locators were taken from the manuscript's bracketed `[p.N]` markers. Figures appear as textual figure descriptions rather than inspectable image files.

## Unverified references
Singh et al. (2025), Ciria et al. (2023/2024), Dupuy et al. (2024), Garrett et al. (2024), and Wu et al. (2025) were not independently verified outside the manuscript text.
### Round 2
# Peer audit (anonymized)

## Two strongest peer arguments

From Audit X, claim: The paper elevates subgroup findings from exploratory-looking analyses into “targeted recommendations.” Evidence quote+locator: “The moderator analysis did not show evidence of moderation by any of our moderators ... Therefore, we performed subgroup analyses by population type, population age, and exercise category” (p.7, General cognition), while the abstract concludes that “the evidence supports targeted, population- and intervention-specific recommendations” (p.2, Abstract). Why it survives: This is squarely an interpretation overclaim. Even if the subgroup estimates are useful, the paper’s own sequence makes them look like exploratory decomposition after weak moderator evidence, so “promising evidence” should not become recommendation language.

From Audit Y, claim: The residual “other” exercise category cannot support targeted intervention claims. Evidence quote+locator: The authors define “other” as “all estimates not matching any of the previous categories, 1278 estimates” (p.24, Data extraction), yet the introduction lists “other physical activity for executive function in non-healthy children and adolescents” among “promising evidence for the benefit” (p.4, Introduction). Why it survives: A catch-all bin can be reported descriptively, but it cannot carry a targeted contribution unless readers know what interventions it contains and whether the category is coherent.

## Two weakest or overstated peer arguments

From Audit X, claim: Publication-bias adjustment is presented as recovered causal truth. Why it fails: The critique catches a real abstract-level problem, but “causal truth” overstates what the manuscript says. Counter-quote: the paper explicitly warns that QRPs “can result in over- or under-adjusted pooled effect size estimates” (p.20, Discussion) and says “the pooled effects provide little information on their own and should be interpreted with extreme caution” (p.18, Discussion).

From Audit Y, claim: Retained sample counts not reconciling is a high-severity contribution problem. Why it fails: This is an accounting and extraction-audit problem, not primarily a contribution or policy-overclaim problem unless it is tied to a specific interpretive conclusion. Counter-quote: the paper does disclose attrition: “data could not be obtained due to missing study-information tables, forest plots, or supplementary information” (p.5, Results), and “215 meta-analyses containing 2,239 effect size estimates were retained for the analysis” (p.23, Data extraction).

## One issue that neither X nor Y raised

Claim: The contribution is positioned as more comprehensive and robust than the design warrants. Severity: Medium. Evidence quote+locator: The manuscript claims, “This study advances the exercise–cognition literature by providing a comprehensive, study-level perspective” and says the approach lets the authors “ensure the robustness of our results” (p.4, Introduction). But the methods state, “This project extends the just-published meta-meta-analysis by Singh et al. (2025) and therefore relies on its search, screening, and inclusion procedures” (p.21, Methods), and the results note that some “data could not be obtained due to missing study-information tables, forest plots, or supplementary information” (p.5, Results). Why: The contribution is valuable, but it is a re-analysis of the extractable portion of another review’s evidence base, not a fully independent comprehensive map. Fix: Recast the contribution as a study-level re-analysis of extractable RCT estimates from Singh et al. and replace “ensure the robustness” with narrower language about robustness within the specified RoBMA ensembles and available data.

## Top 3 surviving criticisms after Round 2

1. Source X, claim: Targeted recommendation language exceeds the evidential status of the subgroup findings. Evidence: “Subgroup analyses identify specific population-intervention combinations with more consistent benefits” (p.2, Abstract), after “The moderator analysis did not show evidence of moderation by any of our moderators” (p.7, General cognition). Severity: High. Why: The paper can report hypotheses or promising cells, but recommendation language asks the subgroup analysis to do more than the manuscript shows.

2. Source Y, claim: The “other” category weakens the scope of the claimed intervention-specific contribution. Evidence: “other (all estimates not matching any of the previous categories, 1278 estimates)” (p.24, Data extraction), contrasted with “other physical activity for executive function in non-healthy children and adolescents” (p.4, Introduction). Severity: Medium. Why: A residual category is not an intervention type; treating it as one blurs the claimed map of “under what conditions” exercise helps.

3. Source new, claim: The paper overstates comprehensiveness and robustness in its contribution framing. Evidence: “comprehensive, study-level perspective” and “ensure the robustness of our results” (p.4, Introduction), while the project “relies on” Singh et al.’s search and inclusion procedures (p.21, Methods). Severity: Medium. Why: The design supports a strong secondary re-analysis contribution, but not the broader implication of a fully comprehensive, independently robust evidence map.

## Devil's-advocate check on emerging consensus

The strongest defense is that the paper repeatedly resists broad claims: “strong recommendations that exercise improves cognition are not warranted” (p.17, Discussion), and “the pooled effects provide little information on their own and should be interpreted with extreme caution” (p.18, Discussion). That caution makes the negative contribution credible. The objection survives because the same manuscript still says the evidence supports “targeted, population- and intervention-specific recommendations” (p.2, Abstract), which is stronger than “hypothesis-generating subgroup signals.”

## Bottom line after Round 2

The central negative message is well supported: the manuscript documents “substantial publication bias and extreme between-study heterogeneity” (p.17, Discussion). The positive contribution needs tightening: “promising evidence” (p.18, Discussion) should be framed as provisional subgroup evidence, not as a basis for targeted recommendations.

## Extraction caveats and unverified references

The markdown extraction contains mojibake in names and symbols, and figures are represented as text descriptions rather than original graphics. I used embedded page markers as locators. I did not verify the OSF repository, the PDF, or external references.

## Designated minority objection