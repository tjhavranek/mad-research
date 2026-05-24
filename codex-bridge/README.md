# codex-bridge

The minimal "let Claude call Codex" skill. No protocol, no synthesis.

Install just this skill if you only want one-shot Codex calls from
Claude. Install `mad-build` if you want the structured collaboration
protocol. Install `mad-research` if you want the document audit.

See the [top-level README](../README.md) for the install commands.

## What it gives you

After install, you can ask Claude in natural language:

```
Have Codex draft a one-paragraph plain-English explanation of WAIVE.
Ask Codex to review the R script I just wrote.
Use Codex to extract the tables from sample.pdf into markdown.
Run Codex on this CSV and tell me anything that looks odd.
```

Claude will run the codex-bridge protocol: doctor checks pass,
sensible sandbox defaults, prompt via stdin file, output captured to
disk and shown to you.

## What you don't get

- No structured rounds. No cross-review. No synthesis. No audit
  trail beyond the single output file.
- If you want any of those, use `mad-build` or `mad-research`.
