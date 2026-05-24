# How to invoke Codex from this skill

**Verify your installed version first.** Run `codex exec --help` and confirm
the documented flags include `--cd`, `--skip-git-repo-check`, `--sandbox`,
`--output-last-message`, and `--dangerously-bypass-approvals-and-sandbox`.
If any are missing, run the doctor (`helpers/doctor.md`) and tell the user
their Codex version is incompatible with this skill version.

This skill has been tested against `@openai/codex` versions 0.13x.

## Honest framing of the flags

`codex exec` is documented as "Run Codex non-interactively." In practice
on Windows + PowerShell, without `--dangerously-bypass-approvals-and-sandbox`
the first model-generated shell command can still block on a confirmation
that has nowhere to go, which makes the orchestrator hang.

So the canonical invocation **uses the bypass flag**. The flag's
documented meaning is: "Skip all confirmation prompts and execute commands
without sandboxing. EXTREMELY DANGEROUS. Intended solely for running in
environments that are externally sandboxed."

In our context, "externally sandboxed" means: the user's machine, the
session folder under `mad_sessions/`, and the prompts (which come from
this skill, not from arbitrary internet input). The risk is **not zero** —
a manipulated source document could in principle inject instructions
asking Codex to run shell commands. Mitigations:

- The role prompts tell Codex to write a markdown analysis, not run shell.
- The session folder is the cwd; if Codex writes outside it, you can
  detect that from the audit trail.
- `helpers/safety_notes.md` covers what NOT to feed into this skill.

If the user wants strict sandboxing instead, use **Strict mode** below.

## Prompt files, not prompt arguments

Long manuscripts plus quotes contain `&`, `|`, `<`, `>`, `%`, `!`, and
newlines — all of which break shell command-line parsing. **The skill
writes each role's prompt to a file and feeds it via stdin**, never as a
positional argument:

### macOS / Linux (canonical)

```bash
codex exec \
  --cd "<session_path>" \
  --skip-git-repo-check \
  --dangerously-bypass-approvals-and-sandbox \
  --output-last-message "<session_path>/<output.md>" \
  - \
  < "<session_path>/<prompt_file>"
```

The trailing `-` tells Codex to read the prompt from stdin (per the
`codex exec --help`: "if `-` is used, instructions are read from stdin").

### Windows (cmd.exe — recommended)

```cmd
codex.cmd exec --cd "<session_path>" --skip-git-repo-check --dangerously-bypass-approvals-and-sandbox --output-last-message "<session_path>\<output.md>" - < "<session_path>\<prompt_file>"
```

### Windows (PowerShell — via cmd trampoline)

PowerShell's pipeline does not interoperate cleanly with native-binary
stdin. Use `cmd /c` and pass the cmd command as a single string:

```powershell
$cmd = 'codex.cmd exec --cd "{0}" --skip-git-repo-check --dangerously-bypass-approvals-and-sandbox --output-last-message "{1}" - < "{2}"' -f $session_path, $output_file, $prompt_file
cmd /c $cmd
```

**Do not interpolate the prompt body into the command string.** The prompt
goes in `$prompt_file`. Only paths and option values are interpolated.

## Strict mode (for users who want sandboxing)

If the user prefers strict sandboxing at the cost of needing to approve
prompts mid-run, replace `--dangerously-bypass-approvals-and-sandbox` with
`--sandbox workspace-write`. The skill cannot run fully autonomously in
this mode; tell the user this up front.

## What each flag does

| Flag | Purpose |
|---|---|
| `--cd <path>` | Sets Codex's working directory to the session folder. |
| `--skip-git-repo-check` | Session folders are not git repos. |
| `--dangerously-bypass-approvals-and-sandbox` | Skips approvals; no sandbox. Required for fully autonomous orchestration. Honest: this gives Codex full local execution authority. |
| `--output-last-message <file>` | Writes Codex's final message to the named file. |
| `-` | Read prompt from stdin (so the prompt comes from a file, safely). |

## Running in parallel vs. sequentially

Round 1 has three independent streams. Two are Codex, one is Claude.
Sequential is easier to debug; in parallel cuts wall-clock time roughly
in half.

For parallel, use Claude Code's `Bash` tool with `run_in_background: true`
once per Codex stream, then `TaskOutput` (block: true) to wait. **Do not
launch two Codex processes from the same shell with `&` on Windows** —
that path is not robust.

## When `codex exec` returns non-zero or empty

Read the output log. Common causes:

- `Reading additional input from stdin...` indefinitely → prompt file
  reference wrong; verify the path resolves.
- `cwd is not a git repository` → `--skip-git-repo-check` missing.
- Empty output, exit 0 → likely rate-limited or context exceeded. Re-run
  with a shorter prompt and tell the user.
- `authentication required` → user must run `codex` interactively to log
  in (one-time).

When recovery fails: do not silently substitute Claude for the failed
stream. Tell the user, write the failure into `meta.json`, and ask
whether to abort or proceed with a labeled degraded run.

## What NOT to do

- Don't use `--approval-policy` — does not exist.
- Don't use `--path` — does not exist (use `--cd`).
- Don't pass the prompt as a positional argument when it might contain
  shell metacharacters — use stdin redirection from a file.
- Don't run Codex from inside a git repo without `--cd` to redirect — it
  may pick up your real repo's state and misbehave.
- Don't claim the bypass flag is "still sandboxed." It isn't.
