# gemini-bridge

The minimal "let Claude call Gemini" skill. No protocol, no
synthesis. Parallel to `codex-bridge` but routes to Google Gemini.

Install just this skill if you want one-shot Gemini calls from
Claude. Install `codex-bridge` if you want Codex calls too. Install
`mad-build` for structured Claude+Codex collaboration. Install
`mad-research` for the document audit.

See the [top-level README](../README.md) for install commands.

## What it gives you

After install, you can ask Claude in natural language:

```
Have Gemini draft a one-paragraph plain-English summary of this CSV.
Ask Gemini to review the R script I just wrote.
Use Gemini to extract the tables from sample.pdf into markdown.
Run Gemini on this dataset and tell me anything that looks odd.
Use Gemini as a second opinion on what Codex just gave me.
```

Claude will run the gemini-bridge protocol: doctor checks pass,
safe-by-default approval mode, prompt via `-p` with the body in a
file, output captured to `output.md` and shown to you.

## What you don't get

- No structured rounds. No cross-review. No synthesis. No audit
  trail beyond the single output file.
- No integration into mad-research (yet). The three-stream audit
  still runs on Claude + Codex + Codex with fresh-Codex synthesis;
  the v0.8 `Independence:` line is unaffected. Gemini integration
  into mad-research is on the v1.0 backlog, conditional on a future
  comparative evals release.
- If you want multi-model synthesis, use `mad-build` (collaboration)
  or `mad-research` (audit) — both today are Claude+Codex pipelines.

## Cost / auth

- Free tier via Google sign-in (OAuth): 1,000 requests/day per
  Google account. Sufficient for most ad-hoc use.
- Or set `GEMINI_API_KEY` from Google AI Studio for API-key auth.
- One-time browser OAuth on first run. After that the skill picks up
  cached tokens from `~/.gemini/settings.json`.

Doctor will guide you through whichever path you pick.
