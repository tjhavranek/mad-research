## Audit X
### Round 1
I now have the complete paper (71 pages: main text pages 1-41, supplementary S1-S18 through page 71). I have everything needed for a methodological/causal audit. Let me reason through the key issues in my lane.

The paper's central causal claim is laid out clearly. Let me work through the estimand and identification logic before drafting.

**The core causal argument the paper makes:**
1. Raw beauty premium ~5% (4.3% per estimate).
2. Publication bias correction â†’ ~3%.
3. Controlling for cognitive ability (or DiD) â†’ ~1%, not significantly different from zero.
4. Interpretation: beauty does not affect labor-market outcomes "per se"; it operates "via its correlation with other characteristics" (p.2). The remaining premium is "explained away."

**Key methodological tension I can see â€” and the paper itself half-acknowledges it (p.13):** "cognitive ability could act as a mediator rather than a confounder; thus... beauty could remain economically significant as a driver of human capital acquisition." This is the crux. The paper's headline ("beauty is not important in the labor market per se but via its correlation with other characteristics," p.2; abstract "explain away most of the mean beauty premium") treats conditioning on cognitive ability as removing confounding. But if beauty â†’ cognitive ability/schooling â†’ earnings (the developmental story the paper itself endorses on pp.6, 13), then cognitive ability is a mediator on the causal path, and controlling for it produces over-control bias / blocks part of the total effect. The "1%" is then a controlled direct effect, not the total causal effect of beauty, and the abstract's "explain away" framing conflates the two estimands.

Let me develop each criticism with exact quotes and locators.

Now let me verify the specific quotes I'll use are exact, by re-reading the critical passages.

The abstract (p.1): "two factors combine to explain away most of the mean beauty premium reported in the literature. First, correcting for publication bias reduces the premium by at least a third. Second, controlling for cognitive ability renders the premium small (mean = 1.1%; 95% CrI = âˆ’0.8%, 3.0%) for all occupations except sex workers, where appearance is a direct input."

p.2 intro: "These findings suggest that beauty is not important in the labor market per se but via its correlation with other characteristics."

p.13 discussion: "Under this developmental framework, cognitive ability could act as a mediator rather than a confounder; thus, while we do not find strong evidence of substantial direct discrimination, beauty could remain economically significant as a driver of human capital acquisition."

These are consistent with what I read. Let me also pin down the cross-sectional dependence / "treats each estimate as the unit" issue, the funnel-asymmetry identifying assumption discussion, the attenuation-bias OLS-vs-IV inference, and the subgroup credible-interval mechanics.

Let me reason about the devil's advocate for my top criticism (mediator vs confounder). The paper does flag the mediator possibility on p.13 and even says beauty "could remain economically significant." So is the criticism blunted? Not for the headline claims: the abstract and intro and the Hamermesh-contrast sentence ("This contrasts the frequently cited conclusion... that 'beauty pays'", p.5) all assert the strong "explain away / not important per se" reading without the mediator caveat. The caveat is buried in one Discussion sentence and is not carried into the abstract, intro, or the policy framing ("consequences for anti-discriminatory legislation," p.2). So the estimand is genuinely ambiguous and the headline picks the interpretation that the paper's own evidence cannot license. The devil's advocate partially holds (they did mention it) but does not rescue the framing â€” which is exactly a methodologist's concern about what parameter is being claimed.

Let me also double-check the "no test of the mediator vs confounder" point â€” does the paper run any diagnostic that could distinguish them? It controls for education, age, ethnicity separately (Table 3 / S10), and finds cognitive control is what matters. But it never reports the beautyâ†’cognitive-ability association in primary data, never does a formal mediation/decomposition, and the developmental claim ("attractive children achieve slightly higher test scores," p.6) is supported only by external citations, not by the meta-analytic data. That is a genuine "diagnostic the paper should but did not run."

I have enough to write all five criticisms with grounding. Let me also confirm the productivity-vs-earnings discrimination test logic and the cross-study independence assumption, and the funnel/MAIVE exclusion-restriction point.

I have sufficient grounding for all points. Here is my review.

# Peer audit (anonymized)

## Top 5 criticisms

### 1. The headline estimand ("beauty does not matter per se") treats cognitive ability as a confounder while the paper's own developmental story makes it a mediator; conditioning on it then yields a controlled direct effect, not the total causal effect that the abstract claims to "explain away."
- Severity: High
- Evidence: Abstract: "two factors combine to explain away most of the mean beauty premium... Second, controlling for cognitive ability renders the premium small (mean = 1.1%...)" (p.1). Intro: "beauty is not important in the labor market per se but via its correlation with other characteristics" (p.2). Yet the Discussion concedes the opposite causal structure: "Under this developmental framework, cognitive ability could act as a mediator rather than a confounder; thus... beauty could remain economically significant as a driver of human capital acquisition" (p.13), preceded by "attractive children could receive more encouragement from teachers and peers, attain slightly higher test scores, and complete more education" (p.13).
- Why it matters: If beauty â†’ schooling/measured ability â†’ earnings, then controlling for cognitive ability blocks a causal pathway (over-control / mediator bias). The "1.1%" is then a controlled direct effect (effect of beauty holding ability fixed), whereas "explain away the beauty premium" and "not important per se" are statements about the total effect. These are different estimands; the evidence licenses only the former, but the abstract, intro, and the policy framing ("consequences for anti-discriminatory legislation," p.2) assert the latter.
- Concrete fix: State the target estimand explicitly (total effect vs controlled direct effect). Either reframe the headline as "the *direct* return to appearance, holding measured ability fixed, is small" or attempt a decomposition (e.g., report the beautyâ†’ability association and bound the indirect path). At minimum, carry the p.13 mediator caveat into the abstract and intro so the claim matches the identified parameter.
- Devil's-advocate check: The strongest defense is that the paper *does* flag the mediator possibility (p.13) and even says beauty "could remain economically significant," so it has not over-claimed. This does not hold for the load-bearing sentences: the abstract ("explain away"), the intro ("not important... per se"), and the Hamermesh contrast ("This contrasts the frequently cited conclusion... that 'beauty pays'," p.5) all adopt the strong total-effect reading with no caveat. The mediator concession appears once, mid-Discussion, and is not propagated to the claims a reader will cite. The ambiguity is therefore real and consequential, not resolved.

