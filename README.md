# mad-research: three Claude Code skills for working with Codex

[![DOI](https://zenodo.org/badge/1248112144.svg)](https://doi.org/10.5281/zenodo.20829175)

**For researchers who want a structured second opinion on a paper, proposal,
or report — without writing code.**

Most [Claude Code](https://claude.com/claude-code) users who also use
ChatGPT keep [Codex CLI](https://github.com/openai/codex) running in a
separate terminal and shuttle outputs by hand. These skills let Claude call
Codex directly from one session — for one-shot tasks, structured
collaboration, or an adversarial audit — with no MCP server, no external
orchestrator, no code to write.

Three independent skills in one repo. Install only the ones you want.

| Skill | What it does | Use when... |
|---|---|---|
| **`codex-bridge`** | One-shot Codex call from inside Claude. No protocol. | You want Codex's view on a specific question without scaffolding. |
| **`mad-build`** | Four-step Claude+Codex collaboration: independent drafts → cross-review → revisions → merge. | You're producing something (code, draft, plan) and want a structured second pair of eyes. |
| **`mad-research`** | Three-role-stream adversarial audit of a document. Quote+page grounded criticism, anonymized cross-critique, fresh-context Codex synthesis against a locked rubric, minority report preserved. Optional opt-in **Bayesian Mode** (experimental — no documented end-to-end run yet) for evaluating the truth of a contested empirical claim. | You want a structured stress-test of a paper, grant, or referee report. See [worked example](examples/mad-research-waive/). |

These skills are the third, automated generation of the [Research Audit
Duel + MAD protocols](https://github.com/tjhavranek/research-audit-duel-protocol)
family (the manual protocols are versioned separately as Duel v1.7 / MAD
v2.0 — see Citation below), implemented for the Claude Code + Codex CLI
combination.

**Looking for a deeper, Claude-only workshop?** The authors' successor
skill, [`paper-workshop` (CRUCIBLE)](https://github.com/tjhavranek/paper-workshop),
grew out of this project: it convenes a topic-adapted fleet of rival-tradition
referee seats (roundtable to summit scale), independently re-verifies every
comment from multiple angles, and can optionally *rebuild* the paper —
tracked changes, re-run analyses, replication package. It is Claude-only by
design, a choice informed by the comparative example below. Use
`mad-research` when you want a fast, lightweight **cross-model** audit with
a second provider in the loop; use `paper-workshop` when you want the
deepest single-provider workshop and, optionally, the rebuild.

## Privacy and limitations (read first)

**Your documents go to two AI providers.** Both Claude and Codex send the
text of your manuscript and the prompts to their respective cloud APIs
(Anthropic and OpenAI). Read each provider's data-handling policy before
sending anything confidential, embargoed, under double-blind review, or
covered by an NDA. If in doubt, don't. (The opt-in **Claude-only mode**
sends only to Anthropic — a single-provider option; see "How to invoke".)

**The default cross-model mode needs both providers; Claude-only mode
needs only Claude.** The default drives two AI services: your existing
Claude Code (Anthropic) plan, and an OpenAI Codex account — a ChatGPT
subscription *or* an OpenAI API key. A typical `mad-research` run is 5–6
Codex calls over 30–60 minutes; as of June 2026 that costs roughly
$0.10–$1.00 when Codex is billed through the OpenAI API (an estimate from
the authors' own metered runs on 30–50-page manuscripts, shown to you by
the mandatory cost gate before each run), or is covered within quota on a
ChatGPT/Codex subscription. Claude Code usage counts against your existing
Claude plan. If you don't already hold both, expect to set up the second
one before the default mode will run end-to-end — or use the opt-in
**Claude-only mode**, which runs on Claude alone and needs no Codex
account or OpenAI login at all.

**Two warnings worth reading before your first run** (full list in
[`mad-research/helpers/safety_notes.md`](mad-research/helpers/safety_notes.md)):
a quote-grounded criticism can still **overstate its severity** — acting on
an inflated "High" can cost you more time than missing a minor true issue,
so treat each surviving criticism as a candidate to weigh, not a verdict to
obey; and manuscripts you hold **as a referee are confidential regardless
of blinding regime** — many venues prohibit uploading submissions to cloud
AI services outright, so check your venue's policy before running this on
a paper you are reviewing.

**Multi-agent debate is not ground truth.** Smit et al. (ICML 2024) found
that MAD does not reliably beat a single strong model with
self-consistency, and the 2026 literature sharpens the picture in both
directions: controlled studies find that *reasoning strength and group
diversity are the dominant drivers* of debate success while structural
tweaks matter less, that majority pressure suppresses independent
correction, and that long debates degrade through "problem drift"
(arXiv:2511.07784; arXiv:2502.19559). The diversity finding cuts *in
favor* of this skill's cross-model premise; none of it makes any debate
output ground truth. Treat the audit trail this skill produces as a
structured critique that surfaces issues for you to evaluate — not as an
authoritative verdict. The "Points rejected" and "Trajectory ledger"
sections in the final memo exist so you can audit what the protocol kept
and what it dropped.

**A first comparative look (illustrative).** We ran a comparison on five
recent meta-analyses: the full protocol *with* Codex (T) versus the *same*
protocol run *Claude-only* (C1; all streams plus a fresh-context Claude
subagent as synthesizer) versus the earlier 5-lens Claude panel, with a
third model (Gemini) judging the three memos blind on each paper — see
[`examples/claude-only-vs-mad-3way/`](examples/claude-only-vs-mad-3way/).
The judge ranked `C1 > T > Panel` on all five papers. Stated precisely:
**one LLM judge consistently perceived the Claude-only memos as most
useful** — which is evidence about perceived audit quality, not verified
correctness. The example's own caveats bound it tightly: a single judge and
one run per arm mean the 5/5 unanimity measures *judge consistency*, not
five independent confirmations; blinding was imperfect (the Codex-arm memos
kept protocol scaffolding the judge explicitly penalized, so part of the
gap is presentation, not reasoning); there is no seeded ground truth; and
the 2026 judge-bias literature has since quantified how strong LLM-judge
style and verbosity preferences are (style bias 0.76–0.92 across judge
models; verbosity ≈ +17% — arXiv:2604.23178), which fits the observed
pattern: on one paper the Codex arm ranked *lower* for declining to assert
a claim it could not ground in the text — the stricter call by this skill's
own rules. The same caveats apply, more weakly, to the protocol-beats-Panel
margin (the Panel summaries were also stylistically distinct, and shorter).
The full, single authoritative account of the example's evidence
limitations — including the loss of its raw per-stream files — is in the
example README's "Evidence status" section.

**Why the cross-model default remains, for now.** The honest answer is that
the evidence is split and weak in both directions. Against: the blinded
example above (one judge, n = 5, confounded). For: 2026 controlled studies
find *group diversity* among the dominant drivers of debate success
(arXiv:2511.07784), which is exactly what a second provider buys; and this
repo's own development history contains cross-model catches that a single
family missed (a third-family reviewer caught a real v1.0.1 bug; a Codex
stream rejected a fabricated reference in v0.5; v1.1.2's dual audit found
complementary, non-overlapping issue sets — recorded in the CHANGELOG).
Neither side has powered evidence. The default therefore stays cross-model
as the design bet the diversity literature supports, the Claude-only mode
is a first-class opt-in, and the decision will be revisited when the
evaluation below exists. If you weigh the example more heavily than the
diversity literature, use Claude-only mode — that is why it ships.

**Status (v1.2).** The protocol and tooling are complete for the documented
scope and stable in use; the integrity rails (anti-tamper rule, quote
verification, honest-degradation labels) are documented procedures followed
by the orchestrating model, with deterministic anchors where noted (input
and raw-memo hashes in `meta.json`) — not externally enforced code gates.
**Bayesian Mode is experimental**: no end-to-end Bayesian-Mode run is
documented yet. No configuration of this skill has been validated against
ground truth (seeded flaws or human adjudication of its memos); the planned
evaluation is now public in [`docs/EVALUATION.md`](docs/EVALUATION.md).
v1.2 means honestly bounded and openly specified — not proven superior.

## Independent user feedback

> "I wholeheartedly recommend this new AI tool from Tomas and Zuzana.
> For those who are not used to working with the terminal versions of
> Claude Code and Codex — like me — the setup required some help from
> ChatGPT. However, well worth it. I used the mad-research skill. The
> comments I received were good and caused me to make some changes to
> my paper. I also compared it to two proprietary AI review sites […].
> mad-research was comparable, if not superior. And, of course, it is
> free. mad-research will be part of standard toolkit in the future
> when writing papers."
>
> — **Bob Reed**, University of Canterbury — from his comment on the
> [MAER-Net announcement](https://www.maer-net.org/post/stress-testing-research-with-ai-now-super-easy-and-fully-automated),
> quoted with permission.

<sub>The `[…]` omits two competitor sign-up links from the original comment; nothing else is altered. Note on "free": the skills are MIT-licensed and free; running the default cross-model mode consumes Codex/Claude usage as described in the cost note above.</sub>

## Install

### Prerequisites

1. **Claude Code.** Assumed already installed (this is a Claude Code
   skill).
2. **Git.** Used by the install commands below. macOS comes with Git
   via Xcode Command Line Tools (`xcode-select --install`); Linux
   ships it via your package manager; on Windows, install [Git for
   Windows](https://git-scm.com/download/win) with default options.
3. **Node.js 18+.** Download from <https://nodejs.org> and run the
   installer with default options.
4. **Codex CLI.** Install with npm, then authenticate once via browser:
   ```sh
   npm install -g @openai/codex
   codex                  # opens browser for OpenAI login
   ```
   Codex can authenticate via your ChatGPT account or an OpenAI API key.
   Availability, rate limits, **and data-handling defaults** depend on which
   path you use. The `$0.10–$1.00` per-run estimate refers to OpenAI API
   billing. For confidential research, review the data-handling policy of
   the path you chose (a free-tier ChatGPT login may retain prompts for
   training); if your institution requires a specific zero-retention
   agreement, configure Codex with an OpenAI API key under that agreement.

Verify in a fresh terminal:
```sh
git --version     # expect any modern version
node --version    # expect v18.x or higher
codex --version   # tested against 0.13.x; newer versions (e.g. 0.133.x) may
                  # behave differently under --sandbox workspace-write —
                  # see helpers/invoke_codex.md and run the doctor
```

If you cannot install Git, you can also use GitHub's "Download ZIP"
button (green Code button on the repo page) and unpack the archive
to your temp directory. Replace `git clone ...` in the commands
below with the unpack step. **Note:** GitHub ZIPs unpack into a
branch-named folder (typically `mad-research-main/`), so replace
`/tmp/mad-research` (macOS/Linux) or `$env:TEMP\mad-research`
(Windows) in the commands below with the actual unpacked path. **If you
installed via ZIP, also skip the `rm -rf` / `Remove-Item` line that deletes the
temp checkout and point only the copy step at your unpacked folder** — otherwise
that line can remove the very folder you just unpacked.

The install commands below are safe to re-run: they refresh the temp
checkout, and they replace an installed skill **only after verifying the
freshly cloned copy exists** — so a failed download (network hiccup,
GitHub outage) leaves your existing install untouched instead of deleting
it.

### Quick start: install all three skills

#### macOS / Linux

```sh
mkdir -p ~/.claude/skills
rm -rf /tmp/mad-research
git clone https://github.com/tjhavranek/mad-research /tmp/mad-research
for skill in codex-bridge mad-build mad-research; do
  if [ -d /tmp/mad-research/$skill ]; then
    rm -rf ~/.claude/skills/$skill
    cp -r /tmp/mad-research/$skill ~/.claude/skills/$skill
  else
    echo "SKIPPED $skill: clone incomplete — existing install left untouched"
  fi
done
```

#### Windows (PowerShell)

```powershell
$skillsDir = "$env:USERPROFILE\.claude\skills"
$tempRepo  = "$env:TEMP\mad-research"
New-Item -ItemType Directory -Force $skillsDir | Out-Null
if (Test-Path $tempRepo) { Remove-Item -Recurse -Force $tempRepo }
git clone https://github.com/tjhavranek/mad-research $tempRepo
foreach ($skill in 'codex-bridge','mad-build','mad-research') {
  $src  = Join-Path $tempRepo $skill
  $dest = Join-Path $skillsDir $skill
  if (Test-Path $src) {
    if (Test-Path $dest) { Remove-Item -Recurse -Force $dest }
    Copy-Item -Recurse $src $dest
  } else {
    Write-Host "SKIPPED ${skill}: clone incomplete - existing install left untouched"
  }
}
```

### Install one skill only

Each skill is self-contained. Replace `<skill-name>` with the one you
want (`codex-bridge`, `mad-build`, or `mad-research`).

#### macOS / Linux

```sh
SKILL=mad-research                              # or codex-bridge / mad-build
mkdir -p ~/.claude/skills
rm -rf /tmp/mad-research
git clone https://github.com/tjhavranek/mad-research /tmp/mad-research
if [ -d /tmp/mad-research/$SKILL ]; then
  rm -rf ~/.claude/skills/$SKILL
  cp -r /tmp/mad-research/$SKILL ~/.claude/skills/$SKILL
else
  echo "SKIPPED: clone incomplete — existing install left untouched"
fi
```

#### Windows (PowerShell)

```powershell
$skill     = "mad-research"                     # or codex-bridge / mad-build
$skillsDir = "$env:USERPROFILE\.claude\skills"
$tempRepo  = "$env:TEMP\mad-research"
New-Item -ItemType Directory -Force $skillsDir | Out-Null
if (Test-Path $tempRepo) { Remove-Item -Recurse -Force $tempRepo }
git clone https://github.com/tjhavranek/mad-research $tempRepo
$src  = Join-Path $tempRepo $skill
$dest = Join-Path $skillsDir $skill
if (Test-Path $src) {
  if (Test-Path $dest) { Remove-Item -Recurse -Force $dest }
  Copy-Item -Recurse $src $dest
} else {
  Write-Host "SKIPPED: clone incomplete - existing install left untouched"
}
```

### Update an existing install

Re-run whichever Quick-start block above matches your platform. The
commands replace each installed skill with the freshly cloned copy (and
leave it untouched if the clone failed), so they also work as updates.

### Verify the install

**Restart Claude Code** (skills are loaded at session start). Then ask
Claude in natural language for the doctor of whichever skill you installed:

```
Run the mad-research doctor.
Run the mad-build doctor.
Run the codex-bridge doctor.
```

Each skill ships its own `helpers/doctor.md`, so users who installed only one
skill should ask for that skill's doctor — not `mad-research`'s. The doctor
prints whether Node, npm, Codex, the expected `codex exec` flags, and Codex
authentication all check out. If any check fails it tells you exactly what to
install or fix.

## How to invoke

| You say | Skill that fires |
|---|---|
| "have Codex draft X" / "ask Codex to review file Y" / "run Codex on this" | `codex-bridge` |
| "MAD-build a script that does X" / "have Codex help me build Y" / "competition agent on this" | `mad-build` |
| "MAD-research this paper" / "stress-test this proposal" / "referee report on attached.pdf" | `mad-research` |

You can also just say `Run MAD on file.pdf`; Claude routes to the
right skill based on the file type and the verbs you used.

To run `mad-research` **Claude-only** — single-provider, with all streams
and the synthesis done by fresh-context Claude subagents instead of Codex —
add *"Claude-only"* to the request (e.g. *"MAD-research this paper,
Claude-only"*). The default remains the Codex cross-model run; the
Claude-only configuration is an explicit, labelled opt-in (motivated by the
[3-way comparison](examples/claude-only-vs-mad-3way/)), distinct from the
degraded in-session fallback used when Codex is simply unavailable.

## See also

- A real `mad-research` audit, end-to-end: [`examples/mad-research-waive/`](examples/mad-research-waive/).
- Does Codex add value? A blinded 3-way comparison on five meta-analyses: [`examples/claude-only-vs-mad-3way/`](examples/claude-only-vs-mad-3way/).
- The deep Claude-only successor workshop (review + rebuild):
  [`paper-workshop` (CRUCIBLE)](https://github.com/tjhavranek/paper-workshop) —
  see "Looking for a deeper, Claude-only workshop?" above for how the two relate.
- The planned ground-truth evaluation of this skill: [docs/EVALUATION.md](docs/EVALUATION.md).
- The manual two-model / four-model protocols this builds on:
  <https://github.com/tjhavranek/research-audit-duel-protocol>.
- Changelog and version history: [CHANGELOG.md](CHANGELOG.md).

## License

MIT. Use, modify, share. Credit the authors.

## Citation

Havránek, T., & Iršová, Z. (2026). *mad-research: Claude Code skills for
adversarial multi-agent debate on research documents*. Zenodo.
<https://doi.org/10.5281/zenodo.20829175>

For the underlying methodology (Duel v1.7 / MAD v2.0) please also cite:
Iršová & Havránek (2026), *Research Audit Protocols: Duel + MAD*,
Zenodo, <https://doi.org/10.5281/zenodo.19105954>.

### Evaluated in

Havránek, T., & Iršová, Z. (2026). *Does Multi-Agent Debate Improve AI
Feedback on Research Papers?* arXiv:2607.14713.
<https://arxiv.org/abs/2607.14713>

A pre-registered experiment in which authors of 44 economics meta-analyses
ranked blinded AI reports on their own papers. The plain single-pass baseline
was ranked above mad-research — an against-interest result we report in full;
see the paper for the design, caveats, and what the comparison does not show.
