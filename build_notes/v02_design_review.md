# v0.2 design review — outside feedback synthesis

Five chatbots reviewed `REVIEW_PACKAGE.md`. Their feedback is verbatim
in the user's message; this file captures the convergent points and
the open design calls.

## Strong consensus (4-5 of 5)

**1. Split into two skills + thin shared layer.** AI1, AI2, AI3, AI5
all argue the two modes are different products with different failure
modes, target users, and trigger phrases. AI4 alone defends the
unified design. The split would be:
- `codex-bridge` — minimal shared skill containing only
  `helpers/invoke_codex.md`, `helpers/doctor.md`, and the Codex
  invocation contract. Installable on its own.
- `mad-build` — production mode. Depends on `codex-bridge`.
- `mad-research` — research-audit mode. Depends on `codex-bridge`.

**2. "Blinded structured synthesizer" is a fig leaf.** AI2 and AI3 are
explicit; AI5 alludes via "identity-driven bias." Concrete fix both
name: the synthesis should run as a fresh `codex exec` call against
the anonymized claim packets plus the locked rubric. The orchestrating
Claude would only format the returned memo, not write the verdict.
This costs one extra Codex call per run and meaningfully strengthens
the audit trail.

**3. Default Codex flags too aggressive.** AI1 sharpest on this.
Currently the canonical invocation uses
`--dangerously-bypass-approvals-and-sandbox` for non-programmer
convenience. Safer defaults per AI1:
- MAD-research → `--sandbox read-only` (audit is read-only by design).
- MAD-build → `--sandbox workspace-write` confined to a copied/temp
  workspace, never the user's source files.
- Reserve bypass behind an explicit `--unsafe-yolo` opt-in for users
  who knowingly want it.

## MAD literature gaps (AI2, AI4, AI5)

- **Smit et al., ICML 2024 — "Should we be going MAD?"** (PMLR 235:45883).
  Multi-agent debate does not reliably beat self-consistency; gains
  are hyperparameter-sensitive. Implication: the README should
  acknowledge the question and the WAIVE example or future paper
  should include a baseline ("ask Claude three times with different
  role prompts and merge").
- **Khan et al., ICML 2024 Best Paper — "Debating with More
  Persuasive LLMs Leads to More Truthful Answers."** Debate helps the
  judge most when debaters are *stronger* than the judge. Our setup
  has Claude as judge over Codex+Codex+Claude. Implication aligns
  with consensus #2: swap judge to Codex (fresh `codex exec`
  synthesis), or rotate judges and report stability.
- **Free-MAD / anti-conformity** (AI1, AI3). Add an explicit
  anti-sycophancy / validity-over-agreement clause to Round 2 and
  synthesizer prompts.
- **Adaptive Stability Detection** (AI1, AI4, AI5). Replace the fixed
  Round 3 trigger logic with stability-aware stopping. Beta-Binomial
  / KS-test stopping is the canonical form.
- **Dynamic role assignment** (AI1) — explicitly judged too much for
  v1.
- **Trajectory ledger** (AI1): in synthesis, label each claim with
  "originated by / challenged by / survived | downgraded | rejected."
  Cheap addition; strengthens the audit trail.

## My prioritization for v0.2

### Clearly right (would do without asking)
- Default flag change. AI1 made the security case cleanly; no
  countervailing argument; cheap fix.
- Cite Smit et al. + Khan et al. in limitations of `SKILL.md` and
  `examples/mad-research-waive/README.md`. Disarms the obvious
  referee objection AI2 flagged, and is honest about MAD's empirical
  status.
- Anti-conformity clause in Round 2 and synthesizer prompts. AI3 and
  AI4 both name this; literature supports it; minimal change.
- Trajectory ledger in synthesis output. Cheap, adds audit-trail
  strength without bloating.

### Genuine design call (need a verdict)
- **Split the repo.** 4/5 reviewers want it, including the two most
  reliable. Defending the unified design (AI4's position) is
  possible — "non-programmer convenience: one install command" — but
  the failure mode (mode misfire on ambiguous phrasing) is real.
  Splitting is a breaking change to a 6-hour-old public repo (one
  external user might exist already; the GitHub URL is in the
  REVIEW_PACKAGE.md circulating to chatbots). Worth it?
- **Synthesis via fresh `codex exec`.** AI2 and AI3 converge. Khan
  et al. supports it. Implementable: one extra Codex call, pass
  anonymized packets and `rubric.md`, return final memo, Claude only
  formats. But: changes the protocol's headline. And: Codex as
  judge is a new claim that should be tested.

### Defer to v0.3
- Adaptive stopping (Beta-Binomial / KS for Round 3 trigger).
- Dynamic role assignment.
- Baseline comparison ("ask Claude three times and merge") run on
  the WAIVE example.
- BiasScope / CollabEval engagement (AI5).

### Reject
- AI3's "halt the skill and ask the user to open a fresh Claude Code
  session" version of fresh-synthesis. Breaks the non-programmer
  promise. The `codex exec` version is the right one.