### 2. The mediator-vs-confounder question is never tested with a diagnostic the design could support; the developmental channel is asserted from external citations, not from the meta-analytic data.
- Severity: High
- Evidence: The causal interpretation rests on "appearance proxies measured skill differences reported in primary studies, with little evidence of an additional payoff once information about ability is available" (p.13) and on developmental claims sourced only to outside studies ("attractive children... attain slightly higher test scores, and complete more education[32]," p.13; "In longitudinal data, more attractive adolescents tend to complete more education and earn higher wages as adults[31]," p.6). The 35 coded moderators (Table 3, Table S10) include education, age, and ethnicity controls but the paper reports no estimate of the beautyâ†’cognitive-ability or beautyâ†’schooling association within its own 1,159 estimates, and runs no formal mediation/decomposition.
- Why it matters: The entire reinterpretation of Hamermesh hinges on whether the cognitive-ability control removes confounding or removes mediation. Without any test that distinguishes the two (e.g., whether the premium drops *more* in studies where ability is plausibly downstream of beauty), the causal story is observationally equivalent to "appearance has a genuine effect that flows through human capital," which the paper concedes (p.13) but does not rule out.
- Concrete fix: Exploit the coded structure: compare the cognitive-control coefficient across studies stratified by whether ability is measured *contemporaneously* vs *pre-treatment* (childhood IQ), since conditioning on pre-treatment ability is confounder control whereas conditioning on adult ability is mediator control. Report the sign/magnitude of any beautyâ€“ability gradient available in primary data. If neither is feasible, downgrade the causal language to associational.
- Devil's-advocate check: The defense is that meta-analysis cannot recover micro-level mediation because it only observes reported coefficients, so demanding a decomposition is unfair. Partly fair â€” but the paper itself codes the *timing/type* of ability controls (objective vs self-rated; "Objective cog. control or DID," Table 2) and could at least test whether childhood-ability controls behave differently from adult-ability controls. The diagnostic is within reach of the existing coding, so the omission is a genuine gap, not an impossibility.

### 3. The "earnings â‰ˆ productivity premium" test is offered as evidence against taste-based discrimination, but the comparison rests on an untested assumption that the two subsamples are otherwise exchangeable.
- Severity: Medium
- Evidence: "the beauty premium is similar for earnings and (imperfect) measures of productivity... If the beauty premium reported in the literature was due to taste-based discrimination by employers, we would expect a larger correlation of beauty with earnings than with productivity" (p.2); "On average, the mean beauty premium for earnings is identical to the mean premium for productivity: 4.3%, which is consistent with no or little taste-based discrimination by employers" (p.18); Table 2 reports earnings = 0.24 [0.00, 1.30] vs productivity = 0.14 [âˆ’0.49, 1.24] (simple RoBMA).
- Why it matters: The inferential leap "equal premiums â‡’ no taste-based discrimination" requires that earnings-based and productivity-based estimates come from comparable occupations, raters, and designs; otherwise the equality could reflect compositional offsetting (e.g., productivity proxies concentrated in different occupations, Table S18) rather than absence of taste discrimination. The productivity measures are also self-described as "imperfect" (p.2), so a downward bias in measuring productivity could mask a true earnings>productivity gap.
- Concrete fix: Show the earnings-vs-productivity contrast *within* occupation/industry cells (or as an interaction in the BMA meta-regression) rather than as two marginal means, and discuss how measurement error in productivity proxies could attenuate the very gap the test looks for.
- Devil's-advocate check: The defense is that this is one of three converging arguments (publication bias, ability control, earningsâ‰ˆproductivity) and is presented as suggestive, not dispositive ("consistent with"). Reasonable â€” the language is hedged. But the test is repeatedly used to rule *out* a named causal mechanism (employer taste), and the marginal-means design cannot separate "no taste discrimination" from "compositional confounding plus mismeasured productivity," so it carries less identifying weight than the prose implies.

### 4. The preferred publication-bias corrections (funnel/FAT-PET, simple RoBMA) treat each of 1,159 estimates as the unit of analysis, embedding a within-study independence assumption the paper elsewhere shows is violated.
- Severity: Medium
- Evidence: "simple RoBMA suggests a mean beauty premium of 0.2, while three-level RoBMA yields 3.1" (p.9); the representative correction is "the median value reported in Panel A: 2.9 corresponding to the simple OLS" funnel regression (p.23), and Table S6's OLS/BE estimates pool all 1,159 estimates. The paper acknowledges the dependence â€” "the three-level one that treats each study as the unit and accounts for within-study correlation among estimates" (p.9) â€” and the two units disagree materially (simple RoBMA 0.21 vs 3lvl 3.13, Table 1, Part 1).
- Why it matters: When dozens of correlated estimates per study are treated as independent (some studies contribute 200+ estimates, e.g., general population n=435 from 23 studies, Table 2), the funnel regression's standard errors and its precision-weighting are driven by a few prolific studies, and the identifying "estimates independent of their SEs" condition is evaluated on a pseudo-sample. The headline "around 3" is selected from the *estimate*-level OLS (2.9), while the *study*-clustered three-level RoBMA gives a similar number only by coincidence; the estimate-unit simple RoBMA collapses to 0.2.
- Concrete fix: Make the study- (or database-) clustered specification the primary one throughout, and reconcile why the representative number is taken from an estimate-unit regression when the estimate-unit RoBMA (0.21) and study-unit RoBMA (3.13) bracket it so widely. Report how many studies drive the funnel slope.
- Devil's-advocate check: The defense is that SEs are clustered at the study level in Table S6 and that three-level RoBMA and MAIVE are reported precisely to handle dependence, so the issue is addressed. Largely fair for inference. But the *point estimate* chosen as representative (2.9, p.23) still comes from an estimate-weighted regression, and the choice is justified by proximity to the three-level RoBMA rather than by a principled unit-of-analysis argument â€” so dependence is handled for variance but not transparently for the headline location.

### 5. The funnel-asymmetry identification is internally contested: the paper documents that estimateâ€“SE correlation can be non-bias (IV by construction, p-hacked SEs, method choice) yet still reports funnel-based numbers as the bias correction.
- Severity: Medium
- Evidence: "a variety of mechanisms can produce a correlation between estimates and standard errors even in the absence of publication bias. For example, Keane and Neal[20] show that, with instrumental variables, the correlation arises naturally" (pp.3â€“4); "if researchers p-hack standard errors (e.g. by changes in clustering), the correlation resulting from this form of p-hacking is not associated with any bias in the mean reported estimate, and funnel asymmetry tests introduce a downward bias that did not exist before" (p.22). Despite this, the funnel-based FAT-PET intercept (2.9) is adopted as the representative correction (p.23), and Table S4 shows estimates and SEs remain correlated (0.550 [0.43, 0.61]) even after the selection-model correction.
- Why it matters: The paper correctly diagnoses that the funnel slope is not a clean estimate of publication bias in this literature (IV-induced correlation, p-hacked clustering), which means the FAT-PET intercept is not guaranteed to identify the bias-free mean. Choosing that intercept as "representative" sits in tension with the paper's own argument for MAIVE, which exists precisely because the funnel assumption fails here.
- Concrete fix: Either lead with MAIVE/RTMA (which the paper built to address the spurious-precision problem) as the representative correction, or justify why the contested funnel intercept is preferred over the estimator designed to fix the contamination it identifies. Report the share of IV-based estimates and re-run the funnel test excluding them.
- Devil's-advocate check: The defense is that MAIVE (3.1) and the funnel OLS (2.9) and three-level RoBMA (3.1) all cluster near 3, so the contamination does not change the bottom line. This is the paper's strongest rebuttal and it largely holds for the *publication-bias* step â€” the corrected mean is robustly "around 3." The criticism therefore lands less on the number and more on the rhetorical choice to anchor on the one estimator the paper itself argues is least identified.

## Top 2 strengths

