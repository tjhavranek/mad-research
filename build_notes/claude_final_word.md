# Claude's final-word notes — what I accepted, what I rejected

I (Claude) had final word per Tomas's instructions. Here's what I did with
Codex's review (`codex_review_v1.md`).

## Accepted in full

1. **Honest framing of `--dangerously-bypass-approvals-and-sandbox`.**
   Codex was right — the helper text claimed the sandbox flag still
   confined writes, but the bypass flag overrides it. Rewrote
   `helpers/invoke_codex.md` to say so plainly, and added a Strict mode
   for users who want sandboxing at the cost of interactive approvals.

2. **Mode detection priority order.** Codex was right that "write" /
   "draft" / "design" collide between build and research. Added a
   5-step priority order to `SKILL.md` and pointed `orchestration.md` at
   it. Mode is recorded in `meta.json` and cannot drift mid-run.

3. **Hard lane boundaries in role prompts.** Codex was right that the
   three roles overlap. Added explicit "Lane boundary — strict" sections
   to each Round 1 prompt. The Methodologist must engage estimand/
   identification; the Evidence Auditor must engage arithmetic/citation;
   the Contribution Skeptic must engage framing/overclaim. Cross-lane
   criticisms are leave-it-out instructions.

4. **Orchestration completeness.** Codex listed many missing steps. I
   added:
   - Cost and consent gate (Step 2).
   - Token/page threshold with abort policy (Step 3).
   - Post-round validation with regex checks (Step 6.2).
   - Per-stream anonymization mapping (Step 6.3, owned by orchestration
     only).
   - Synthesis input isolation — synthesis sees only anonymized claim
     packets + rubric + manuscript, never `meta.json` (Step 7).
   - Final quote verification against source (Step 8).
   - Resume/retry state (separate section).

5. **PDF safety.** Codex was right that single-locator extraction was
   risky. Rewrote `helpers/pdf_extraction.md` to:
   - Preserve original page order (no reordering ever).
   - Record both `PAGE_INDEX` and `PRINTED_LABEL`.
   - Add per-page `first_15_chars`, `last_15_chars`, `char_count`,
     `has_table`, `has_figure`, `extraction_status` in
     `manuscript_meta.json`.
   - Hard-abort conditions when extraction is too lossy.
   - Quote verification routine for synthesis.
   - Dropped the "keep TOC/refs late" advice — Codex was right that it
     implied reordering.

6. **Windows install.** Added a ZIP-download install path in `README.md`
   for users who don't have Git. Switched tree glyphs to ASCII.

7. **`SKILL.md` vs `orchestration.md` policy conflict.** Reconciled.
   `SKILL.md` is now the source of truth on the Codex-failure policy
   (degrade in Mode A, abort by default in Mode B). `orchestration.md`
   defers to it explicitly.

## Accepted with modification

8. **Codex CLI flags.** Codex verified `--cd`, `--skip-git-repo-check`,
   `--sandbox`, `--output-last-message` are all real. But Codex
   suggested I "test the current `-c` config route" for approval
   suppression. I checked — there's no documented `--ask-for-approval`
   or `-c approvals.never` switch in the installed `codex exec --help`.
   For non-interactive orchestration, the bypass flag is the only
   working option as of v0.13x. The honest framing in
   `helpers/invoke_codex.md` documents this trade-off rather than hiding
   it.

9. **Prompt via stdin file instead of positional arg.** Codex flagged
   that `cmd /c` interpolation breaks on quotes, `&`, `|`, `<`, `>`,
   `!`, newlines. Rewrote `helpers/invoke_codex.md` to use the
   documented `codex exec ... -` (read prompt from stdin) and feed
   prompts via `< prompt_file`. This is robust regardless of prompt
   content.

## Rejected

None of Codex's substantive criticisms. All landed.

## Things Codex did not catch that I should still fix in v2

- The `examples/` folder is a placeholder. Need a real worked example
  before public release.
- The synthesis quote verification is conceptually correct but
  performance-sensitive on long manuscripts. Real-world testing will
  tell us if it's fast enough.
- The "fresh-context blinded synthesis" by copy-pasting into a new
  Claude Code window is mentioned but not implemented as a flag. It's
  a user-driven workflow for v1; a v2 might automate it.

## Summary

Codex's review was sharp and 100% landed. The skill is materially better
for it. The two-MAD-on-a-MAD-skill experiment (today's RDT trial, and
the feasibility study, and this build) all validated the pattern: each
side caught real bugs the other missed. None of these are bugs I would
have caught in pure self-review.
