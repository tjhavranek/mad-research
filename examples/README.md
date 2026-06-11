# Examples

Worked end-to-end runs of the skills on real research material.

## [`mad-research-waive/`](mad-research-waive/) — full audit of the WAIVE proposal

A complete `mad-research` run on a paper-plus-presentation pair from
the meta-research literature: MAIVE (*Nature Communications* 16: 8454,
2025) as published baseline; WAIVE (MAER-Net Ottawa, October 2025) as
the proposed extension under audit. Same inputs as the manual Duel
v1.7 example in the [duel-protocol
repo](https://github.com/tjhavranek/research-audit-duel-protocol),
so the automated and manual outputs can be compared directly.

Contents:

- `input/` — the two source PDFs plus the structured text version
  with page locators.
- `round1/`, `round2/` — all three streams' outputs, both rounds.
- `synthesis_packet/` — the anonymized claim packets, manuscript, and
  rubric that were sent to the fresh `codex exec` synthesis call.
- `final_memo_codex_raw.md` — the verbatim output of the fresh-Codex
  synthesis. Preserved as the load-bearing audit artifact.
- `final_memo.md` — the same memo, lightly formatted by the
  orchestrating Claude with a quote-verification note appended. Per the
  anti-tamper rule, Claude does not modify verdict, criticisms,
  minority report, or action list.

The WAIVE team are collaborators of the orchestrator. The example was
deliberately chosen as a case where the skill would have to produce
real criticism of friendly work.

## [`claude-only-vs-mad-3way/`](claude-only-vs-mad-3way/) — does Codex add value? A 3-way comparison

A blinded 3-way comparison on **five recent meta-analyses** (relative risk
aversion, class size, exercise & cognition, beauty & success, hedge-fund
alphas): the full `mad-research` protocol **with Codex** (arm T) vs. the
*same* protocol run **Claude-only** with a fresh-subagent synthesizer (arm
C1) vs. the earlier 5-lens Claude panel. An independent third model
(**Gemini**) judged the three memos blind on each paper.

Result (illustrative, not a powered study): one blinded LLM judge ranked
**C1 > T > Panel** on all five papers — a consistent *perceived-quality*
verdict, not ground truth. The folder's `README.md` has the full tally, the
concrete reasons (e.g. on the beauty paper the Claude arm caught a sign
contradiction and a transcription error that the Codex arm missed), and the
caveats that must travel with the claim: n = 5, one run per arm, a single
LLM judge (so the 5/5 unanimity measures judge consistency, not five
confirmations), **imperfect blinding** (the Codex-arm memos carried
identifiable scaffolding the judge penalized — part of the gap is
presentation), no seeded ground truth, and the raw per-stream files did not
survive (see the example's "Evidence status" section). This example
motivated the opt-in **Claude-only mode** added in v1.1.

## More to come

If you run a `mad-build` or `mad-research` session on material you're
happy to share publicly, send a PR adding it here. Examples accumulate
useful pattern-knowledge about what these skills produce on different
kinds of research material.