### 1. The paper anticipates and pre-empts the core identification threat to its own publication-bias inference (spurious precision / endogenous standard errors) and deploys an estimator (MAIVE) targeted at it, rather than relying on the standard FAT-PET assumption uncritically.
- Evidence: "Precision can be p-hacked... which introduces reverse causality. To address these problems we use the novel Meta-Analysis Instrumental Variable Estimator (MAIVE)[21], which accounts for potential spurious precision and various forms of p-hacking" (p.4); "MAIVE uses inverse sample size used in the primary study as an instrument for the reported squared standard error" (p.22). This is a methodologically serious response to a real threat to the estimand, and the convergence of MAIVE, FAT-PET, and three-level RoBMA near 3 strengthens the publication-bias conclusion.

### 2. The bias-correction conclusion is stress-tested across estimators that rest on *different* identifying assumptions (selection models, PET-PEESE, MAIVE, RTMA, z-curve), so the "premium falls to ~3%" result does not hinge on a single assumption.
- Evidence: Table 1 reports selection model (0.49), PET-PEESE (0.34), MAIVE (3.05), RTMA (2.27), simple RoBMA (0.21), three-level RoBMA (3.13); the text frames these as spanning the no-p-hacking vs p-hacking assumption space ("all these models share a common assumption: estimates in the literature are not actively p-hacked... For this reason we also report... MAIVE... and RTMA," pp.9â€“10). Triangulating across assumption sets is exactly the right defense against any one model's identification failure, and the z-curve diagnostic (EDR 0.31 vs ODR 0.47, p.24) provides an independent quantification of selection.

## Biggest blind spot
The paper's single largest unaddressed identification problem is the estimand ambiguity created by conditioning on cognitive ability. The whole reinterpretation of the literature â€” that beauty "is not important per se" (p.2) and the premium can be "explain[ed] away" (p.1) â€” depends on reading the cognitive-ability control as confounder adjustment, yet the paper's own developmental narrative (pp.6, 13) makes ability a downstream mediator, in which case the adjusted "1.1%" is a controlled direct effect that mechanically understates the total causal effect of beauty. The paper notices this exactly once (p.13) but neither tests it nor lets it temper the headline, and it provides no diagnostic (e.g., separating childhood/pre-treatment ability controls from contemporaneous-ability controls, or any mediation decomposition) that could adjudicate confounder vs mediator. Because the policy stakes the paper invokes â€” anti-discrimination law and accident compensation (p.2) â€” turn on the *total* return to appearance, not the direct-holding-ability return, the gap between the parameter estimated and the parameter claimed is the most consequential weakness in the causal lane.

## Bottom line
Methodologically this is a careful, assumption-aware meta-analysis whose publication-bias conclusion ("premium falls from ~5% to ~3%") is well-triangulated across estimators with distinct identifying assumptions and is genuinely credible. The causal step beyond that â€” "controlling for cognitive ability shows beauty does not matter per se" â€” is not identified at the level the abstract claims, because the paper's own framework casts cognitive ability as a mediator, turning the conditional estimate into a controlled direct effect rather than the total effect the headline describes. The fix is primarily interpretive (state the estimand, propagate the p.13 caveat, and where possible test confounder-vs-mediator using the existing coding) and would not require new data. As written, the publication-bias contribution is solid; the "beauty is unimportant" contribution rests on a conditioning choice the paper has not defended against the over-control reading.

## Extraction caveats
The PDF rendered cleanly across all 71 pages (main text 1â€“41, References through 41, Supplementary S1â€“S18 through 71); tables and figures were legible. I did not independently verify the contents of the online appendix at meta-analysis.cz/beauty (O*NET coding rules for interpersonal intensity / output measurability), which the paper repeatedly references for observation-level classification (Table S18); my comments on those indices rely only on the in-paper descriptions.

## Unverified references
None. (I did not check arithmetic or citation accuracy, which are outside the methodological lane and assigned to other reviewers; I made no [EXTERNAL] factual claims requiring verification.)
### Round 2
Let me verify two specific anchors before finalizing: the exact Table 4 subgroup CIs (Politicians, Scientists) and the funnel-endogeneity passage wording, both of which are load-bearing for my lane.

The Table 4 (p.34) values I can read: "Politicians 1.06 [âˆ’8.41, 10.52]", "Scientists 1.58 [âˆ’0.35, 7.29]" â€” confirmed. And p.21-22 on the endogeneity problem in funnel asymmetry is confirmed ("we face a classical endogeneity problem in our meta-analysis specifications," p.22). I have everything I need.

# Peer audit (anonymized)

## Two strongest peer arguments

**1. From Audit Y â€” The headline estimand ("beauty is not important per se") is not the estimand the design identifies, because the controls may absorb a mediating channel.**
Claim: the paper conditions on cognitive ability and reports the residual as the causal verdict on beauty, but its own theory makes cognitive ability a plausible *mediator* of beauty's total effect, not a confounder. Evidence: "These findings suggest that beauty is not important in the labor market per se but via its correlation with other characteristics" (p.2, Introduction), set against the paper's own concession "Under this developmental framework, cognitive ability could act as a mediator rather than a confounder; thus, while we do not find strong evidence of substantial direct discrimination, beauty could remain economically significant as a driver of human capital acquisition that the labor market later rewards" (p.13, Discussion), and the developmental premise "Attractive children achieve slightly higher test scores and attain more schooling" (p.6, Conceptual Background). Why it survives: this is the core estimand problem and it is squarely in my lane. Conditioning on a post-treatment mediator changes the target parameter from a *total* effect of beauty to a *controlled direct* effect (net of the cognitive pathway). If beauty causally raises measured cognitive skill, then "control for cognitive skills reduces the beauty premium" (p.10) is partly mechanical attenuation of the channel of interest, and the headline interpretation ("not important per se") does not follow from the estimate. The paper concedes the mechanism but does not reconcile it with the abstract-level claim, and runs no mediation/decomposition test (e.g., it does not estimate the beautyâ†’cognitionâ†’earnings path it posits). The survival is reinforced by the BMA design itself: "Cognitive skill control" enters with posterior mean âˆ’2.265 and PIP 1.000 (Table 3, p.32), i.e., the single largest conditioning effect is on a variable the paper itself flags as possibly intermediate.

**2. From Audit Y â€” The taste-discrimination rejection over-extends because the "productivity" outcomes are themselves appearance-contaminated.**
Claim: parity of the beauty premium across earnings and productivity is offered as evidence against employer taste-based discrimination, but the productivity proxies are subjective/evaluator-mediated, so parity cannot separate true productivity from appearance-driven evaluation. Evidence: "The beauty premium is similar for earnings and productivity, a fact inconsistent with discrimination based on employer tastes for beauty" (p.1, Abstract), against the broad outcome definition "Productivity in this context can be measured as sales, research outcomes, study outcomes, electoral success, etc." (p.18, Methods 4.1). Why it survives: this is an identification-of-mechanism problem, not a tone complaint. The discrimination test rests on an exclusion-type assumption â€” that "productivity" outcomes are free of appearance-driven evaluator bias â€” which the paper's own categories violate. Electoral success (votes), teaching/study outcomes (student/teacher evaluations), and athletic "TV viewership" outcomes are exactly the channels the halo-effect literature the paper cites (Zebrowitz et al., p.6) says are contaminated by appearance. The paper's own subgroup table shows productivity proxies span low-measurability, subjective outcomes (Table S18, p.69: "Low output measurability... mostly subjective, composite"). If both "earnings" and "productivity" outcomes carry the same appearance-driven evaluation bias, parity is *consistent* with taste/evaluator discrimination, not inconsistent with it. The paper hedges the proxies as "imperfect" (p.2) but still draws the decisive inference in the abstract, so the gap between assumption and claim is real.

