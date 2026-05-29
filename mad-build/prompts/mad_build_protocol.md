# MAD-build — the production mode protocol

Use when the user wants to **make something** together with Codex: code,
draft, plan, analysis. The 4-step protocol automates the manual
Research Audit Duel (RDT) code-collaboration session into a single
Claude Code skill.

## Inputs

The user's request (e.g., "write a Python script that does X", "draft a
Czech op-ed on Y", "design an experiment to test Z") and any source
materials they hand you.

## Setup

Pick a slug from the user's request (e.g., `rdt-script`, `oped-housing`).
Create:

```
mad_sessions/<YYYYMMDD-HHMM-slug>/
├── ASSIGNMENT.md
├── claude_v1/
├── codex_v1/
├── round1_feedback/
├── round2_revisions/
└── final/
```

Write `ASSIGNMENT.md`:
- **Goal:** what the user wants in one sentence.
- **Deliverable:** what the output file(s) must contain.
- **Constraints:** language, length, libraries, length budget, style.
- **What success looks like:** 2-4 bullets the user named or that you
  inferred.
- **Where each agent writes:** `claude_v1/<files>`, `codex_v1/<files>`.

## Round 1 — independent drafts

Claude writes the draft into `claude_v1/`. In parallel, dispatch Codex
via `codex exec` (see `helpers/invoke_codex.md`) with this prompt:

```
Read ASSIGNMENT.md in the current directory and follow it exactly. Write
your draft to codex_v1/. Do not read claude_v1/. Work autonomously.
```

Wait for Codex to finish. Confirm `codex_v1/` is populated.

## Round 2 — cross-review

**Strict rule: each side reviews ONLY the other side's draft.** This is
what makes the critique a fair second opinion rather than a defense of
one's own work. Do not let either prompt mention the reviewer's own draft.

