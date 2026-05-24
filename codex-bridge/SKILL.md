---
name: codex-bridge
description: |
  Lets Claude call the Codex CLI for a single ad-hoc task — no
  protocol, no rounds, no synthesis. Use this when you want a one-shot
  second opinion or a quick external draft without the full MAD
  scaffolding.

  Triggers:
   - "have Codex <verb> <thing>"  (e.g. "have Codex draft X", "have Codex review file Y")
   - "ask Codex to <verb>"
   - "use Codex for <task>"
   - "run Codex on <file>"
   - Explicit "codex-bridge" mention.

  For structured Claude + Codex collaboration (drafts → cross-review →
  revisions → merge), use the mad-build skill instead.

  For an adversarial audit of a research document, use the
  mad-research skill instead.
---

# codex-bridge skill

A minimal foundation: just the verified `codex exec` invocation
pattern plus a doctor for pre-flight checks. No multi-round protocol,
no synthesis, no role assignment.

## When this skill is the right tool

- "Have Codex draft a Python function for X." — one-shot draft.
- "Have Codex review the file I just wrote." — one-shot review.
- "Ask Codex to extract the tables from this PDF and write them as
  markdown." — one-shot transformation.
- "Run Codex on this dataset and tell me what stands out."

## When this skill is NOT the right tool

- You want Claude AND Codex to both draft something and then critique
  each other → use **`mad-build`**.
- You want a structured adversarial audit of a paper or grant → use
  **`mad-research`**.

## What this skill does

1. Run `helpers/doctor.md` pre-flight (Node.js, Codex CLI, flags, auth).
2. Write the user's request to a prompt file under the user's `cwd`
   (e.g. `codex_bridge_runs/<YYYYMMDD-HHMM-slug>/prompt.txt`).
3. Call `codex exec` per `helpers/invoke_codex.md`. Use
   `--sandbox read-only` if the user's task is purely informational;
   `--sandbox workspace-write` if Codex needs to write file(s) into
   the run folder.
4. Capture Codex's output to `output.md` and present it to the user.

## What this skill does NOT do

- No multi-round protocol.
- No anonymization, no rubric, no synthesis.
- No assumption that Codex's output is verified — Claude may
  optionally read and comment, but does not run a structured audit on
  it.
- No `--dangerously-bypass-approvals-and-sandbox` by default. Users
  who want it must say so explicitly (e.g., "run unsafe-yolo Codex
  on...").

## Files

```
codex-bridge/
├── SKILL.md            — this file
├── helpers/
│   ├── invoke_codex.md — verified codex exec patterns (safe defaults)
│   └── doctor.md       — pre-flight checks
└── README.md           — what to install and why
```
