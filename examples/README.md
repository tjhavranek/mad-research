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

## More to come

If you run a `mad-build` or `mad-research` session on material you're
happy to share publicly, send a PR adding it here. Examples accumulate
useful pattern-knowledge about what these skills produce on different
kinds of research material.