## Two weakest or overstated peer arguments

**1. From Audit X â€” "The fully adjusted cognitive/DID/quasi-experimental sample is ambiguously defined" (claimed High).**
Why it fails (as a substantive identification flaw): the supplementary text I read resolves the alleged ambiguity. Audit X argues the "395" count and the IV-inclusive definition in Table S18 leave it "unclear whether the headline fully adjusted estimates include IV estimates." But Table S18 (p.71) gives an explicit, single definition: "objective=1 or quasi-exp=1: Subsample based on studies that either include an objective cognitive measure or rely on quasi-experimental identification strategies (e.g., random assignment, difference-in-differences, instrumental variables)" â€” IV is *included* by definition. Table S16 (p.63) and S17 (p.66) both report this row as "objective=1 or quasi-exp=1" with Obs = 395 and a 3lvl variant, consistent across tables. Counter-quote: the headline 1.1% figure is built on "controlling for cognitive ability either via an explicit inclusion of the control or via difference-in-differences" (p.12) and the BMA fitted value, not on the S16 "395" cognition-or-quasi row, so the supposed inconsistency does not touch the headline number. This is at most a labeling/traceability nit (a documentation lane issue), not a methodological identification defect, and it is not High severity. Note also that items 3, 4, and 5 of Audit X (wrong table pointer S8 vs S6; reversed earnings/non-earnings labels in Table S3; bare repository URL) are reproducibility/citation/arithmetic issues that belong to other reviewers' lanes, not mine.

**2. From Audit Y â€” "The policy stakes exceed what the synthesis identifies" (claimed Medium).**
Why it is overstated: the paper frames the policy items explicitly as *motivation*, not as estimated quantities. Counter-quote: "The answers have consequences for anti-discriminatory legislation and compensations after accidents damaging looks. In his superb narrative survey Hamermesh could not make strong conclusions on these questions" (p.2, Introduction) â€” this is a "why the topic matters" sentence, and the paper nowhere claims to estimate legal damages or accident compensation. No results table reports a policy counterfactual. This is a framing/scope-of-contribution point (closer to the overclaim reviewer's lane than mine), and within my lane it does not describe a threat to identification of the estimand the paper *does* target. Medium overstates a sentence that is conventional motivation. (Audit Y's item 4, "comprehensive" vs the eligible convertible-field-estimate domain, is likewise a contribution-scope/overclaim issue outside my lane.)

## One issue that neither X nor Y raised

Claim: **The exclusion of zero-order correlations and of studies lacking the SD of the beauty measure is a selection-on-identification-quality filter that plausibly biases the corrected mean upward, yet the paper treats the resulting sample as if selection were ignorable.** Severity: Medium. Evidence: inclusion criterion (iv) requires "the study must report statistics that allow us to convert the reported estimate to the percent increase in earnings or productivity following a one-standard-deviation increase in beauty (e.g. studies reporting zero-order correlations or lacking the standard deviation of the beauty measure cannot be converted)" and "we excluded 69 studies... 19 for not reporting convertible statistics (often just correlation coefficients)" (p.16, Methods 4.1; Figure S1, p.42). Why it matters (lane: selection / extrapolation): convertibility is not random with respect to estimate quality or magnitude. Studies that report a full regression with the beauty SD (hence convertible % effects) are systematically the more "complete" wage-regression papers, which are also the ones most likely to control for ability and to be published in better journals â€” precisely the dimensions the paper finds shift the premium ("studies published in more prestigious journals... tend to publish larger beauty premiums," p.11; Impact factor PIP 0.914, Table 3 p.32). Dropping 19 correlation-only studies therefore changes the composition of the synthesized population in a direction correlated with the outcome and with the key moderators, so the publication-bias correction and the BMA fitted values are computed on a non-ignorably selected subset. The paper never tests this: there is no comparison of included vs. convertibility-excluded studies on observed estimate size, and no sensitivity analysis imputing the excluded correlation-only estimates (e.g., via reported r and an assumed SD). The PRISMA box documents the count but not the missingness mechanism. Fix: report the distribution of reported effect sizes (in any common metric, e.g., partial correlation) for the 19 convertibility-excluded and 8 continuous-variable-excluded studies vs. the 67 included; add a sensitivity analysis that converts correlation-only studies under bounding assumptions on the beauty SD and re-estimates the corrected mean; state explicitly that the estimand is "the corrected mean among studies reporting SD-convertible field estimates," and discuss whether that subset is representative of the broader literature the conclusion generalizes to.

## Top 3 surviving criticisms after Round 2

**1. (Source: Y, sharpened) Mediator-conditioning makes the headline estimand a controlled direct effect, not a total effect â€” so "beauty is not important per se" is not identified.** Evidence: "beauty is not important in the labor market per se but via its correlation with other characteristics" (p.2) vs. the concession "cognitive ability could act as a mediator rather than a confounder... beauty could remain economically significant as a driver of human capital acquisition" (p.13) and "Attractive children achieve slightly higher test scores and attain more schooling" (p.6). Severity: High. Why: conditioning on a plausibly post-treatment variable (cognitive skill, the largest single conditioning effect, posterior mean âˆ’2.265, PIP 1.000, Table 3 p.32) targets the direct effect net of the human-capital channel; the total effect of beauty could be non-trivial. The paper states the mechanism but neither decomposes it nor caveats the abstract, so the central interpretive claim outruns what the design identifies.

**2. (Source: Y, sharpened) The discrimination inference relies on an unstated exclusion assumption that productivity proxies are appearance-clean, which the paper's own outcome set violates.** Evidence: "The beauty premium is similar for earnings and productivity, a fact inconsistent with discrimination based on employer tastes for beauty" (p.1) vs. "Productivity in this context can be measured as sales, research outcomes, study outcomes, electoral success, etc." (p.18) and the halo-effect channel "facial cues linked to attractiveness systematically affect perceived intelligence, independent of measured ability" (p.6). Severity: Medium-High. Why: votes, evaluations, and viewership are evaluator-mediated and can carry the same appearance bias as wages; under common contamination, earnings-productivity parity is consistent with (not evidence against) taste/evaluator discrimination. No test isolates objectively-metered output (e.g., piece-rate sales, prize money) from subjectively-evaluated "productivity," even though Table S18 (p.69) shows the productivity bucket is largely low/mid measurability.

**3. (Source: new) Convertibility-based study exclusion is selection on identification quality, plausibly correlated with the outcome and key moderators, and is left untested.** Evidence: criterion (iv) and "19 for not reporting convertible statistics (often just correlation coefficients)" (p.16); Impact factor PIP 0.914 and the finding that high-impact journals report larger premiums (Table 3 p.32; p.11). Severity: Medium. Why: the synthesized population is the SD-convertible subset, which is non-randomly related to ability-control and publication characteristics that the paper itself shows move the premium; the bias-correction and BMA are conditioned on this subset with no missingness diagnostic or bounding sensitivity check.

## Devil's-advocate check on emerging consensus

