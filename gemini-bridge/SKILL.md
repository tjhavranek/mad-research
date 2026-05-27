---
name: gemini-bridge
description: |
  Lets Claude call the Gemini CLI for a single ad-hoc task — no
  protocol, no rounds, no synthesis. Parallel to codex-bridge but
  routes to a different model family (Google Gemini, OAuth or
  API-key auth, 1M context). Use this when you want a one-shot
  second opinion from a non-OpenAI model.

  Triggers:
   - "have Gemini <verb> <thing>"  (e.g. "have Gemini draft X", "have Gemini review file Y")
   - "ask Gemini to <verb>"
   - "use Gemini for <task>"
   - "run Gemini on <file>"
   - Explicit "gemini-bridge" mention.

  For one-shot Codex calls instead, use codex-bridge.
  For structured Claude + Codex collaboration, use mad-build.
  For an adversarial audit of a research document, use mad-research.

  mad-research does NOT use Gemini internally as of v0.9 — the
  three-stream audit and synthesis still run on Claude + Codex. This
  skill is a separate entry point.
---

# gemini-bridge skill

Minimal foundation: verified `gemini` invocation pattern plus a doctor
for pre-flight checks. No multi-round protocol, no synthesis.

## When this skill is the right tool

- "Have Gemini draft a Python function for X." — one-shot draft.
- "Have Gemini review the file I just wrote." — one-shot review.
- "Ask Gemini to extract the tables from this PDF and write them as
  markdown." — one-shot transformation.
- "Run Gemini on this dataset and tell me what stands out."
- "Use Gemini for a sanity check on what Codex just gave me." — a
  cheap second-provider opinion when stakes are unclear.

## When this skill is NOT the right tool

- You want a structured adversarial audit of a paper or grant →
  **`mad-research`** (still Claude + Codex; Gemini integration into
  mad-research is on the v1.0 backlog, conditional on evals).
- You want Claude AND Codex to draft and cross-review →
  **`mad-build`**.
- You want one-shot Codex (OpenAI) calls → **`codex-bridge`**.

## What this skill does

1. Run `helpers/doctor.md` pre-flight (Node.js, Gemini CLI, flags,
   auth method, one-trip OAuth or API-key check).
2. Write the user's request to a prompt file under the user's `cwd`
   (e.g. `mad_sessions/<YYYYMMDD-HHMM-slug>/prompt.txt` — same
   session-folder convention as the other skills).
3. Call `gemini` per `helpers/invoke_gemini.md`. Use
   `--approval-mode plan` for read-only tasks; `--approval-mode
   default` if the user explicitly wants Gemini to use tools (e.g.
   `read_file`).
4. Capture Gemini's stdout, find the last `{...}` JSON object, pluck
   `.response`, write it to `output.md`, and show the user.

## What this skill does NOT do

- No multi-round protocol.
- No assumption that Gemini's output is verified — Claude may
  optionally read and comment, but does not run a structured audit on
  it.
- No `--approval-mode yolo` by default. Users who want it must say so
  explicitly (e.g. "run unsafe-yolo Gemini on…").
- Does NOT replace codex-bridge. Both bridges can coexist; the user
  picks per task.

## Independence note (carries the v0.8 spirit)

`gemini-bridge` is a different model family from Claude and Codex.
That changes the provider mix for ad-hoc tasks but does NOT change
mad-research's audit-trail independence_signature — the formal
three-stream audit still uses Claude + Codex + Codex with fresh-Codex
synthesis, and the v0.8 disclosure still applies there. If you want a
quick second-provider opinion on a mad-research memo, run
gemini-bridge on the final memo separately; this is informal cross-
checking, not part of the audit.

## Files

```
gemini-bridge/
├── SKILL.md             — this file
├── helpers/
│   ├── invoke_gemini.md — verified gemini invocation patterns (safe defaults)
│   └── doctor.md        — pre-flight checks
└── README.md            — what to install and why
```
