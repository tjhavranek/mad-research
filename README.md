# mad-research: three Claude Code skills for working with Codex

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
| **`mad-research`** | Three-role-stream adversarial audit of a document. Quote+page grounded criticism, anonymized cross-critique, fresh-context Codex synthesis against a locked rubric, minority report preserved. Optional opt-in **Bayesian Mode** for evaluating the truth of a contested empirical claim. | You want a structured stress-test of a paper, grant, or referee report. See [worked example](examples/mad-research-waive/). |

These skills implement v3 of the [Research Audit Duel + MAD
protocols](https://github.com/tjhavranek/research-audit-duel-protocol)
family, automated for the Claude Code + Codex CLI combination.

## Privacy and limitations (read first)

**Your documents go to two AI providers.** Both Claude and Codex send the
text of your manuscript and the prompts to their respective cloud APIs
(Anthropic and OpenAI). Read each provider's data-handling policy before
sending anything confidential, embargoed, under double-blind review, or
covered by an NDA. If in doubt, don't.

**You need access to both providers.** This drives two AI services: your
existing Claude Code (Anthropic) plan, and an OpenAI Codex account — a
ChatGPT subscription *or* an OpenAI API key. A typical `mad-research` run
is 4–6 Codex calls over 30–60 minutes; that costs roughly $0.10–$1.00 when
Codex is billed through the OpenAI API, or is covered within quota on a
ChatGPT/Codex subscription. Claude Code usage counts against your existing
Claude plan. If you don't already hold both, expect to set up the second
one before the skill will run end-to-end.

**Multi-agent debate is not ground truth.** Recent evidence (Smit et al.,
ICML 2024) suggests that MAD does not reliably beat a single strong model
with self-consistency. Treat the audit trail this skill produces as a
structured critique that surfaces issues for you to evaluate — not as an
authoritative verdict. The "Points rejected" and "Trajectory ledger"
sections in the final memo exist so you can audit what the protocol kept
and what it dropped.

**Status (v1.0).** The skill is feature-complete and stable. Whether
multi-agent debate actually outperforms a single strong model on these
tasks is the open question above — a comparative evaluation is a separate
planned study, not part of this release. v1.0 means the tooling is done
and honestly bounded, not that the approach is proven superior.

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
(Windows) in the commands below with the actual unpacked path.

The install commands below are **idempotent** — safe to re-run. They
remove any existing temp checkout and any existing installed skill
folders before copying, so you cannot accidentally nest old files
inside new ones.

### Quick start: install all three skills

#### macOS / Linux

```sh
mkdir -p ~/.claude/skills
rm -rf /tmp/mad-research
git clone https://github.com/tjhavranek/mad-research /tmp/mad-research
for skill in codex-bridge mad-build mad-research; do
  rm -rf ~/.claude/skills/$skill
  cp -r /tmp/mad-research/$skill ~/.claude/skills/$skill
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
  $dest = Join-Path $skillsDir $skill
  if (Test-Path $dest) { Remove-Item -Recurse -Force $dest }
  Copy-Item -Recurse (Join-Path $tempRepo $skill) $dest
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
rm -rf ~/.claude/skills/$SKILL
cp -r /tmp/mad-research/$SKILL ~/.claude/skills/$SKILL
```

#### Windows (PowerShell)

```powershell
$skill     = "mad-research"                     # or codex-bridge / mad-build
$skillsDir = "$env:USERPROFILE\.claude\skills"
$tempRepo  = "$env:TEMP\mad-research"
New-Item -ItemType Directory -Force $skillsDir | Out-Null
if (Test-Path $tempRepo) { Remove-Item -Recurse -Force $tempRepo }
git clone https://github.com/tjhavranek/mad-research $tempRepo
$dest = Join-Path $skillsDir $skill
if (Test-Path $dest) { Remove-Item -Recurse -Force $dest }
Copy-Item -Recurse (Join-Path $tempRepo $skill) $dest
```

### Update an existing install

Re-run whichever Quick-start block above matches your platform.
Because the commands remove the destination before copying, they
also work as updates.

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

## See also

- A real `mad-research` audit, end-to-end: [`examples/mad-research-waive/`](examples/mad-research-waive/).
- The manual two-model / four-model protocols this builds on:
  <https://github.com/tjhavranek/research-audit-duel-protocol>.
- Changelog and version history: [CHANGELOG.md](CHANGELOG.md).

## License

MIT. Use, modify, share. Credit the authors.

## Citation

Havránek, T., & Iršová, Z. (2026). *mad-research: Claude Code skills for
adversarial multi-agent debate on research documents*. GitHub repository,
<https://github.com/tjhavranek/mad-research>.

For the underlying methodology (Duel v1.7 / MAD v2.0) please also cite:
Iršová & Havránek (2026), *Research Audit Protocols: Duel + MAD*,
Zenodo, <https://doi.org/10.5281/zenodo.19105954>.