The strongest counter to my top criticism (mediator-conditioning) is the one the authors could legitimately invoke: they may be deliberately estimating a *controlled direct effect* â€” "is there a residual taste-for-beauty payoff once ability is observed?" â€” which is a coherent and policy-relevant estimand, and they do flag the mediator possibility in the Discussion (p.13). On that reading the analysis is internally honest and only the abstract wording ("not important per se," p.2) is too strong. I still judge the criticism survives, because (a) the abstract and intro state the broad mechanism-resolving claim, not the narrow controlled-direct-effect claim, and (b) the paper never reports the mediation decomposition (beautyâ†’cognitionâ†’outcome) that would license either reading â€” so the gap is between what is claimed and what is shown, which is exactly a methodological defect, not a tonal one. One genuine caution against over-weighting the whole consensus: the paper's *empirical* robustness in my lane is real and partly defuses milder versions of these points â€” it treats sex workers as a separate well-identified subsample (p.18, funnel in Fig. 3b), handles within-study/within-database dependence via three-level RoBMA and database-level clustering (Table 1, p.30), and explicitly diagnoses the funnel-asymmetry endogeneity problem rather than assuming it away ("we face a classical endogeneity problem in our meta-analysis specifications," p.22), responding with MAIVE. So the surviving criticisms are about the interpretation of conditioned estimates and sample selection, not about sloppy estimation.

## Bottom line after Round 2

Within the methodological/causal lane, the paper's estimation machinery is strong, but its two headline causal claims rest on assumptions it states yet does not test: that cognitive ability is a confounder rather than a mediator (making the residual a direct, not total, effect), and that "productivity" outcomes are appearance-clean enough to separate statistical from taste-based discrimination. The cleanest defensible reframing is conditional and decomposition-aware â€” "a small *direct* premium net of measured ability and publication bias, with the total effect possibly operating through earlier human-capital accumulation, and parity across (imperfect, partly evaluator-mediated) outcomes only weakly informative about employer tastes." A mediation decomposition and an included-vs-excluded selection diagnostic would convert the contested interpretations into supported ones.

## Extraction caveats and unverified references

Full text extracted cleanly across all 71 pages (main text pp.1â€“41; Supplementary Information pp.42â€“71), including Tables S1â€“S18 and Figures S1â€“S7; figures rendered as images and were inspectable. Both audits' core quotes that bear on my lane were verifiable against the source: the mediator concession (p.13), the abstract discrimination claim (p.1), the "not important per se" claim (p.2), the Table 4 subgroup intervals (Politicians 1.06 [âˆ’8.41, 10.52]; Scientists 1.58 [âˆ’0.35, 7.29], p.34), and the "objective=1 or quasi-exp=1" definition (Table S18, p.71). I did not access the external repository at meta-analysis.cz/beauty or run any code, so claims about the online appendix's contents are unverified. No external bibliographic references were checked (out of lane). No fabricated quotes or locators.

## Audit Y
### Round 1
# Peer audit (anonymized)
## Top 5 criticisms
### 1. Search and screening are not textually reproducible enough.
- Severity: High
- Evidence: "Studies identified through Google Scholar via the query ('beauty' or 'attractiveness') earnings productivity estimate (n = 1,715)" and "Studies screened according to the order in G. Scholar (n = 500)" (p.42, Figure S1)
- Why it matters: The manuscript does not state the stop rule for screening only 500 of 1,715 Google Scholar hits, nor the exact search settings needed to recreate the ordering. The final 67-study sample therefore depends on a dynamic ranking process that readers cannot reconstruct from the text.
- Concrete fix: Report the full query syntax, date/time, locale, sorting, language/date restrictions, duplicate handling, and stopping rule. Add a reproducible screening log for the unscreened and screened records.
- Devil's-advocate check: The paper does give the query, PRISMA counts, inclusion criteria, and snowballing. That helps, but it does not make Google Scholar ordering or the top-500 cutoff reproducible.

### 2. The fully adjusted cognitive/DID/quasi-experimental sample is ambiguously defined.
- Severity: High
- Evidence: "Objective cognitive skill control only 256/12; Objective cog. and non-cog. control 127/10; Objective cog. control or DID method 395/21" (p.46, Table S2); "objective=1 or quasi-exp=1: Studies that either include an objective cognitive measure or rely on quasi-experimental identification strategies (random assignment, difference-in-differences, instrumental variables)" (p.71, Table S18)
- Why it matters: The printed 395 count aligns with objective-cognition estimates plus DID estimates, while Table S18 says IV also belongs to the quasi-experimental definition. This leaves unclear whether the headline fully adjusted estimates include IV estimates, exclude them, or include only overlapping IV estimates.
- Concrete fix: Use one label and definition across Tables S2, S16-S18, and Table 4. Add a cross-tab of objective cognition, DID, IV, and overlaps.
- Devil's-advocate check: It is possible all IV observations overlap with objective-cognition or are intentionally excluded from this adjustment. The text needs to state that, because the tables currently imply different rules.

### 3. A key methods paragraph points to the wrong table.
- Severity: Medium
- Evidence: "For this reason we prefer the results reported in Panel A of Table S8 to the precision-weighted specifications reported in Panel B... the representative estimate we choose the median value reported in Panel A: 2.9 corresponding to the simple OLS" (p.23, Methods 4.2); "Effect beyond bias (constant): OLS 2.865" (p.49, Table S6)
- Why it matters: The 2.9 simple-OLS correction is traceable to Table S6, not Table S8, which is about premiums and penalties. This miscitation interrupts the audit trail for a central publication-bias correction.
- Concrete fix: Replace Table S8 with Table S6 and name the exact panel/column. If Table S8 is also intended, explain why its penalty/no-penalty results justify the same representative value.
- Devil's-advocate check: Table S8 contains a close no-penalty OLS value, so the typo may not change the conclusion. Still, the surrounding discussion is about full-sample funnel and precision-weighted models in Table S6.

### 4. The fully adjusted earnings/productivity labels appear reversed.
- Severity: Medium
- Evidence: "Measurement of success: Earnings 688/43... Non-earnings outcomes 471/29" (p.45, Table S2); "Output type: Earnings 471/29... Non-earnings outcomes 688/43" (p.47, Table S3); "earnings=0 0.38, earnings=1 0.43" (p.66, Table S17)
- Why it matters: Table S3 appears to swap the earnings and non-earnings counts. Because the paper emphasizes that earnings and productivity estimates are similar, the labels need to be exact even if the numerical difference is small.
- Concrete fix: Correct Table S3 labels and ensure Table 4 and Table S17 use the same coding. Add a note that earnings=1 corresponds to earnings and earnings=0 to non-earnings outcomes.
- Devil's-advocate check: The two adjusted means are close, so the substantive claim likely survives. The table remains hard to reproduce and cite accurately.

