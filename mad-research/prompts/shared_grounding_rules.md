# Shared grounding rules — mad-research skill

These rules apply to every role stream in every round. Any role prompt
includes this file by reference. The synthesis step auto-rejects any
criticism that violates them.

## The eight non-negotiables

1. **Quote + locator.** Every substantive criticism must include a direct
   quote from the source plus a locator: page number, section heading, line
   number, or equation/table number. Claims without locators are downgraded
   to "ungrounded" and listed in "Points rejected under scrutiny" in the
   final memo.

2. **No confidence scores in audit-quality language.** Do not write
   percentages, "70% confident," "9/10," etc. when assigning criticism
   severity or expressing your own confidence in an audit point. Use plain
   language: "almost certainly," "likely," "I'm not sure but," "this could
   go either way." Calibrated uncertainty in words, not in fake numbers.
   **Exception:** in Bayesian Mode only (opt-in at pre-flight), the
   *designated empirical claim* under evaluation gets a point estimate +
   80% interval. That number is about the claim's truth, not about audit
   severity. Severity ratings remain plain-language in all modes.

3. **Devil's advocate is a duty, not a role.** Each of the three streams
   includes one devil's-advocate pass against any emerging convergence with
   the other streams (this matters in Round 2). One stream is responsible
   for preserving the minority objection in the final memo even if it loses.

4. **Minority report preserved — evidence-engagement test.** The final
   memo must include at least one grounded objection that did not survive
   majority view, with the surviving counter-arguments named. The test
   for preservation: the objection has verified quote+locator support
   AND the convergent majority ignores rather than directly refutes its
   update-relevant evidence. ("Three streams said the opposite" is not a
   refutation; addressing the evidence is.) Never compress this section
   into silence.

5. **Manuscript evidence vs. outside knowledge.** Distinguish them
   explicitly. Tag external claims `[EXTERNAL]`. Treat external claims as
   weaker than grounded ones unless they are widely accepted in the field.

6. **Report extraction failures.** If a page is unreadable, a table is
   garbled, a figure caption is missing, a citation cannot be resolved, or
   the PDF text appears truncated — say so in your output under a heading
   "Extraction caveats." Do not fabricate around extraction failures.

7. **Traceable chain.** Each surviving criticism in the final memo must
   point to its originating role-stream output and to its source quote.
   Reader must be able to follow: final memo → role stream → manuscript.

8. **No fabricated references.** Do not cite page numbers you have not
   verified. Do not invent paper titles, journals, or DOIs. If you think
   something exists but cannot confirm it, say so under "Unverified
   references."

## How violations are handled in synthesis

- A criticism with no quote+locator → moved to "Points rejected" with the
  one-line reason "no locator."
- A criticism with a quote but a fabricated locator (page that doesn't
  exist) → moved to "Points rejected" with reason "fabricated locator."
- An external claim labeled `[EXTERNAL]` → kept in final memo but in a
  separate section "External considerations," not in the main criticism
  list.
- A criticism strengthened by Round 2 cross-examination → moved into the
  surviving-criticism list with the cross-exam quote attached.

## A note on style

Across all role outputs, prefer specific verbs ("the IV exclusion fails
because…") over generic adjectives ("identification is weak"). Specific
verbs are easier to falsify and easier for the author to act on.

## Relevant recent MAD literature (acknowledged limitations)

Two ICML 2024 papers are worth knowing when interpreting any
MAD-research output:

- **Smit et al., *"Should we be going MAD?"* (PMLR 235:45883).**
  Multi-agent debate does not reliably beat self-consistency or
  simple ensembling, and gains are hyperparameter-sensitive enough
  that many published MAD wins look like overfitting. Implication:
  the strongest defensible claim for a single MAD-research run is
  "this is one structured pass at adversarial critique with a
  verifiable audit trail," not "this is a higher-truth aggregate."
- **Khan et al., *"Debating with More Persuasive LLMs Leads to More
  Truthful Answers"* (ICML 2024 Best Paper).** Debate helps the
  judge most when the debaters are *stronger* than the judge.
  This skill's use of fresh-Codex synthesis is motivated partly by
  this — the orchestrating Claude is a debater (Methodologist
  stream) and cannot also be a fresh judge of its own output. The
  fresh-Codex judge has no session history with the debaters,
  which is the minimal honest fix.

  **Caveat (honesty).** The current configuration has three
  debaters (Claude as Methodologist, Codex as Evidence Auditor,
  Codex as Contribution Skeptic) plus a fresh Codex synthesizer as
  judge. Judge and two of the three debaters are therefore the same
  underlying model. This avoids session-context leakage but does
  NOT optimally exploit Khan's "stronger debaters than judge"
  finding — for that effect to operate at full strength, the judge
  would need to be plausibly weaker than the debaters, which we
  cannot engineer when both available models are at frontier
  strength. Model rotation across runs (which model judges, which
  takes the Methodologist seat) is a candidate for future work.
  Do not overclaim the Khan effect in external descriptions of
  the skill.

Both papers should be cited as limitations in any external use of
the protocol's output.
