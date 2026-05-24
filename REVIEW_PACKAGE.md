# Review package — `mad-research` Claude Code skill

A self-contained snapshot for outside review. Goal of this document:
let a reviewer judge the design without browsing the repo.

GitHub (new repo, may not be indexed yet): <https://github.com/tjhavranek/mad-research>

---

## 1. What I wanted

A single Claude Code skill that does **two** things:

1. **General Claude+Codex collaboration on any project.** When a user
   triggers it, Claude (running in Claude Code) calls Codex CLI
   (`codex exec`), both draft independently, they cross-review, both
   revise, and Claude merges. Useful for code, drafts, plans — any
   creative or technical artifact a user wants a second opinion on.

2. **Automated MAD (multi-agent debate) on research documents.** Three
   role streams (Methodological referee, Evidence auditor,
   Contribution skeptic), three rounds (independent → anonymized
   cross-critique → optional adaptive third round → blinded
   synthesis), one rubric-based final memo. Built to stress-test
   papers, grants, referee reports.

Constraints:
- **Easy for non-programmers.** Only Claude Code (which they already
  have) plus a basic ChatGPT subscription. One-time install of
  Node.js + `npm i -g @openai/codex` + `git clone` of the skill.
  Then trigger in natural language: *"Run MAD on paper.pdf as a
  top-5 referee."*
- **Robust on Windows, Mac, and Linux.**
- **Parsimonious.** No Python framework, no provider adapter layer,
  no HTML viewer, no fourth model. Markdown-only skill.
- **Honest about limits.** Quote+page grounding required for every
  surviving criticism. Synthesis auto-rejects ungrounded claims with
  a visible audit trail. "Three independent streams" is qualified —
  two of three streams run on the same Codex model.

This sits alongside a prior public protocol family:
<https://github.com/tjhavranek/research-audit-duel-protocol> (Duel
v1.7 — two-model manual; MAD v2.0 — four-model manual). The new
skill is v3 in spirit: same family, but automated and code-friendly.

---

## 2. Architecture (the whole thing in one page)

```
~/.claude/skills/mad-research/
├── SKILL.md                          — front door + trigger phrases
├── README.md                         — install for non-programmers
├── LICENSE                           — MIT
├── rubric.md                         — locked 6-criterion synthesis rubric
├── prompts/
│   ├── shared_grounding_rules.md     — 8 non-negotiables
│   ├── mad_build_protocol.md         — Mode A: production workflow
│   ├── round1_methodologist.md       — Mode B Round 1, role 1
│   ├── round1_evidence_auditor.md    — Mode B Round 1, role 2
│   ├── round1_contribution_skeptic.md — Mode B Round 1, role 3
│   ├── round2_cross_critique.md      — anonymized cross-exam template
│   ├── round3_adaptive.md            — conditional third round
│   └── synthesis.md                  — blinded structured synthesizer
├── helpers/
│   ├── orchestration.md              — 9-step operational checklist
│   ├── invoke_codex.md               — verified codex exec invocation
│   ├── doctor.md                     — pre-flight checks
│   ├── pdf_extraction.md             — dual-locator PDF -> text
│   └── safety_notes.md               — privacy, prompt injection, cost
└── examples/
    └── mad-research-waive/           — worked example on a real paper
```

The skill is entirely declarative — every operational rule lives in
markdown that Claude reads at trigger time. The only code-shaped
artifact is the `codex exec` invocation, documented with verified
flags in `helpers/invoke_codex.md`.

---

## 3. The eight non-negotiables (the methodological core)

From `prompts/shared_grounding_rules.md`:

1. **Quote + locator** on every substantive criticism. Page, section,
   line, equation, or table number. Anything without survives no
   round.
2. **No confidence scores or percentages.** Calibrated uncertainty in
   words, not in fake numbers.
3. **Devil's advocate is a duty, not a role.** Each of the three
   streams runs an adversarial pass against any emerging convergence
   (especially in Round 2). One stream is responsible for the
   minority report.
4. **Minority report preserved.** The final memo must include at
   least one grounded objection that did not survive majority view.
   Never compress this into silence.