### 5. Code/data availability is declared, but the text lacks versioned implementation detail.
- Severity: Medium
- Evidence: "All data have been deposited in the meta-analysis.cz repository (meta-analysis.cz/beauty)" (p.15, Data Availability); "All codes have been deposited in the meta-analysis.cz repository (meta-analysis.cz/beauty)" (p.15, Code Availability); "Full coding rules and details for all observations are provided in the online appendix at meta-analysis.cz/beauty" (p.71, Table S18 notes)
- Why it matters: A bare URL does not specify a version, archive, checksum, package environment, or script entry point. That matters because the paper relies on complex transformations, prior rescaling, winsorization, BMA, RoBMA, and simulation-based occupation weighting.
- Concrete fix: Add a persistent archive DOI or commit hash, software versions, reproduction scripts for each table/figure, and checksums for the analytic dataset. State whether the repository contains excluded-study logs and all hand-coded variables.
- Devil's-advocate check: Declaring both data and code availability is a real strength. It does not fully solve textual reproducibility unless the manuscript identifies the exact archived state and execution path.

## Top 2 strengths
### 1. The paper gives unusually detailed inclusion and exclusion criteria.
Evidence: "We use the following inclusion criteria: i) the study must report the effect of beauty on a continuous variable reflecting earnings or productivity..." (p.16, Methods 4.1); "Based on these criteria, we excluded 69 studies..." (p.17, Methods 4.1). Why: This gives readers a concrete basis for checking eligibility decisions.

### 2. Robustness coverage is broad across publication-bias and model-uncertainty methods.
Evidence: "We use two versions of RoBMA: the simple one... and the three-level one..." (p.9, Section 2.2); "we also report the results of two models that explicitly allow for p-hacking: MAIVE... and RTMA" (p.10, Section 2.2); "robustness checks that employ different priors for BMA... and that use a subsample... without beauty penalties" (p.27, Methods 4.3). Why: The authors vary several consequential estimator and prior choices rather than relying on a single correction.

## Biggest blind spot
The largest reproducibility gap is the pipeline between the text and the final adjusted estimates. The manuscript says fitted BMA values set "other study characteristics... to their maximum observed values" (p.26, Methods 4.3), but it does not provide a compact recipe showing exactly which values enter each reported fitted estimate. It also sends key coding details outside the paper: "Full coding rules and details for all observations are provided in the online appendix" (p.71, Table S18 notes). The paper is therefore strong on reporting results, but weaker on making the complete transformation from search hits to tables reproducible from the manuscript alone.

## Bottom line
The paper reports a large, carefully assembled meta-analysis with extensive bias-correction diagnostics. The main reproducibility concerns are not about running another estimator, but about traceability: search reconstruction, exact adjustment definitions, table labels, table citations, and archived code/data state. The arithmetic/table issues look fixable, but they affect central claims enough to require correction before publication. The declared repository should be versioned and tied directly to table-by-table reproduction.

## Extraction caveats
The audit used only `manuscript_text.md`; I did not run code or verify the online appendix. The extracted text contains encoding artifacts such as mojibake in dashes, accents, and minus signs. Figures are represented as textual descriptions rather than inspectable plotted images.

## Unverified references
External bibliographic accuracy was not checked. One candidate for manual verification is reference 73: "Information Systems Research 35, 1507-2085 (2024)" (p.37, References).
### Round 2
# Peer audit (anonymized)
## Two strongest peer arguments
1. From Audit X: representative correction/unit-of-analysis ambiguity.
Evidence: â€œthe simple one that treats each estimate as the unit of analysis, and the three-level one that treats each study as the unit and accounts for within-study correlationâ€ (p.9, Results 2.2); â€œsimple RoBMA suggests a mean beauty premium of 0.2, while three-level RoBMA yields 3.1â€ (p.9, Results 2.2); â€œfor the representative estimate we choose the median value reported in Panel A: 2.9 corresponding to the simple OLSâ€ (p.23, Methods 4.2).
Why it survives: The paper acknowledges dependence and reports dependence-aware estimates, but the representative number is still tied to an estimate-level OLS correction. This is a robustness/reporting issue in this lane, independent of causal interpretation.

2. From Audit Y: subgroup â€œsmall premiumâ€ language outruns interval width.
Evidence: â€œAll individual contexts examined in Part 4 are consistent with small beauty premiumsâ€ (p.12, Results 2.3); Table 4 reports â€œScientists 1.58 [âˆ’0.35,7.29]; Prostitutes 1.42 [âˆ’4.05,10.20]â€ (p.34, Table 4).
Why it survives: Audit Yâ€™s politician example partly used Part 2 rather than the cited Part 4, but the concern survives using Part 4 itself. Several intervals remain wide enough that the text should distinguish small point estimates from intervals that still allow larger premiums.

## Two weakest or overstated peer arguments
1. From Audit Y: the â€œcomprehensive meta-analysisâ€ scope criticism is overstated.
Why it fails: The paper repeatedly states the scope in reproducibility-relevant places. Counter-quote: â€œWe focus on studies conducted in field settingsâ€ and â€œwe exclude laboratory experiments and surveysâ€ (p.16, Methods 4.1). The title also says â€œField Studiesâ€ (p.1). The scope could be repeated more tightly in the abstract, but the methods do not hide it.

2. From Audit X: the proposed timing diagnostic for mediator-vs-confounder claims is not textually supported.
Why it fails: Audit X says the existing coding could separate pre-treatment from contemporaneous ability, but the paperâ€™s definitions do not show such a timing variable. Counter-quote: â€œStudies controlling only for cognitive ability (e.g., IQ, test scores, or grades)â€ and â€œStudies including an objective cognitive ability measure (IQ, test scores, grades; not self-assessments or external ratersâ€™ judgments)â€ (p.71, Table S18). That objection may matter for identification, but as a reproducibility criticism it asks for coding the manuscript does not document.

## One issue that neither X nor Y raised
Claim/severity: Medium â€” the replication trail has internal table-reference, label, and subgroup-definition inconsistencies.

Evidence: The text says, â€œfor the representative estimate we choose the median value reported in Panel A of Table S8: 2.9 corresponding to the simple OLSâ€ (p.23, Methods 4.2), but the full-sample OLS value appears under â€œTable S6: Linear and nonlinear techniques detect publication biasâ€ with â€œEffect beyond bias (constant): OLS 2.865â€ (p.49, Table S6), while â€œTable S8: Publication bias tests separately for beauty premiums and penaltiesâ€ is a different robustness table (p.51, Table S8). The output-type Ns also conflict: Table S2 says â€œEarnings 688/43â€ and â€œNon-earnings outcomes 471/29â€ (p.45, Table S2), but Table S3 says â€œOutput type: Earnings 471/29â€ and â€œNon-earnings outcomes 688/43â€ (p.47, Table S3), while Table S17 confirms â€œearnings=1 (3lvl) 688/43/0.43â€ (p.67, Table S17). Finally, the cognition sample is inconsistently described: Table S2 labels â€œObjective cog. control or DID method 395/21â€ (p.46, Table S2), but Table S18 defines the same concept as â€œquasi-experimental identification strategies (random assignment, difference-in-differences, instrumental variables)â€ (p.71, Table S18), even though Table S2 separately reports â€œInstrumental variables 83/10; Difference-in-differences 12/2â€ (p.46, Table S2).

Why: These look like manuscript/table-label errors rather than necessarily computational errors, but they block exact textual reproduction because a reader cannot know which table, row label, or inclusion rule to follow.

Fix: Correct the Table S8 reference to the intended table, harmonize earnings/non-earnings labels across Tables S2/S3/S17, and state whether IV estimates are included in â€œobjective cognitive or quasi-experimentalâ€ adjusted samples, with overlap counts.

