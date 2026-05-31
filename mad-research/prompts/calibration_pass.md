# Optional Opus calibration pass (experimental — opt-in)

**Status: experimental, opt-in, OFF by default.** This step is NOT
part of the validated v1.0 default protocol. Its value (does an
independent Opus review of the synthesis improve net audit utility?)
is exactly what the planned comparative evaluation is meant to test.
Run it only when the user asks (e.g., "add an Opus calibration pass"
/ "calibrate the memo"), and label it as experimental wherever it
appears.

## What it is

After the fresh-Codex synthesis has produced `final_memo_codex_raw.md`
and Claude has done Step 8 quote-verification, the orchestrator
dispatches **one independent Opus subagent** (fresh context, via the
Task/Agent tool — NOT the orchestrating session) to review the memo
for *calibration*, not for new findings. It exists to attack the one
failure mode the protocol's grounding rules do not catch: a criticism
that is quote-grounded yet **over-stated** (triage harm — see
`helpers/safety_notes.md`).

## Hard constraint — it does NOT tamper

The calibration subagent has NO authority to change the memo. It does
not edit, delete, re-order, or re-severity any surviving criticism,
the verdict, the minority report, the action list, the trajectory
ledger, or Points rejected. Those remain exactly as the fresh-Codex
judge produced them (anti-tamper rule, `orchestration.md` Step 8). The
subagent's entire output is a **separate, appended, labeled note**.
The human triages; the memo body is untouched.

## Inputs to the subagent

- `final_memo.md` (the quote-verified synthesis memo).
- `manuscript_text.md` (so it can check a criticism's severity against
  what the cited quote actually supports).
Do NOT give it `meta.json` or the role/provider mapping.

## The subagent's brief (paste into the Task prompt)

> You are an independent calibration reviewer (you did not write this
> memo). Read the final audit memo and the manuscript. For EACH
> surviving criticism, judge only its **calibration**: does its stated
> severity match what its cited quote actually supports, or is it
> over-stated relative to the evidence? Flag any criticism where
> acting on it would likely cost more author time than the issue is
> worth (triage harm). Also note any criticism that reads as genuinely
> well-calibrated, so the reader can trust those faster.
> You may NOT propose new criticisms, rewrite, or re-score anything —
> you are calibrating, not auditing. Output ONLY:
> 1. Per-criticism: `well-calibrated` | `possibly over-stated (why, 1
>    sentence, citing the quote)` | `can't tell from the text`.
> 2. One-line overall: is the memo's severity profile calibrated, or
>    does it skew toward over-statement?
> Keep it under 250 words. Plain language, no numbers.

## How the orchestrator uses the result

Append the subagent's output verbatim to `final_memo.md` as a new
final section, exactly titled:

`## Calibration note (independent Opus subagent — experimental)`

Prepend this disclaimer line inside that section, verbatim:

> *Independence: this calibration is by an Opus subagent — the same
> model family as the Methodologist stream and the orchestrator. It is
> a within-family calibration check, not an independent judge, and it
> did not alter any criticism above. Experimental; not yet validated.*

Record in `meta.json`: `"calibration_pass": "opus-subagent"`. If the
subagent is unavailable, skip the pass and note its absence — never
silently let the orchestrating Claude "calibrate" in-session (that
would be the host judging its own run).
