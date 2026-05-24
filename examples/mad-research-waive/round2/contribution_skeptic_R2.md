# Round 2 — Cross-critique — Contribution Skeptic

## Two strongest peer arguments

1. From: Audit Y.  
Claim: WAIVE's weight is scale-dependent and underspecified.  
Evidence: WAIVE defines `omega_i = exp[-max(0, -pi_i)]` (WAIVE slide 21), while also noting that "In practice it's better to use logs in the first stage" (WAIVE slide 12).  
Why it survives: This is the sharpest contribution-level objection because it attacks the estimator as stated. If `pi_i` is a variance-regression residual, its size depends on units, whether the dependent variable is `SE`, `SE^2`, or log variance, and any normalization. The exponential penalty is not invariant to rescaling unless the algorithm standardizes the residual or formally moves to a log-residual definition.

2. From: Audits X and Y.  
Claim: Negative first-stage residuals are not validated as a unique or reliable signal of p-hacking.  
Evidence: WAIVE asks "hacking if negative?" and says "Negative pi_i -> reported SE too small relative to N -> possibly p-hacking" (WAIVE slide 20), then automatically penalizes negative residuals (WAIVE slide 21).  
Why it survives: The slides' wording is tentative, but the estimator's action is mechanical. A negative residual can reflect legitimate design features: tighter measurement, richer controls, different scaling, or genuinely lower residual variance. MAIVE itself says the residual "soaks up, among other things, the spurious elements of the reported standard error related to p-hacking" (MAIVE p. 4). "Among other things" is fatal for a classifier unless WAIVE shows those other things are rare or harmless.

## Two weakest or overstated peer arguments

1. From: Audit X.  
Claim: The first-stage F-statistic threshold of 10 is too low.  
Why it fails: The concern is plausible, but overstated as a WAIVE-specific contribution criticism. MAIVE already states caution below F = 10 and reports stricter subsets: 267 meta-analyses with F > 10 and 239 with F > 100 (MAIVE p. 6). A stronger version would ask WAIVE to report sensitivity by F-bin. The blanket claim that 10 is "too low" needs evidence not present in either audit.  
Counter-quote: "Caution is warranted if the F-statistic from the first-stage regression of reported variance on inverse sample size falls below 10" (MAIVE p. 7).

2. From: Audit X.  
Claim: The empirical validation is not preregistered.  
Why it fails: This is under-grounded. The source text does not state preregistration status, and WAIVE's final results slides are truncated. Absence of preregistration may be worth asking about, but it is not a demonstrated weakness from the available record. The better criticism is selection/sensitivity of empirical filters, not preregistration itself.  
Counter-quote: "Final slides: results/applications/conclusions/Q&A (text-extraction truncated; not used in this audit)" (WAIVE slides 22-26 note).

## One issue neither X nor Y raised

Claim: WAIVE appears to double-count the same first-stage residual: MAIVE drops `pi_i` from adjusted precision, then WAIVE reuses negative `pi_i` to downweight observations in the second stage.  
Severity: High.  
Evidence: MAIVE's adjustment uses the fitted part of `SE(E_i)^2 = alpha_0 + alpha_1(1/N_i) + pi_i`, with the `pi_i` term struck through/dropped (WAIVE slide 12). WAIVE then defines the weight from the "Residual from MAIVE first stage" (WAIVE slide 20) and runs PEESE using `SE(E)^2_adj,i` with weights `omega_i` (WAIVE slide 21).  
Why it matters: If fitted MAIVE precision already purges the suspicious residual component from the regressor, WAIVE must explain why the same residual should also reduce the observation's influence. That may be right if negative residuals predict biased point estimates, not just biased standard errors. But the slides mostly motivate negative residuals as "reported SE too small relative to N" (WAIVE slide 20). WAIVE therefore needs evidence that negative residuals identify contaminated estimates after MAIVE correction, not merely contaminated reported variances before it.  
Concrete fix: Compare MAIVE-PEESE and WAIVE-PEESE conditional on residual sign/magnitude, and test whether negative residuals predict effect-size inflation after controlling for MAIVE-adjusted precision.

## Top 3 surviving criticisms after Round 2

1. Source: Audits X/Y plus this cross-critique. Claim: The negative-residual classifier is not validated. Evidence: "hacking if negative?" and "possibly p-hacking" (WAIVE slide 20). Severity: High. Why: WAIVE's distinctive operation depends on this classification; if it mostly captures honest heterogeneity, the estimator penalizes the wrong studies.

2. Source: Audit Y, strengthened. Claim: The exponential weight is not scale-invariant as stated. Evidence: `omega_i = exp[-max(0, -pi_i)]` (WAIVE slide 21) and the separate log-first-stage note (WAIVE slide 12). Severity: High. Why: Without a dimensionless residual or fixed transformation, the same dataset can imply different penalties under rescaling.

3. Source: Audit Y plus the new issue above. Claim: WAIVE's incremental contribution over MAIVE is unproven. Evidence: "WAIVE first stage -- same as MAIVE" (WAIVE slide 20) and "Then run PEESE ... with weights omega_i" (WAIVE slide 21). Severity: High. Why: MAIVE already replaces reported variance with sample-size-implied variance; WAIVE must show its additional residual weighting improves estimates after that correction.

## Designated minority objection

Preserve this objection: WAIVE may double-count first-stage residual information by dropping `pi_i` from adjusted SEs and then penalizing observations based on negative `pi_i`. The key locators are WAIVE slide 12, where the MAIVE adjustment drops the residual, and WAIVE slide 21, where the second stage uses weights from that residual. This deserves preservation even if the majority focuses on scale or validation, because it asks whether WAIVE has a coherent estimand-level rationale beyond "more aggressive downweighting looks better."

## Devil's-advocate check on emerging consensus

The emerging consensus risks being too harsh if slides 22-26 contain the missing ablations and empirical comparisons. Also, a heuristic penalty can still be useful if presented as a sensitivity analysis rather than as a new default estimator. The manuscript should not be rejected for using a simple weight, but it should be required to show invariance, validation, and incremental value.

## Extraction caveats and unverified references

WAIVE slides 22-26 were truncated, so any claims about final results, applications, or conclusions remain unverified. Several slide figures are text-light, and exact plotted values could not be audited from `manuscript_text.md`. I did not verify EasyMeta.org, the DOI screenshot, supplementary MAIVE materials, or external datasets beyond the extracted source.
