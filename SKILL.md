---
name: mad-research
description: |
  Run a multi-agent debate (MAD) between Claude (this session) and Codex CLI
  on either (a) any project where the user wants a second pair of eyes
  ("MAD-build" mode) or (b) a research document the user wants
  stress-tested as a referee would ("MAD-research" mode).

  Triggers:
   - "MAD-build <task>"      -> production mode
   - "MAD-research <file>"   -> research-audit mode
   - "Run MAD on <file>"     -> infer mode (see priority order below)
   - "stress-test this paper" / "referee report on this" -> research
   - "have Codex help me build X" / "competition agent on this" -> build

  When the user says any of the above, follow the protocol in this skill
  exactly. Do not improvise the rounds.
---

# mad-research skill

Two-mode orchestrator for Claude + Codex collaborations. Claude (you, in this
session) drives. Codex is invoked non-interactively via `codex exec`. The user
should never have to touch a terminal beyond the one-time install.

## Two modes

### Mode A: MAD-build (production)
Use when the user wants to **make something** with Codex: code, a draft, an
analysis, a plan. Four steps. See `prompts/mad_build_protocol.md`.

### Mode B: MAD-research (research audit)
Use when the user wants a **document stress-tested**. Three role streams,
three rounds, one blinded synthesis. See `prompts/` for the full protocol.

## Mode detection (priority order)

Apply in order. Stop at the first match.

1. **Explicit verb.** "MAD-build" -> A; "MAD-research" -> B.
2. **Object class.** A "paper", "manuscript", "grant", "proposal",
   "referee report", "preprint" -> B. A "repo", "code", "script",
   "module", "package", "draft" (non-academic) -> A.
3. **Verb tie-breaker.** "stress-test", "audit", "review", "critique" -> B.
   "build", "write", "implement", "design" (when no academic object) -> A.
4. **File-extension hint.** `.pdf` of a paper-like document -> B; `.py`,
   `.R`, `.js`, `.md` of code/draft -> A.
5. **Ask one short question** if still ambiguous. Do not guess.

Once decided, the mode is recorded in `meta.json` before the doctor runs.

## Pre-flight policy (run once per session)

Before starting either mode, run `helpers/doctor.md`. **If Codex is
missing or its auth is broken:**

- Mode A: still useful as a single-agent run. Tell the user clearly that
  Codex is unavailable and offer to proceed Claude-only or abort. The
  result is **not** called multi-model.
- Mode B: by default, **abort with install instructions**. The audit
  promise requires three streams. If the user insists, offer a
  Claude-only run clearly labeled "single-model audit" in the final
  memo — never "MAD-research."

This policy is the same in `helpers/orchestration.md`. If they disagree,
this file wins.

## How to invoke Codex

See `helpers/invoke_codex.md`. **Do not improvise flags.** The skill is
pinned against `@openai/codex` versions where the documented flags
include `--cd`, `--skip-git-repo-check`, `--sandbox`,
`--output-last-message`, and `--dangerously-bypass-approvals-and-sandbox`.

Prompts are passed to Codex via stdin from a prompt file, not as
positional arguments (long manuscripts contain shell metacharacters).

## Session storage

Each MAD run creates a session folder under the user's workspace:
`<cwd>/mad_sessions/<YYYYMMDD-HHMM-slug>/`. This keeps everything visible
and tied to the user's own version control. Never modify the user's
source documents -- copy them into `input/` first.

## What this skill does NOT do

- No Python preprocessing. Claude reads PDFs natively.
- No provider adapter layer. Two providers, two code paths, that's it.
- No HTML viewer.
- No marker files or polling -- `wait` on background jobs.
- No confidence scores anywhere.
- No fourth model.
- No mode beyond A and B.
- No silent substitution of Claude for a failed Codex stream while
  calling the result multi-model.

## Reading order for the orchestrating Claude

When triggered, read in this order:
1. This file.
2. `helpers/doctor.md` (run checks).
3. `helpers/orchestration.md` (the operational checklist).
4. Mode-specific files (`prompts/mad_build_protocol.md` or the Round 1/2/3
   + synthesis prompts).
5. `helpers/safety_notes.md` (warn the user about anything relevant
   before sending data).