5. **Manuscript evidence vs. outside knowledge.** Distinguish them
   explicitly. Tag external claims `[EXTERNAL]`.
6. **Report extraction failures.** Unreadable pages, garbled tables,
   missing captions — say so in `## Extraction caveats`.
7. **Traceable chain.** Each surviving criticism in the final memo
   points to its originating role-stream output and to its source
   quote. Reader can follow: final memo → role stream → manuscript.
8. **No fabricated references.** Don't cite page numbers you have
   not verified. Don't invent paper titles, journals, DOIs.

---

## 4. The two modes

### Mode A — MAD-build (production)

When user says: *"MAD-build a script that does X"*, *"have Codex help
me draft Y"*, *"competition agent on this."*

Four steps:
1. Claude scaffolds `<project>/ASSIGNMENT.md`.
2. Both agents draft independently → `claude_v1/`, `codex_v1/`.
3. Each reviews the other's draft → `round1_feedback/`.
4. Each revises → `round2_revisions/`. Claude merges to `final/`.
   Writes `README_DEBRIEF.md`.

Validation: post-Round-2 check that each side reviewed only the
other (not their own draft). This was caught and fixed in a
smoke-test bug.

### Mode B — MAD-research (audit)

When user says: *"MAD-research this paper"*, *"stress-test the
proposal"*, *"give me a referee report on the grant."*

Steps:
1. PDF → structured `manuscript_text.md` with dual page locators
   (PDF page index AND printed page label) and per-page checksums
   (first 15 chars, last 15 chars, char count, has_table) so quote
   verification can run later.
2. Round 1: Claude (Methodologist, reads PDF directly) + two
   separate `codex exec` calls (Evidence Auditor + Contribution
   Skeptic, read the text version). **Lane boundaries are strict.**
3. Post-Round-1 validation: regex checks for required sections,
   quotes, locators, extraction caveats.
4. Round 2: anonymized cross-critique. Each stream sees the *other
   two* labeled `Audit X` / `Audit Y`. Per-stream anonymization
   mapping lives only in `meta.json` (orchestrator-only).
5. Round 3 trigger: only if two streams fundamentally disagree on a
   high-severity grounded point, OR Round 2 surfaces a new
   high-severity issue, OR user forces it. Otherwise skip.
6. Synthesis: locked rubric (6 equally-weighted criteria, prose
   verdicts only — no scores). Anonymized inputs only. Auto-reject
   ungrounded claims into `## Points rejected`. Preserve one
   minority objection.
7. Final quote verification: every surviving criticism's quote+page
   re-checked against the source text. Verification failures move
   to `Points rejected`.

---

## 5. Synthesis rubric (locked)

Six criteria, equally weighted, prose verdicts only. From `rubric.md`:

1. **Identification credibility** — does the design plausibly identify the parameter the paper claims to estimate?
2. **Statistical validity** — clustering, dependence, weak-instrument concerns, multiple testing.
3. **Robustness coverage** — which specification choices were varied; which weren't; diagnostic vs. decorative.
4. **Numerical and citation accuracy** — abstract/table/text agreement; do cited papers say what's claimed.
5. **Transparency of limitations** — specific vs. generic acknowledgement.
6. **Severity-weighted issue count** — across surviving criticisms.

No numeric scores. No letter grades. Plain-language verdicts.

---

## 6. How Codex is actually called (the hard part)

From `helpers/invoke_codex.md`. Verified against `@openai/codex` v0.13x:

```bash
# macOS / Linux
codex exec \
  --cd "<session_path>" \
  --skip-git-repo-check \
  --dangerously-bypass-approvals-and-sandbox \
  --output-last-message "<output.md>" \
  - < "<prompt_file>"
```

```cmd
:: Windows cmd.exe (or via PowerShell using "cmd /c")
codex.cmd exec --cd "<path>" --skip-git-repo-check --dangerously-bypass-approvals-and-sandbox --output-last-message "<output.md>" - < "<prompt_file>"
```

Key design choices:

- **Prompt via stdin file**, not positional argument. Long
  manuscripts plus quotes contain `&`, `|`, `<`, `>`, `%`, `!`,
  newlines — all of which break shell command-line parsing.
