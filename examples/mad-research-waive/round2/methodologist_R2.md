# Round 2 — Cross-critique — Methodologist

For Round 2 I treat the two peer audits as anonymized: Audit X (the
evidence-quality peer) and Audit Y (the contribution-overclaim peer).

## Two strongest peer arguments

### Strong #1 — from Audit X
- Claim: The exponential weight `ω_i = exp(−max(0, −π_i))` is scale-dependent and underspecified; the same evidence can receive different weights after rescaling.
- Evidence: "ω_i = exp[ −max(0, −π_i) ]" (WAIVE slide 21) combined with "In practice it's better to use logs in the first stage." (WAIVE slide 12).
- Why it survives: π_i is a residual from a variance regression whose units depend on whether SE, SE², or log SE is on the LHS. WAIVE slide 12 quietly recommends logs but slide 21 displays the formula without restating the transformation. A reader implementing WAIVE from the slides will get different weights depending on which scale they pick. This is exactly the kind of hidden choice that erodes the contribution claim. Audit X is right that this is a serious evidence-quality gap, and it complements my own criticism (#2) that the exponential is unmotivated: it's not just unmotivated, it's *also* unstandardized.

### Strong #2 — from Audit Y
- Claim: WAIVE's "successor to MAIVE" framing leans on MAIVE's credibility while needing to prove *incremental* validity over MAIVE, not merely continuity with it.
- Evidence: "WAIVE first stage — same as MAIVE." (WAIVE slide 20).
- Why it survives: Audit Y caught something my Round 1 also flagged but framed differently. The slides explicitly say the first stage is shared. The contribution claim therefore rests entirely on the second-stage downweight. Without a clean MAIVE-vs.-WAIVE ablation on the *same* simulation panels and the *same* empirical sample, there is no evidence that the additional layer earns its name. Audit Y is right to insist the burden of proof is incremental, not categorical.

## Two weakest or overstated peer arguments

### Weak #1 — from Audit Y (criticism #5)
- Claim: "Practical deployment language may imply readiness before the extracted evidence supports it." (Y's criticism #5, citing slide 17's "Supports MAIVE, WAIVE, multilevel, clustering.")
- Why it fails: This is a stretch. The phrase "supports" on slide 17 describes the easymeta.org software's capability, not a claim of methodological readiness for WAIVE itself. Slide 19 explicitly visualizes WAIVE as more aggressive than MAIVE — that is presentation of a proposal, not premature deployment. Audit Y is generalizing from a tool screenshot to an overclaim of validation. The deeper criticisms Y makes (residual interpretation, functional form, ablation) are stronger and Y should drop this one.
- Counter-quote: "Penalize only negative residuals (π_i < 0) — downweight spuriously precise estimates." (WAIVE slide 21) — the slides are explicit about the mechanical novelty and don't claim full validation.

### Weak #2 — from Audit X (criticism #5)
- Claim: "Robustness coverage does not yet address inherited MAIVE fragilities." (X's criticism #5, naming the F<10 threshold and sample-size-selection assumption from MAIVE.)
- Why it fails: This is a real concern, but X is double-counting the inherited MAIVE limitations as a WAIVE-specific evidence gap. They are not. MAIVE's published paper acknowledges these (MAIVE p. 7) and recommends Anderson-Rubin CIs and F>100 as defensive practice. WAIVE inherits the warnings. The correct framing is "WAIVE inherits MAIVE's limitations and adds a new one" — not "WAIVE fails to re-derive MAIVE's existing caveats." X's R1 criticism #5 reads as boilerplate; X should consolidate it with X's stronger criticism #2 (residual interpretation) into a single targeted point.
- Counter-quote: "Caution is warranted if the F-statistic from the first-stage regression of reported variance on inverse sample size falls below 10." (MAIVE p. 7).

## One issue neither X nor Y raised

- Claim: WAIVE's downweight is asymmetric (only negative π_i is penalized), but the manuscript does not justify the asymmetry against an alternative reading.
- Severity: Medium
- Evidence: "Penalize only negative residuals (π_i < 0) — downweight spuriously precise estimates." (WAIVE slide 21).
- Why it matters: If π_i is supposed to capture "p-hacking and misspecification" (slide 12 says exactly this), then *positive* π_i values — reported SE larger than the design implies — could just as easily reflect misspecification (e.g., conservative clustering, redundant controls) as they reflect honest design. WAIVE penalizes only one side. The justification (slide 20: "Negative π_i → reported SE too small → possibly p-hacking") is asymmetric. A reader could legitimately ask: why is *over*-reported precision the only direction that signals misbehavior? The published MAIVE paper does not commit to this asymmetry; WAIVE introduces it without defense.
- Concrete fix: Either (i) defend the asymmetry on the grounds that incentives push only in one direction (researchers want significance, not insignificance, so they shrink SE) — this is plausible but should be stated; or (ii) make the downweight symmetric and report both versions.

## Top 3 surviving criticisms after Round 2

1. **Negative first-stage residuals are not a unique signal of p-hacking** (Self R1 #3, also X R1 #2, also Y R1 #2). Three streams converge on this. Severity: High. Evidence: WAIVE slide 20. Survives because none of the streams could find a justification in the manuscript for treating negative π_i as evidence of hacking rather than as evidence of honest design heterogeneity.

2. **The exponential downweight `ω_i = exp(−max(0,−π_i))` is unmotivated and scale-dependent** (Self R1 #2, also X R1 #3, also Y R1 #3). Three streams converge. Severity: High. Evidence: WAIVE slide 21. Survives because the slides offer "smooth and proportional decay" as the only justification, which is a property of many possible curves, not a derivation of this one.

3. **The contribution claim is unsupported until a clean MAIVE-vs.-WAIVE ablation is shown** (Y R1 #1 and #4, also X R1 #1, also Self R1 implicitly). Severity: High. Evidence: "WAIVE first stage — same as MAIVE." (WAIVE slide 20). Survives because the available slides show WAIVE doing more aggressive downweighting visually, but no panel directly compares WAIVE-PEESE to MAIVE-PEESE under the same simulation grid.

## Devil's-advocate check on emerging consensus

All three streams agree that (a) negative-residual interpretation is uncertain, (b) the exponential tilt is unmotivated, (c) ablation is needed. This is a lot of convergence. Is it grounded or herd-driven? I think grounded: each criticism is anchored in a different specific quote+locator (slide 20, slide 21, slide 20+22-26 truncation), and each stream argued the point from a different lane (methodology, evidence quality, contribution overclaim). The convergence reflects the slides actually having these gaps. But I should be wary: the four sources of negative-residual variation I named (heteroskedasticity, controls, clustering, scale) are *my* conjectures — none of the streams could quote the manuscript to confirm them. If the missing slides (22-26) actually address one of these, the convergent consensus may be wrong in the same way.

## Extraction caveats and unverified references

Same as Round 1: slides 22-26 truncated; supplementary information (S1-S6 of MAIVE) not in the audit material. The Round 2 conclusions are conditional on what's in the audit corpus, not on the full evidence the authors may have.