## Top 3 surviving criticisms after Round 2
1. source new: internal replication-trail inconsistencies.
Evidence: â€œPanel A of Table S8: 2.9â€ (p.23) points to the wrong table; â€œEarnings 688/43â€ (p.45, Table S2) conflicts with â€œOutput type: Earnings 471/29â€ (p.47, Table S3).
Severity: Medium.
Why: These are concrete table/reference/N issues that affect reproducibility from the manuscript text.

2. source X: representative publication-bias correction is not clearly aligned with the dependence-aware specification.
Evidence: â€œsimple RoBMA suggests a mean beauty premium of 0.2, while three-level RoBMA yields 3.1â€ (p.9); â€œrepresentative estimateâ€¦ 2.9 corresponding to the simple OLSâ€ (p.23).
Severity: Medium.
Why: The paper reports the dependence problem and alternative estimators, but the representative estimator choice needs a clearer rule.

3. source Y: subgroup uncertainty is under-described.
Evidence: â€œAll individual contexts examined in Part 4 are consistent with small beauty premiumsâ€ (p.12); â€œScientists 1.58 [âˆ’0.35,7.29]; Prostitutes 1.42 [âˆ’4.05,10.20]â€ (p.34, Table 4).
Severity: Medium.
Why: The table supports small point estimates, not uniformly tight exclusion of larger context-specific effects.

## Devil's-advocate check on emerging consensus
The strongest defense of the top criticism is that these inconsistencies may be typographical: Table S17â€™s â€œearnings=1 (3lvl) 688/43/0.43â€ (p.67) likely reveals the intended earnings row, and the online appendix may implement the correct coding. Still, textual reproducibility requires the manuscript tables and subgroup definitions to agree without requiring readers to infer which label is wrong.

## Bottom line after Round 2
The strongest evidence-auditor concerns are not about the identification story; they are about table consistency, estimator-unit transparency, and wording around imprecise subgroup estimates. The peersâ€™ causal/estimand objections may be important elsewhere, but in this lane the most actionable fixes are correcting internal labels/references and tightening robustness language.

## Extraction caveats and unverified references
Some extracted symbols and names show mojibake, especially minus signs and accented names; I relied only on passages where the intended text was clear. I did not run code, access the online appendix, or verify external references; data/code availability was text-checked only against â€œAll data have been depositedâ€ and â€œAll codes have been depositedâ€ (p.15, Data/Code Availability).

## Audit Z
### Round 1
# Peer audit (anonymized)
## Top 5 criticisms
### 1. The paper turns a conditional attenuation result into a "beauty is not important" claim.
- Severity: High
- Evidence: "These findings suggest that beauty is not important in the labor market per se but via its correlation with other characteristics." (p.2, Introduction)
- Why it matters: The paper later concedes that "cognitive ability could act as a mediator rather than a confounder" (p.13, Discussion). That caveat weakens the abstract-level claim that the premium is explained away rather than partly moved upstream.
- Concrete fix: Reframe the main claim as a small residual premium conditional on publication-bias correction and available cognitive controls. Move the mediator caveat into the abstract and first-page contribution statement.
- Devil's-advocate check: The authors could say they are only rejecting a direct residual labor-market premium. That defense holds for the narrow estimand, but not for the broader wording "beauty is not important."

### 2. The taste-based discrimination conclusion rests too much on earnings-productivity parity.
- Severity: High
- Evidence: "The beauty premium is similar for earnings and productivity, a fact inconsistent with discrimination based on employer tastes for beauty." (p.1, Abstract)
- Why it matters: The paper defines productivity broadly: "Productivity in this context can be measured as sales, research outcomes, study outcomes, electoral success, etc." (p.18, Methods 4.1). Those outcomes can themselves reflect appearance-mediated evaluation, demand, or selection, so parity does not rule out discrimination.
- Concrete fix: Present earnings-productivity parity as suggestive evidence against one narrow employer-taste channel. Separate employer taste, customer taste, evaluator bias, and appearance-shaped measured output.
- Devil's-advocate check: The authors do call the productivity measures "imperfect" (p.2, Introduction). That helps, but the abstract still states the discrimination inference more decisively than the measures warrant.

### 3. The policy stakes exceed what the synthesis identifies.
- Severity: Medium
- Evidence: "The answers have consequences for anti-discriminatory legislation and compensations after accidents damaging looks." (p.2, Introduction)
- Why it matters: The paper synthesizes reported associations and adjusted meta-analytic means, not accident-specific losses or legal-policy counterfactuals. That policy framing invites readers to use the results for decisions the estimand does not directly support.
- Concrete fix: Keep the policy sentence as motivation only, then state that the paper does not estimate legal damages or adjudicate discrimination claims. Add a short implications paragraph limited to what the meta-analysis can and cannot inform.
- Devil's-advocate check: Policy motivation can justify why the topic matters. It does not justify implying that the meta-analysis resolves those policy applications.

### 4. The contribution is sometimes framed as broader than the eligible evidence.
- Severity: Medium
- Evidence: "We conduct a comprehensive meta-analysis to reconcile these discrepancies" (p.7, Conceptual Background)
- Why it matters: The eligible set excludes several adjacent literatures: "we exclude laboratory experiments and surveys" and require convertible statistics for "the percent increase in earnings or productivity" (p.16, Methods 4.1). The contribution is valuable, but it is comprehensive only within a narrower field-estimate, convertible-outcome domain.
- Concrete fix: Replace "comprehensive meta-analysis" with "comprehensive meta-analysis of convertible field estimates on own earnings or productivity." Repeat that scope limit in the abstract.
- Devil's-advocate check: The Methods section is transparent about exclusions. The concern is not concealment; it is that headline positioning outruns the stated scope.

### 5. The paper describes subgroup evidence as small when some intervals remain wide.
- Severity: Medium
- Evidence: "All individual contexts examined in Part 4 are consistent with small beauty premiums." (p.12, Results 2.3)
- Why it matters: Table 4 reports wide subgroup intervals, for example "Politicians 1.06 [âˆ’8.41, 10.52]" and "Scientists 1.58 [âˆ’0.35,7.29]" (p.34, Table 4). These estimates may be consistent with small effects, but they are also consistent with substantively larger effects in some contexts.
- Concrete fix: Say the point estimates are small while subgroup uncertainty remains large. Reserve stronger language for contexts where the interval itself rules out sizable premiums.
- Devil's-advocate check: "Consistent with small" is technically true. The problem is that the phrasing suppresses the equal truth that several context estimates remain imprecise.

## Top 2 strengths
### 1. The paper is unusually transparent about scope and inclusion.
- Evidence: "The literature search was terminated on February 16, 2024. The dataset, together with R and Stata codes and reasons for exclusion of the 69 above-mentioned individual studies, is available at meta-analysis.cz/beauty." (p.17, Methods 4.1)
- Why: This makes the contribution easier to audit and limits ambiguity about what the meta-analysis covers.

### 2. The Discussion contains the caveat needed to discipline the main interpretation.
- Evidence: "Under this developmental framework, cognitive ability could act as a mediator rather than a confounder" (p.13, Discussion)
- Why: This sentence recognizes the strongest threat to the paper's "explained away" interpretation and gives the authors a clear path to a more defensible contribution claim.