Claude reads `codex_v1/` (NOT `claude_v1/`) and writes
`round1_feedback/claude_reviews_codex.md`: be specific (line numbers,
named bugs, what to steal, what's weaker). Do not re-read your own draft.

Dispatch Codex with this exact prompt template (do not improvise the
wording):
```
Round 2 of MAD-build. Read ASSIGNMENT.md and claude_v1/ (the other agent's
draft). Do NOT open codex_v1/ — that is your own draft and looking at it
would defeat the purpose of cross-review. Write a critical review at
round1_feedback/codex_reviews_claude.md. Be specific: cite line numbers,
identify bugs, name what you would steal and what is weaker. 200-400 words.
```

## Post-Round-2 validation (mandatory)

Before proceeding to Round 3, verify:
- `round1_feedback/claude_reviews_codex.md` exists and is non-empty.
- `round1_feedback/codex_reviews_claude.md` exists and is non-empty.
- Neither review references the reviewer's own draft by name.

If any of these fail, the round did not produce a fair cross-review.
Re-run the failed side with the strict prompt above.

## Round 3 — revisions (do NOT skip)

This round is **mandatory** in MAD-build. Do not jump from Round 2 to the
final merge. The point of MAD-build is that each side gets a chance to
respond to legitimate criticism. Skipping this round produces a final
that mixes one side's first draft with the other side's critique — not a
true synthesis.

If, after Round 2, you (the orchestrator) decide the critiques are too
trivial to warrant revision, say so explicitly in
`README_DEBRIEF.md` and write the v2's as `(no changes; v2 = v1)` files.
Do not silently skip.



Claude reads Codex's review of Claude's draft, revises into
`round2_revisions/claude_v2.<ext>`.

Dispatch Codex with:
```
Read round1_feedback/claude_reviews_codex.md. Revise your draft based on
the legitimate criticisms, keeping your strengths. Write the revised
version to round2_revisions/codex_v2.<ext>. Also write a short
codex_v2_notes.md listing what you changed and why, and which Claude
criticisms you rejected and why.
```

## Round 4 — Claude merges (only when drafts are comparable)

**Default path.** When both v2's are merge-worthy contributions to the
assignment, Claude reads both and synthesizes them into `final/`. The
merge is anti-tamper: keep each side's stronger material, attribute
changes, and do not silently substitute Claude's preferred phrasing
for Codex's working solution.

Write `README_DEBRIEF.md` reporting:
- What each agent contributed.
- Which criticisms led to which changes.
- What still feels uncertain.
- Total elapsed time and number of Codex calls.

**Alternate path: candidate asymmetric failure.** If one side's v2
cannot contribute final-relevant material without redoing the core
task, do NOT force a merge. The merge step is mandatory only when
the inputs are comparable; forcing it on a broken draft launders the
failure into the final output.

### Trigger criteria (Claude is conservative; sharper criteria avoid self-serving judgment)

Declare a draft "candidate asymmetric failure" only when supported by
**one mechanical trigger** OR **two objective-evidence triggers** —
not by quality preference alone.

Mechanical triggers (observable on the draft itself, no comparison
needed):
- Refused: the draft is an explicit refusal with no substantive
  attempt. (Caveats followed by useful work are NOT refusal.)
- Empty / missing: required artifact absent or zero-length.
- Unparseable: the artifact cannot be opened, parsed, or rendered.
- Broken build: when the task required runnable output, the build /
  test / lint fails. (Distinguish from flaky environment errors.)

Objective-evidence triggers (require evidence, not opinion):
- Wrong task: material mismatch between the draft and `ASSIGNMENT.md`
  — quote both, show the deliverable gap. Stylistic differences do
  NOT count.
- Fabricated source: cited file, function, API, or paper does not
  exist on inspection.
- Self-contradiction: draft's own claims are inconsistent with its
  own cited evidence.
- Internally broken artifact: deliverable parts contradict each other
  in a way that can be shown by quoting them side-by-side.

**Not enough by itself:** lower quality, partial coverage, different
priorities, individual points rejected during cross-review, or
orchestrator disagreement with a peer choice. Those go through the
normal Round 3 revision cycle.

### Procedure when a candidate failure is observed

1. **Document the evidence first.** Write `round2_revisions/
   asymmetric_failure_evidence.md` containing the trigger(s) and the
   verbatim quotes / locators / error messages / refusal text that
   support each. Do not redact or paraphrase.

2. **Surface the decision to the user with three options.** The
   orchestrator does NOT ship a one-sided `final/` unilaterally,
   except when the failure is already equivalent to non-run (empty
   output, plain refusal, missing required artifact). Surface:

   > "I observe a candidate asymmetric failure on the <Claude | Codex>
   > side: <one-line summary>. Evidence in `round2_revisions/
   > asymmetric_failure_evidence.md`. Options:
   >  (a) Re-run the failed side with a sharpened prompt.
   >  (b) Ship the working side as `final/` and label this run
   >      'degraded' in `README_DEBRIEF.md`.
   >  (c) Proceed with the normal merge anyway, despite the defect."

3. **If the user picks (a):** Re-run that side's Round 1 + Round 2
   with the sharpened prompt; document the retry in `README_DEBRIEF.
   md`.

4. **If the user picks (b):** Copy the working side's v2 into
   `final/` (or a hand-edited refinement of it). Write
   `README_DEBRIEF.md` with these mandatory fields:
   - `mode: degraded — asymmetric failure`
   - `failed side: <claude | codex>`
   - `triggers fired: <list>`
   - `evidence file: round2_revisions/asymmetric_failure_evidence.md`
   - `user decision: ship-degraded`
   - `effective contributors: 1/2`

5. **If the user picks (c):** Proceed with the normal merge. Log
   that the candidate failure was overridden by user choice and
   note the unresolved concern in `README_DEBRIEF.md` under "What
   still feels uncertain."

### Symmetry note

Claude is always the orchestrator in mad-build, so the asymmetry
risk is one-directional: Claude can over-flag Codex failures more
easily than the reverse. Two countermeasures:

- Round 2 is where Codex reviews Claude's draft. If Codex's review
  identifies Claude's draft as broken in the trigger-criteria
  sense (not "I would do it differently"), the orchestrator is
  obligated to surface that as a candidate Claude-side asymmetric
  failure using the same procedure above.
- The "Not enough by itself" list (lower quality, partial coverage,
  etc.) explicitly excludes the cases where Claude would be most
  tempted to dismiss a peer draft.

## When to abort or simplify

- If the task is genuinely small (e.g., "fix this typo") → don't MAD it.
  Just do it.
- If Codex is unavailable → tell the user, ask if they want a single-agent
  run, and proceed without claiming it was multi-model. (Distinct from
  candidate asymmetric failure: this is technical non-run; that is
  ran-but-unsalvageable.)
- If the user interrupts mid-round → save state, summarize what's done,
  and ask whether to continue, restart, or stop.

## Output guarantee

At the end of a successful MAD-build run, the user has:
- Two independent drafts (`claude_v1/`, `codex_v1/`).
- Two mutual critiques (`round1_feedback/`).
- Two revisions (`round2_revisions/`).
- A merged final (`final/`).
- A short debrief explaining the convergence and disagreements.

That artifact set is the auditable thing — not just the final file.