- **`--dangerously-bypass-approvals-and-sandbox` is honest framing,
  not a fig leaf.** That flag disables Codex's internal sandbox. We
  use it because non-interactive orchestration hangs without it on
  Windows/PowerShell. Documented honestly. Strict mode (sandboxed,
  approval-blocking) is available for users who prefer it at the
  cost of interactive approvals.
- **Doctor checks `codex exec --help` for all five expected flags**
  before any run. Version mismatch aborts.

---

## 7. The skill ate its own dogfood (build trail)

The skill was built using the skill. Three rounds:

1. **Claude drafts all 16 markdown files.**
2. **Codex review pass** — one `codex exec` call with the whole
   tree. Caught: dishonest framing of the bypass flag (it doesn't
   "still sandbox"); mode-detection trigger collisions; role overlap
   in Round 1 prompts; missing orchestration steps (cost gate,
   per-stream anonymization, quote verification); PDF locator
   fragility; Windows install assumptions.
3. **Claude finalized.** Accepted every substantive criticism; one
   narrow exception (Codex suggested a `-c` config route to avoid
   the bypass flag — checked the installed help, no such route
   exists in v0.13x; documented honestly instead). Final-word trail
   in `build_notes/`.

The smoke test caught a second bug: in the first MAD-build run, the
orchestrator (Claude) skipped Round 3 (revisions) and the empty
`round2_revisions/` folder was not flagged. Codex's smoke-test
critique caught it. The skill's post-Round-2 validation step was
added as a result.

The WAIVE example (`examples/mad-research-waive/`) is a real
MAD-research run on a paper plus presentation pair from the
meta-research literature. Subjects (MAIVE/WAIVE) are the
orchestrator's collaborators — deliberately chosen so the example
would have to produce real criticism of friendly work. The synthesis
went through its own self-critique pass (Codex review of the final
memo, post-hoc fixes documented in `build_notes/`).

---

## 8. Open design questions for outside review

These are the things I'd most like a sharp outside reviewer to test:

a. **Is the two-mode-in-one-skill design right**, or should
   MAD-build and MAD-research ship as two skills?

b. **Are eight non-negotiables the right set?** What's missing,
   what's redundant?

c. **The "blinded structured synthesizer" framing** — is this an
   honest way to describe synthesis-in-orchestrator-session, or is
   it a fig leaf? If the latter, what's the smallest change that
   would make it honest?

d. **Cross-platform robustness.** The skill is tested on Windows.
   The Mac/Linux paths are written but not verified. Where will it
   fail?

e. **Cutting-edge MAD research.** Are there recent results on
   multi-agent debate (consensus dynamics, role assignment,
   blinding, calibration) that this design ignores? Specifically:
   the "permanent devil's advocate" pattern, the question of
   majority-vote bias, and the synthesizer's role.

f. **Failure modes for a non-programmer.** What's the most likely
   way a researcher who only knows Claude Code will hit a brick
   wall on first use, and how should the skill prevent it?

g. **Is the WAIVE example informative enough as a demo**, or does
   it look performative? (It contains a self-critique trail; some
   might read that as flagellation rather than honesty.)

---

## 9. Sample of what the skill produces (Mode B)

What follows is the final memo from a real MAD-research run audited
the WAIVE proposal — included so the reviewer can see the *output
quality*, not just the design.

(The example below is one possible synthesis; running the skill
again produces a different one because LLMs are non-deterministic.
What is stable is the structure: verdict, surviving criticisms with
quote+locator, points rejected, minority report, external
considerations, action list, audit trail.)

---

## SAMPLE OUTPUT: WAIVE final memo (real run, abridged)

**Audit target:** WAIVE: Weighted Adjustment Instrumental Variable
Estimator (slides), with MAIVE (*Nature Communications* 16: 8454,
2025) as the published baseline.

**Audit task:** Stress-test the claimed contribution of WAIVE over
MAIVE as a top-5 referee would.

### Verdict
WAIVE is a plausible incremental extension of MAIVE that adds a
second-stage downweight applied to estimates with negative
first-stage residuals (`ω_i = exp[−max(0, −π_i)]`, slide 21). After
three streams and an anonymized cross-critique round, six high-
severity grounded objections survive — including a structural
double-counting concern surfaced in Round 2 and preserved here as
the minority report.