## Biggest blind spot
The paper's own theory allows beauty to shape human-capital accumulation: "Attractive children achieve slightly higher test scores and attain more schooling" (p.6, Conceptual Background). It also concedes that "beauty could remain economically significant as a driver of human capital acquisition that the labor market later rewards" (p.13, Discussion). That makes the repeated residual-premium framing too narrow: the paper may be estimating what remains after conditioning on an intermediate outcome. The blind spot is not missing a robustness check; it is underplaying the total-effect interpretation while foregrounding "beauty is not important in the labor market per se" (p.2, Introduction).

## Bottom line
This is a strong meta-analytic contribution when framed as evidence on reported field estimates after publication-bias correction and observable-context adjustment. It overreaches when it says the evidence "explain[s] away most of the mean beauty premium" (p.1, Abstract) and when it treats earnings-productivity parity as "inconsistent with discrimination based on employer tastes" (p.1, Abstract). The clean fix is tonal but important: shift from mechanism-resolving language to conditional-association language.

## Designated minority objection
The minority objection is that the paper may be controlling away the very channel through which beauty matters: "cognitive ability could act as a mediator rather than a confounder" (p.13, Discussion). On that reading, the headline should not be that beauty is unimportant, but that its market relevance may operate before wages are observed.

## Extraction caveats
Some extracted text contains mojibake in symbols and names, especially in the author block and some mathematical notation. I avoided relying on corrupted passages where the intended text was not unambiguous.

## Unverified references
None; no outside references were used.
### Round 2
# Peer audit (anonymized)

## Two strongest peer arguments
- From Audit X. Claim: the abstract and introduction overstate what the cognitive-control result identifies. Evidence: â€œtwo factors combine to explain away most of the mean beauty premiumâ€ (p.1, Abstract); â€œbeauty is not important in the labor market per seâ€ (p.2, Introduction); but also â€œcognitive ability could act as a mediator rather than a confounderâ€ (p.13, Discussion). Why it survives: the paperâ€™s own caveat supports Audit Xâ€™s concern that the 1% estimate is better described as a residual/direct premium, not proof that beauty lacks a total labor-market effect.

- From Audit X. Claim: the earnings-versus-productivity comparison carries too much mechanism weight. Evidence: â€œThe beauty premium is similar for earnings and productivity, a fact inconsistent with discrimination based on employer tastes for beautyâ€ (p.1, Abstract); â€œproductivityâ€ includes â€œsales, research outcomes, study outcomes, electoral successâ€ (p.18, Methods 4.1). Why it survives: those outcomes are heterogeneous and can embed evaluator, customer, voter, or institutional responses to appearance, so similarity between earnings and these proxies does not by itself refute taste-based channels.

## Two weakest or overstated peer arguments
- From Audit X. Claim: the paper could readily test mediator versus confounder status by separating pre-treatment from contemporaneous ability controls. Why it fails: the manuscriptâ€™s coding description does not show that timing is coded. Counter-quote: cognitive-control categories are described only as â€œIQ, test scores, or gradesâ€ (p.71, Table S18), and the methods say only that â€œ38% find a way to control for a proxy for cognitive abilityâ€ (p.25, Methods 4.1). The core overclaim survives, but the specific diagnostic may require new coding rather than a simple re-run.

- From Audit Y. Claim: search and screening reproducibility is a high-severity threat. Why it fails in this lane: it matters for reproducibility, but it is not the strongest contribution/interpretation objection because the paper does disclose the query, screened count, criteria, termination date, and exclusion categories. Counter-quote: â€œStudies identified through Google Scholar via the queryâ€¦â€ and â€œStudies screened according to the order in G. Scholar (n = 500)â€ (p.42, Figure S1); â€œThe literature search was terminated on February 16, 2024â€ and reasons for exclusion are available with the dataset (p.17, Methods 4.1).

## One issue that neither X nor Y raised
Claim: the literature-positioning paragraph overstates Nault et al. as independent evidence against employer taste-based discrimination. Severity: Medium. Evidence: the paper first says prior meta-analyses â€œmostly focus on laboratory experiments with indirect external validity for the labor marketâ€ and â€œdo not report economic effectsâ€ (p.3, Introduction), then concludes that â€œStinebrickner et al. (3), Nault et al. (2), and our study present independent evidence against employer taste-based discriminationâ€ (p.3, Introduction). Why: after narrowing Naultâ€™s labor-market relevance, the manuscript should not place it on equal footing as independent evidence about employer taste-based discrimination. Fix: rephrase Nault as broadly consistent background, and reserve â€œindependent evidence against employer taste-based discriminationâ€ for labor-market field evidence.

## Top 3 surviving criticisms after Round 2
- Source X. Claim: the abstractâ€™s â€œexplain awayâ€ framing conflates controlled direct effects with total effects. Evidence: â€œcontrolling for cognitive ability renders the premium smallâ€ (p.1, Abstract), while â€œbeauty could remain economically significant as a driver of human capital acquisitionâ€ (p.13, Discussion). Severity: High. Why: the headline should say â€œdirect residual premium conditional on ability,â€ unless the paper can separate confounding from mediation.

- Source X. Claim: policy implications exceed the identified parameter. Evidence: â€œThe answers have consequences for anti-discriminatory legislation and compensations after accidents damaging looksâ€ (p.2, Introduction), but the conclusion is â€œlittle evidence of an additional payoff once information about ability is availableâ€ (p.13, Discussion). Severity: High. Why: law and compensation questions often concern total losses or treatment channels, not only residual direct discrimination after conditioning on ability.

- Source Y. Claim: the fully adjusted sample definition is unclear enough to weaken the headline adjusted claim. Evidence: Table S2 labels â€œObjective cog. control or DID methodâ€ (p.46, Table S2), while Table S18 defines quasi-experimental strategies as including â€œrandom assignment, difference-in-differences, instrumental variablesâ€ (p.71, Table S18). Severity: Medium. Why: the abstractâ€™s adjusted 1.1% claim depends on exactly which designs enter the â€œcognitive ability or DIDâ€ adjustment.

## Devil's-advocate check on emerging consensus
The authors do not simply ignore the main caveat: they explicitly write that â€œcognitive ability could act as a mediator rather than a confounderâ€ (p.13, Discussion). They also hedge part of the mechanism claim with â€œconsistent with no or little taste-based discriminationâ€ (p.18, Methods 4.1). The consensus should therefore ask for relabeling and caveating, not treat the meta-analysis as fundamentally invalid.

## Bottom line after Round 2
The publication-bias and model-uncertainty contribution remains credible and useful. The surviving contribution-skeptic objection is interpretive: the paperâ€™s abstract, policy framing, and literature-positioning statements should be narrowed to match a conditional/direct-effect claim.

## Extraction caveats and unverified references
I used `manuscript_text.md`, `T_cs2_peerX.md`, and `T_cs2_peerY.md`. I did not verify the online appendix, code, Google Scholar search ordering, or external bibliographic accuracy; figures are available only as extracted textual descriptions here. Audit Y flagged one reference-format concern, but I did not independently verify it.

## Designated minority objection
