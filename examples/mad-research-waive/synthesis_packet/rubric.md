# Locked synthesis rubric

**This file is locked.** Synthesis (`prompts/synthesis_codex_prompt.md`)
scores against this rubric exactly as written. Do not edit during a MAD
run. If you want to evolve the rubric, bump the version and edit between
runs.

Version: 1.0

## Six equally-weighted criteria

Synthesis writes a 2-3 sentence prose verdict for each. **No numeric
scores, no letter grades.** Words. Calibrated uncertainty, not fake
precision.

### 1. Identification credibility
Does the empirical design plausibly identify the parameter the paper
claims to estimate? Are the identifying assumptions stated and defended?
Are violations of those assumptions ruled out (or shown not to matter) by
the evidence in the paper?

### 2. Statistical validity
Are the statistical procedures appropriate for the data structure
(clustering, dependence, panel features)? Is inference handled correctly
(standard errors, multiple testing, weak-instrument concerns)? Are
distributional assumptions defensible?

### 3. Robustness coverage
Which specification choices were varied in robustness checks? Which
weren't? Are the robustness checks diagnostic (do they vary something that
matters), or decorative (varying a control that wouldn't change the
result)?

### 4. Numerical and citation accuracy
Do the numbers in the abstract, tables, and main text agree? Do the cited
papers say what the manuscript claims they say? Do reported sample sizes,
counts, and shares survive arithmetic checking?

### 5. Transparency of limitations
Does the paper acknowledge the limitations a sophisticated reader would
identify, or does it underplay them? Are the limitations specific (with
concrete consequences) or generic?

### 6. Severity-weighted issue count
Across the surviving criticisms: how many High-severity, Medium-severity,
Low-severity issues survive? A paper with 2 High and 0 Medium is in worse
shape than one with 0 High and 5 Medium.

## Why no scores

Numeric scores invite fake precision and false comparability across very
different papers. A 2-3 sentence prose verdict per criterion is harder to
fake and easier to act on.

## Why six criteria, equally weighted

Equal weighting prevents the synthesizer from quietly emphasizing the
criteria that produce the cleanest narrative. If you want a different
weighting for a specific kind of paper (e.g., experimental vs.
observational), say so explicitly in a wrapper memo above the rubric
verdict — but do not edit the rubric itself during a run.