### Top surviving criticisms (abridged)

1. **Negative first-stage residuals are not a unique signal of
   p-hacking.** Slide 20: "Negative π_i → reported SE too small
   relative to N → possibly p-hacking." MAIVE p. 4 says the same
   residual "soaks up, *among other things*, the spurious elements
   of the reported standard error related to p-hacking." That
   "among other things" is fatal for a classifier. Other plausible
   sources: heteroskedasticity, legitimate richer controls, cluster
   structure, scale choices on the LHS.

2. **The exponential weight is unmotivated and scale-dependent.**
   Slide 21 calls it "smooth and proportional decay" — a property
   of many possible curves, not a derivation of this one. π_i's
   units depend on whether the first stage is in SE, SE², or log SE
   (slide 12 prefers logs but slide 21 displays the formula in raw
   form).

3. **No clean MAIVE-vs.-WAIVE ablation in the available slides.**
   Slide 20: "WAIVE first stage — same as MAIVE." The contribution
   rests entirely on the second-stage downweight. No panel directly
   compares the two under the same simulation grid.

4. **The downweight is asymmetric without defense.** Slide 21
   penalizes only negative π_i. Positive π_i (reported SE *larger*
   than design implies) could equally reflect misspecification.

5. **Double-counting (minority report).** MAIVE drops π_i from the
   adjusted precision; WAIVE then reuses negative π_i to downweight
   observations in the second stage. The slides do not justify this
   two-step usage of the same residual.

6. **Inherited instrument-validity assumption (sample-size
   exogeneity) is not stress-tested for WAIVE specifically.**

### Action list (concrete upgrade targets)

1. *(criticism #1, "design-conditional residuals" upgrade)* Move
   first stage to log-log with coarse design indicators:
   `ln(SE) = α + β ln(N) + Γ'D + ν`. Residuals then mean "precision
   unexplained by N and design."
2. *(criticism #2, "FORT" upgrade)* Replace fixed exponential tilt
   with IRLS / MM-estimation (Huber or Tukey bisquare loss). Let
   data choose tilt parameter δ. Clean studies → δ ≈ 0; spurious
   studies → automatic downweight.
3. *(criticism #3)* Head-to-head MAIVE-vs.-WAIVE ablation panel
   using same simulation and empirical sample as MAIVE NC paper.
4. *(criticism #4)* State the incentive argument for asymmetry, or
   symmetrize.
5. *(criticism #5, minority report)* Justify the two-step use of
   π_i: argue negative π_i predicts biased estimates *after* MAIVE
   adjustment, not just biased reported variances *before* it.
6. *(criticism #6, "SAGE" upgrade)* Build a GMM extension with
   instrument vector `Z = (1/N, 1/√N, ln N)`; report Hansen J-test.
   Operational rule: J-rejection is informative, not automatically
   fatal.
7. *(framing)* Until #3 and #6 are published, label WAIVE in
   presentations as "an experimental sensitivity layer on MAIVE"
   rather than "a successor estimator."

A unified "WAIVE 2.0" workflow combining these: design-conditional
log-log first stage → MAIVE baseline → FORT-style robust second
stage → SAGE-style GMM diagnostic → ablation panel reporting MAIVE
/ WAIVE-FORT / selection-model alternatives side by side, with
Anderson-Rubin CIs throughout.

### Audit trail (excerpt)
- Streams: 3/3. Methodologist = Claude. Evidence Auditor and
  Contribution Skeptic = Codex CLI v0.13x. Independence caveat:
  two of three streams are the same Codex model with the same text
  input.
- Round 3 skipped (no trigger).
- Self-critique pass after first synthesis caught five real bugs
  (one fabricated reference, mis-labeled minority report,
  overstated independence, missing reproducibility objection,
  "External considerations: None" overclaim). The final memo is the
  post-critique revision.
- Synthesis blinding: honest "blinded structured synthesizer," not
  "fresh judge." For high-stakes work, users can copy the
  anonymized claim packets and rubric into a fresh Claude Code
  window.

---

(End of sample output.)
