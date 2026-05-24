# mad-research — Claude Code skill

**A practical multi-agent debate (MAD) skill for Claude Code users who also
have a basic OpenAI subscription.** Adapted from Tomáš Havránek and Zuzana
Iršová's [Research Audit Duel + MAD
protocols](https://github.com/tjhavranek/research-audit-duel-protocol).

Two modes:

- **MAD-build** — Claude (this session) and Codex CLI draft the same task
  independently, cross-review each other, revise, and Claude merges the
  best of both. Use for code, drafts, analyses.

- **MAD-research** — three role streams (Methodologist, Evidence Auditor,
  Contribution Skeptic), three rounds, one blinded synthesis. Use to
  stress-test a paper, grant, referee report, or research idea.

Both modes are invoked by typing what you would naturally say to Claude.
No commands to memorize.

## Quick start (10 minutes, one time)

You need three things on your machine: Node.js, Codex CLI, and this skill.

### 1. Install Node.js
Download the LTS installer from <https://nodejs.org> and click through.
Verify in a fresh terminal:
```sh
node --version    # should print v18.x or higher
```

### 2. Install Codex CLI
```sh
npm install -g @openai/codex
codex
```
The second command opens a browser for you to authenticate with your
OpenAI account. Costs ~$0.10–$1.00 per MAD-research run on a typical
paper depending on document length.

### 3. Install this skill

#### Option A: Git clone (if you have Git)

**macOS / Linux:**
```sh
git clone https://github.com/<owner>/mad-research ~/.claude/skills/mad-research
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/<owner>/mad-research $env:USERPROFILE\.claude\skills\mad-research
```

**Windows (cmd.exe):**
```cmd
git clone https://github.com/<owner>/mad-research %USERPROFILE%\.claude\skills\mad-research
```

#### Option B: ZIP download (no Git needed)

1. Go to <https://github.com/<owner>/mad-research>, click the green
   "Code" button, then "Download ZIP".
2. Unzip the file. You'll get a folder called `mad-research-main`.
3. Rename it to `mad-research`.
4. Move it into your Claude skills folder:
   - macOS / Linux: `~/.claude/skills/`
   - Windows: `%USERPROFILE%\.claude\skills\`

If you have never used Claude skills before, the `.claude/skills/`
folder may not exist yet. Create it first.

Restart Claude Code. The skill auto-loads.

### 4. Test
In Claude Code, type:
```
Run MAD-build "write a one-page summary of attention mechanisms for a master's student".
```
Or, with a sample paper:
```
MAD-research on sample.pdf as a top-5 referee stress test.
```

## How it works (one screen)

### MAD-build (production)
1. Claude scaffolds a project folder.
2. Both Claude and Codex draft the same task in parallel.
3. Each reviews the other's draft.
4. Each revises.
5. Claude merges into a final version and writes a debrief.

Result: a folder with both drafts, both reviews, both revisions, and the
final synthesis. Total time: 10–30 minutes for a typical task.

### MAD-research (audit)
1. Claude reads the document (PDF or markdown) and prepares a text version
   with page locators for Codex.
2. Round 1 — three streams produce independent audits.
3. Round 2 — anonymized cross-critique. Each stream attacks the others'
   weak points and defends strong ones.
4. Round 3 (optional, only triggered by genuine unresolved disagreement).
5. Synthesis — Claude blinds the inputs and writes `final_memo.md` against
   a locked rubric. Auto-rejects ungrounded claims.

Result: a session folder with all round outputs and the final memo,
including a "Points rejected" trail you can audit yourself. Total time:
20–60 minutes depending on document length and Codex's rate limits.

## Trigger phrases

| You say | Skill runs |
|---|---|
| "Run MAD on <file>"  | infers mode from file type |
| "MAD-build <task>"   | production |
| "MAD-research <file>"| audit |
| "stress-test this paper" | audit |
| "referee report on this" | audit |
| "have Codex help me build X" | production |
| "competition agent on this" | production |

## What you get

For each run, a folder in your current working directory:

```
mad_sessions/<YYYYMMDD-HHMM-slug>/
  meta.json            - session ID, mode, Codex version, audit trail
  input/               - copy of your source document(s)
  round1/              - three independent audits (research mode)
  round2/              - anonymized cross-critiques (research mode)
  round3/              - only if a real disagreement remains
  final_memo.md        - the synthesis (research mode)
   OR
  final/               - merged final artifact (build mode)
  README_DEBRIEF.md    - what each agent contributed (build mode)
```

## What this skill does NOT do

- Does not run on documents you've explicitly marked confidential. Read
  `helpers/safety_notes.md` before sending sensitive material — both
  Claude and Codex send to their respective cloud APIs.
- Does not summarize the document before Round 1 — that would
  contaminate the independent assessments.
- Does not provide confidence scores or letter grades. Plain-language
  verdicts only.
- Does not silently substitute Claude for a failed Codex stream while
  calling the result multi-model.

## Troubleshooting

If something goes wrong, run the doctor by asking Claude:
> Run the mad-research doctor.

It checks Node, npm, Codex version, Codex flags, authentication, and disk
write access. Each check tells you exactly what to fix if it fails.

## Citing this skill

If you use the skill in published work, please cite:

> Havránek, T., & Iršová, Z. (2026). *Research Audit Protocols: Duel +
> MAD, v3.* GitHub repository. DOI to follow.

## License

CC-BY-4.0. Use, modify, share. Credit the authors.
