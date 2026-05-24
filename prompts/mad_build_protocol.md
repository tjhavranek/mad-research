# MAD-build — the production mode protocol

Use when the user wants to **make something** together with Codex: code,
draft, plan, analysis. The 4-step protocol mirrors the RDT code session
in `Joint/cooperation_trial/` and `claude-codex-rdt-trial` on GitHub.

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

## Round 4 — Claude merges

Claude reads both v2's and synthesizes the best of both into `final/`.
Write `README_DEBRIEF.md` reporting:
- What each agent contributed.
- Which criticisms led to which changes.
- What still feels uncertain.
- Total elapsed time and number of Codex calls.

## When to abort or simplify

- If the task is genuinely small (e.g., "fix this typo") → don't MAD it.
  Just do it.
- If Codex is unavailable → tell the user, ask if they want a single-agent
  run, and proceed without claiming it was multi-model.
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
