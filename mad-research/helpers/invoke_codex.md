# How to invoke Codex from any of these skills

**Verify your installed version first.** Run `codex exec --help` and confirm
the documented flags include `--cd`, `--skip-git-repo-check`, `--sandbox`,
and `--output-last-message`. If any are missing, run the doctor
(`helpers/doctor.md`) and tell the user their Codex version is
incompatible.

Tested-version status, stated precisely: the family was developed and
smoke-tested against `@openai/codex` 0.13.x; on 0.133.0 the documented
testing covers **read-only** `codex exec` runs (the bundled `mad-research`
examples). **`--sandbox workspace-write` on 0.133 has a reproduced
silent-artifact-loss failure** (see CHANGELOG v0.81): Codex reports success
but expected output files are missing. Before relying on workspace-write
(the `mad-build` default), run the doctor and verify a smoke-test file
actually lands on disk; if it does not, pin a 0.13.x version
(`npm install -g @openai/codex@<version>`).

## Safe defaults

Smoke testing on Windows + PowerShell with stdin redirected from a file
confirmed that `--sandbox read-only` and `--sandbox workspace-write` both
run non-interactively without hanging. The
`--dangerously-bypass-approvals-and-sandbox` flag is **not** the default
in any skill; it is reserved for an explicit `--unsafe-yolo` opt-in.

| Use case | Default sandbox |
|---|---|
| `mad-research` audit (read-only by design) | `--sandbox read-only` |
| `mad-build` collaboration (writes files into session folder) | `--sandbox workspace-write` |
| `codex-bridge` one-shot, no file output expected | `--sandbox read-only` |
| `codex-bridge` one-shot, Codex must write files | `--sandbox workspace-write` |
| User explicitly opts in via `--unsafe-yolo` or natural-language "unsafe yolo Codex" | `--dangerously-bypass-approvals-and-sandbox` |

`--sandbox read-only` lets Codex read files inside `--cd` but not write
anywhere on disk. Codex's response still reaches the orchestrator via the
`--output-last-message` channel, which is a separate path from filesystem
writes and is unaffected by the sandbox.

`--sandbox workspace-write` lets Codex read AND write files inside `--cd`,
which the orchestrator gives as the per-session folder. Writes outside
that folder are blocked.

## Prompt files, not prompt arguments

Long manuscripts plus quotes contain `&`, `|`, `<`, `>`, `%`, `!`, and
newlines — all of which break shell command-line parsing. **The skill
writes each role's prompt to a file and feeds it via stdin**, never as
a positional argument:

### macOS / Linux (canonical for `mad-research` synthesis)

```bash
codex exec \
  --cd "<session_path>" \
  --skip-git-repo-check \
  --sandbox read-only \
  --output-last-message "<session_path>/<output.md>" \
  - \
  < "<session_path>/<prompt_file>"
```

For `mad-build` (or any task that requires Codex to write files into the
session folder), swap `--sandbox read-only` for
`--sandbox workspace-write`.

The trailing `-` tells Codex to read the prompt from stdin (per
`codex exec --help`: "if `-` is used, instructions are read from
stdin").

### Windows (cmd.exe)

```cmd
codex.cmd exec --cd "<session_path>" --skip-git-repo-check --sandbox read-only --output-last-message "<session_path>\<output.md>" - < "<session_path>\<prompt_file>"
```

### Windows (PowerShell — via cmd trampoline)

PowerShell's pipeline does not interoperate cleanly with native-binary
stdin. Use `cmd /c` and pass the cmd command as a single string:

```powershell
$cmd = 'codex.cmd exec --cd "{0}" --skip-git-repo-check --sandbox read-only --output-last-message "{1}" - < "{2}"' -f $session_path, $output_file, $prompt_file
cmd /c $cmd
```

**Do not interpolate the prompt body into the command string.** The
prompt goes in `$prompt_file`. Only paths and option values are
interpolated.

## Unsafe-yolo mode (explicit opt-in only)

If the user says "run unsafe-yolo Codex" or invokes a hypothetical
`--unsafe-yolo` flag, swap the `--sandbox <mode>` flag for
`--dangerously-bypass-approvals-and-sandbox`. **Always log this choice
to `meta.json`** and surface it in the final output, e.g. "this run
used unsafe-yolo Codex (no sandbox)."

```bash
codex exec \
  --cd "<session_path>" \
  --skip-git-repo-check \
  --dangerously-bypass-approvals-and-sandbox \
  --output-last-message "<session_path>/<output.md>" \
  - \
  < "<session_path>/<prompt_file>"
```

Honest framing: the bypass flag disables Codex's sandbox entirely. Its
documented warning is "EXTREMELY DANGEROUS. Intended solely for running
in environments that are externally sandboxed." Reserve it for users
who knowingly want it and understand the trade-off. None of the
default workflows in this skill family use it.

## What each flag does

| Flag | Purpose |
|---|---|
| `--cd <path>` | Sets Codex's working directory to the session folder. |
| `--skip-git-repo-check` | Session folders are not git repos. |
| `--sandbox read-only` | Codex can read inside `--cd`, cannot write to disk. Safe default for `mad-research` audits. |
| `--sandbox workspace-write` | Codex can read and write inside `--cd`. Safe default for `mad-build` collaboration. |
| `--dangerously-bypass-approvals-and-sandbox` | No sandbox; arbitrary shell access. Behind explicit `--unsafe-yolo` opt-in only. |
| `--output-last-message <file>` | Writes Codex's final message to the named file. Independent of sandbox. |
| `-` | Read prompt from stdin (so the prompt comes from a file, safely). |

## Running in parallel vs. sequentially

`mad-research` Round 1 has three independent streams. Two are Codex,
one is Claude. Sequential is easier to debug; in parallel cuts
wall-clock time roughly in half.

For parallel, use Claude Code's `Bash` tool with
`run_in_background: true` twice, then `TaskOutput` with `block: true`
to wait. **Do not launch two Codex processes from the same shell with
`&` on Windows** — that path is not robust.

## When `codex exec` returns non-zero or empty

Read the output log. Common causes:

- `Reading additional input from stdin...` indefinitely → prompt file
  reference wrong; verify the path resolves.
- `cwd is not a git repository` → `--skip-git-repo-check` missing.
- Empty output, exit 0 → likely rate-limited or context exceeded.
  Re-run with a shorter prompt and tell the user.
- `authentication required` → user must run `codex` interactively to
  log in (one-time).
- `sandbox does not allow write` while `--sandbox read-only` → the
  task requires file writes; the skill should switch to
  `workspace-write` for this specific call and document why.

When recovery fails: do not silently substitute Claude for the failed
stream. Tell the user, write the failure into `meta.json`, and ask
whether to abort or proceed with a labeled degraded run.

## What NOT to do

- Don't use `--approval-policy` — not a documented flag.
- Don't use `--path` — not a documented flag (use `--cd`).
- Don't pass the prompt as a positional argument when it might contain
  shell metacharacters — use stdin redirection from a file.
- Don't run Codex from inside a git repo without `--cd` to redirect —
  it may pick up your real repo's state and misbehave.
- Don't default to `--dangerously-bypass-approvals-and-sandbox`. The
  safer sandbox modes work non-interactively given proper stdin
  redirection. The bypass flag is unsafe and unnecessary outside an
  explicit user opt-in.
