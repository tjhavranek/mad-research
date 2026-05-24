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

Claude reads `codex_v1/` and writes
`round1_feedback/claude_reviews_codex.md`: be specific (line numbers,
named bugs, what to steal, what's weaker).

Dispatch Codex with:
```
Read round1_feedback/. Then read claude_v1/. Write a critical review at
round1_feedback/codex_reviews_claude.md. Be specific: cite line numbers,
identify bugs, name what you would steal and what is weaker than yours.
Do NOT read codex_v1/ during this round.
```

## Round 3 — revisions

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
