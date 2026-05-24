# Round 1 — Contribution Skeptic

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
