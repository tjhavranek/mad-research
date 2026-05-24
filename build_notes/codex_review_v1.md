# Codex review v1

I read `SKILL.md`, `README.md`, `rubric.md`, `prompts/*`, and `helpers/*`,
and verified locally on Windows with `codex-cli 0.133.0`. Main verdict: the
protocol is promising, but v1 is not safe to ship to non-programmer users
until invocation, mode detection, and PDF traceability are tightened.

1. `helpers/invoke_codex.md` / "canonical invocation": the installed
`codex exec --help` does include `--cd`, `--skip-git-repo-check`,
`--sandbox`, and `--output-last-message`; syntax is basically current.
Exact discrepancy: the helper says `--dangerously-bypass-approvals-and-sandbox`
is "required for non-interactive runs" and that "the sandbox flag above still
confines writes." The installed help says the opposite: this flag skips
confirmations and executes "without sandboxing." So the canonical command sets
`--sandbox workspace-write` and then disables sandboxing. Fix: remove the
bypass flag, or explicitly require an external disposable OS sandbox. If
approval suppression is needed, test and document the current `-c` config
route rather than claiming this dangerous flag is confined.

2. `SKILL.md` / "Triggers" and `helpers/orchestration.md` / Step 0: mode
detection will still confuse real users. "write", "draft", and "design" collide
with "write a referee report", "draft response to referees", and "design an
identification strategy for my paper." A `.pdf` can be a software spec, and a
paper can be `.md`, `.tex`, or `.docx`. Fix: add a priority order: explicit
`MAD-build`/`MAD-research`; object classifier (paper/grant/manuscript/referee
versus repo/code/product); verb tie-breaker; one confirmation when mixed. Store
the confirmed mode in `meta.json` before running the doctor.

3. `prompts/round1_*`: the roles are directionally distinct but likely to
overlap. Methodologist and Evidence Auditor both touch robustness, statistics,
and implementation details; Contribution Skeptic will often restate
identification weaknesses as overclaim. The shared "Top 5 criticisms" template
reinforces convergence. Fix with harder lane boundaries: Methodologist must
name estimand, identifying assumption, threat, and diagnostic test; Evidence
Auditor must audit arithmetic, Ns, tables, citations, code/data availability,
and avoid identification unless it appears as an inconsistency; Contribution
Skeptic must compare abstract/conclusion/literature/policy claims to the
evidence and avoid re-arguing methods except as overclaim.

4. `helpers/orchestration.md`: the checklist is incomplete. Step 1 conflicts
with `SKILL.md`: orchestration says stop on any doctor failure, while `SKILL.md`
allows Mode A single-agent and Mode B degraded. Pick one policy. Missing steps:
preflight consent/cost gate from `helpers/safety_notes.md`; token/page threshold
and abort or chunk policy; exact research folder layout; prompt assembly rules;
log and output filenames; post-round validation that outputs are nonempty, have
quotes, have parseable locators, and include extraction caveats; per-stream
Round 2 anonymization mappings where each stream gets only the other two
audits; a synthesis package that prevents reading the role mapping in
`meta.json`; resume/retry state; and final verification that every surviving
quote traces to its source. Also reconcile README's "three rounds" language
with Round 3 being skipped by default.

5. `helpers/pdf_extraction.md`: the compromise is not fully safe. It preserves
locators relative to `manuscript_text.md`, not necessarily the original PDF.
Manual page breaks can drift; tables, figures, and scanned pages are lossy; PDF
page index and printed page number can differ. The anti-pattern saying not to
put the table of contents or references at the start but to "keep them late" is
dangerous if it reorders pages. Fix: never reorder pages; record both PDF page
index and printed label; add per-page first/last words and character counts to
`manuscript_meta.json`; require synthesis to verify final quotes against the
original PDF where possible; abort or clearly downgrade when page boundaries or
tables cannot be preserved.

6. Windows breakage for non-programmers: `README.md` assumes Git is installed;
add a ZIP install path. `helpers/invoke_codex.md` injects raw prompts into
`cmd /c`; quotes, `%`, `&`, `|`, `<`, `>`, `!`, newlines, and long manuscripts
can break the command or execute unintended shell syntax. Use prompt-file stdin
redirection, e.g. `codex.cmd exec ... - < prompt_file`, or a tested wrapper
script. The "PowerShell pipes hang" claim should be retested on 0.133.0. The
parallel PowerShell section names Claude Code `Bash`/`TaskOutput`, not
Windows-native steps. Finally, Unicode arrows/tree glyphs displayed as mojibake
in Windows PowerShell during this review; use ASCII in commands and logs.
