# Round 2 — Cross-critique — Evidence Auditor

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
