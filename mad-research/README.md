# mad-research

Three-role-stream adversarial audit of a research document, with
fresh-context Codex synthesis. Adapted from the Havránek/Iršová
[Research Audit Duel + MAD protocols](https://github.com/tjhavranek/research-audit-duel-protocol)
into a Claude Code skill.

See the [top-level README](../README.md) for install commands.

## What it gives you

After install, you can ask Claude:

```
MAD-research on paper.pdf — adversarial referee-style stress test.

Stress-test this grant proposal: focus on identification.

Adversarial audit of attached.pdf, harsh.
```

The output is a structured critique meant to **surface issues for you
to evaluate**, not a polished referee report or authoritative verdict
(see `helpers/safety_notes.md` for the longer caveat).

Claude will:
1. Run pre-flight checks.
2. Prepare a text version of the PDF with page locators preserved.
3. Run three independent role streams (Methodologist via Claude;
   Evidence Auditor and Contribution Skeptic via separate Codex
   `exec` calls).
4. Anonymized Round 2 cross-critique.
5. Conditional Round 3 only if a genuine fault line remains.
6. **Fresh Codex `exec` synthesis** against anonymized claim packets
   plus the locked rubric — Claude verifies quotes and formats but
   does not write the verdict.

Output: a `final_memo.md` with verdict, top surviving criticisms,
points rejected (with one-line reasons), minority report, trajectory
ledger (origin → challengers → status), action list, audit trail.

Typical run: 30-60 minutes wall-clock, 4-6 Codex calls.

There is also an opt-in **Claude-only mode** ("MAD-research this paper,
Claude-only") that runs the same protocol with no Codex — all three
streams and the synthesis as fresh-context Claude subagents. Single
provider, fresh judge preserved; see `SKILL.md` → "Claude-only mode."

## Output structure

```
mad_sessions/<YYYYMMDD-HHMM-slug>/
├── meta.json
├── input/
│   ├── <user_source.pdf>
│   └── manuscript_text.md       — structured text with page locators
├── round1/
│   ├── methodologist.md
│   ├── evidence_auditor.md
│   └── contribution_skeptic.md
├── round2/
│   ├── methodologist_R2.md
│   ├── evidence_auditor_R2.md
│   └── contribution_skeptic_R2.md
├── round3/                       — only if triggered
├── synthesis_packet/             — anonymized claim packets sent to Codex
└── final_memo.md
```

## See also

- A worked example: [`examples/mad-research-waive/`](../examples/mad-research-waive/).
- The original two-model Duel + four-model MAD protocols (manual,
  copy-paste): <https://github.com/tjhavranek/research-audit-duel-protocol>.
