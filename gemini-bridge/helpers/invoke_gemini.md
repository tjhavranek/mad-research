# How to invoke Gemini from gemini-bridge

**Verify your installed version first.** Run `gemini --help` and
confirm the documented flags include `-p`, `-m`, `-o`, `--sandbox`,
`--approval-mode`, `--skip-trust`, and `--include-directories`. If
any are missing, run the doctor (`helpers/doctor.md`).

This skill has been smoke-tested against `@google/gemini-cli` 0.43.x
on Windows 10. Unlike Codex CLI, Gemini has no `--output-last-message
<file>` flag — the final assistant text is in the `response` field
of the JSON object on stdout. The skill captures stdout, finds the
last `{...}` block, parses, and plucks `.response`.

## Safe defaults

| Use case | Default approval mode |
|---|---|
| `gemini-bridge` one-shot, no file output expected | `--approval-mode plan` |
| `gemini-bridge` one-shot, Gemini must use read tools (read_file etc.) | `--approval-mode default` |
| User explicitly opts in via `--unsafe-yolo` or "unsafe-yolo Gemini" | `--approval-mode yolo` |

`--approval-mode plan` blocks edits and shell execution (analogous
to Codex `--sandbox read-only`). `default` allows tools but with
auto-approval policies — fine for headless audit tasks because the
skill writes prompts that don't ask for edits. `yolo` is the
explicit opt-in path.

The CLI also has `--sandbox`/`-s` for OS-level sandboxing (macOS
Seatbelt, Docker/Podman, Windows Native Sandbox, gVisor, LXC), but
those add setup overhead and are not required for read-only tasks.
Use them when stakes are high or when you explicitly opt in.

## Required env var for OAuth path

If the user authenticated via Google sign-in (the default free-tier
path), **`GOOGLE_GENAI_USE_GCA=true` must be set on every
invocation**, even after `~/.gemini/settings.json` has cached
tokens. Without it, the CLI exits with code 41 asking which auth
method to use.

Alternative auth paths: set `GEMINI_API_KEY=<key>` (from Google AI
Studio) or `GOOGLE_GENAI_USE_VERTEXAI=true` (enterprise). The
invocation pattern is the same; only the env var differs. The
doctor reports which path is configured.

## Canonical invocation (macOS / Linux / Git Bash on Windows)

```bash
GOOGLE_GENAI_USE_GCA=true gemini \
  --skip-trust \
  --approval-mode plan \
  -o json \
  -m gemini-3-flash-preview \
  --include-directories "<session_path>" \
  -p "$(cat <session_path>/<prompt_file>)" \
  > "<session_path>/<raw_output.json>"
```

Then parse: find the last `{...}` block in the raw output (the CLI
prints non-JSON warning lines before the JSON), `JSON.parse`,
extract `.response`, write to `<session_path>/output.md`.

## Why no stdin redirection?

Codex supports `codex exec ... - < prompt_file` (positional `-` reads
prompt from stdin). Gemini's `-p` flag takes the prompt inline; piping
into `gemini -` is NOT a documented pattern. The skill therefore
reads the prompt file into memory and passes it via `-p
"$(cat prompt_file)"`. This is fine for prompts up to several hundred
KB; quoting is handled by the shell because the prompt body is
captured by `$(…)` not by the command line.

**Do not interpolate the prompt body into a literal string in a
PowerShell or cmd command.** That re-introduces the shell-escaping
problems that motivated stdin redirection in Codex. Use `$(cat …)` in
bash, or write a temp wrapper script that reads the file in Python /
Node.

## Windows (cmd.exe)

Bash on Windows (Git Bash, MSYS2) works with the canonical pattern
above. From cmd.exe directly, set the env var inline:

```cmd
set GOOGLE_GENAI_USE_GCA=true && gemini --skip-trust --approval-mode plan -o json -m gemini-3-flash-preview --include-directories "<session_path>" -p "<short_inline_prompt>" > "<session_path>\raw_output.json"
```

For long prompts on cmd.exe, write a helper Python script that:
1. Reads the prompt file.
2. `subprocess.run(["gemini", "--skip-trust", "--approval-mode", "plan",
   "-o", "json", "-m", "gemini-3-flash-preview",
   "--include-directories", session_path, "-p", prompt_text],
   env={**os.environ, "GOOGLE_GENAI_USE_GCA": "true"},
   capture_output=True, text=True, timeout=300)`
3. Locates the last `{...}` in stdout, parses, extracts `.response`,
   writes `output.md`.

Helper script lives at `helpers/invoke_gemini.py` if the user has
Python available; otherwise the bash form is the recommended path.

## Windows (PowerShell)

PowerShell 5.1 has the same native-binary stdin quirks as with Codex.
Use the bash form via Git Bash (recommended) or the cmd.exe form via
`cmd /c`:

```powershell
$cmd = 'set GOOGLE_GENAI_USE_GCA=true && gemini --skip-trust --approval-mode plan -o json -m gemini-3-flash-preview --include-directories "{0}" -p "<short_inline_prompt>" > "{1}"' -f $session_path, $raw_output_file
cmd /c $cmd
```

For non-short prompts on PowerShell, the Python helper is the safer
path.

## Output parsing — the only awkward part

Gemini prints non-JSON warnings to stdout BEFORE the JSON object:

```
Warning: Windows 10 detected. ...
Warning: True color (24-bit) support not detected. ...
Ripgrep is not available. Falling back to GrepTool.
{
  "session_id": "…",
  "response": "<the actual text>",
  "stats": { … }
}
```

The robust parse:

```python
import json, re, pathlib
raw = pathlib.Path("raw_output.json").read_text(encoding="utf-8")
# Find the last balanced {...} that contains "response":
matches = re.findall(r"\{.*\}", raw, flags=re.DOTALL)
obj = json.loads(matches[-1]) if matches else {}
text = obj.get("response", "")
pathlib.Path("output.md").write_text(text, encoding="utf-8")
```

Or in shell, if `jq` is available:

```bash
sed -n '/^{/,$p' raw_output.json | jq -r .response > output.md
```

(`sed -n '/^{/,$p'` skips lines before the first `{` at column 0.)

## What each flag does

| Flag | Purpose |
|---|---|
| `-p "<text>"`, `--prompt` | The prompt body. Forces non-interactive mode. |
| `-m`, `--model` | Model selection. Pin a specific model (e.g. `gemini-3-flash-preview`) for deterministic behavior. `auto` is the default and routes between a flash-lite router and a main model. |
| `-o json`, `--output-format json` | Structured output to stdout: `{session_id, response, stats}`. |
| `--approval-mode plan` | No edits, no shell. Safe default for one-shot reads. |
| `--approval-mode default` | Tools allowed (e.g. `read_file`) with auto-policy. Use when the task needs file reads. |
| `--approval-mode yolo` | All tools auto-approved. Behind explicit `--unsafe-yolo` opt-in. |
| `--skip-trust` | Trust current workspace, bypass folder-trust check. Required for headless use in non-trusted folders. |
| `--include-directories "<path>"` | Expose additional directories to the model's file-read tools. Comma-separate multiple paths. |
| `-s`, `--sandbox` | OS-level sandbox (Seatbelt / Docker / Windows / gVisor / LXC). Not required for plan mode; useful for higher-stakes tasks. |
| `-d`, `--debug` | Verbose logging. |

## Unsafe-yolo mode (explicit opt-in only)

If the user says "run unsafe-yolo Gemini" or invokes a hypothetical
`--unsafe-yolo` flag, swap `--approval-mode plan` (or `default`) for
`--approval-mode yolo`. **Always log this choice to the session's
`meta.json`** and surface it in the final output, e.g. "this run used
unsafe-yolo Gemini (all tools auto-approved)."

## Running in parallel vs. sequentially

Gemini-bridge is one-shot, so parallel within one call is N/A. If
the user fires several gemini-bridge invocations concurrently
(e.g., to compare outputs across prompts), use Claude Code's `Bash`
tool with `run_in_background: true` per call. Each call costs 1 of
the 1000 daily free-tier requests per Google account.

## When `gemini` returns non-zero or empty

Read the output log. Common causes:

- Exit 41 with `Please set an Auth method…` → `GOOGLE_GENAI_USE_GCA`
  env var not set on this call, OR no auth configured. Doctor catches
  this.
- Exit 55 with `not running in a trusted directory` → `--skip-trust`
  flag missing.
- Exit 0 but empty `.response` → likely rate-limited (1000/day per
  account) or the prompt asked for something the model declined.
- `Opening authentication page in your browser. Do you want to
  continue? [Y/n]:` hanging → first-time OAuth. The user must
  complete the interactive sign-in once. After that, this prompt
  does not appear.
- JSON parse failure on output → there were no warnings stripped, or
  the model returned an unexpected format. Fall back to `-o text`
  and pipe stdout straight to output.md.

When recovery fails: do not silently substitute Codex or Claude for
the failed Gemini call. Tell the user, write the failure into the
session's `meta.json` (if one exists), and ask whether to abort or
proceed with a labeled degraded run.

## What NOT to do

- Don't try to pipe the prompt into `gemini -`. That positional `-`
  is not a documented pattern. Use `-p "$(cat prompt_file)"` or the
  Python helper.
- Don't omit `GOOGLE_GENAI_USE_GCA=true` on OAuth-path runs. Every
  call needs it.
- Don't default to `--approval-mode yolo`. The safer modes work for
  read-only and tool-use tasks; yolo is the explicit opt-in.
- Don't treat a Gemini response as verified second opinion of a
  mad-research memo. It's an independent informal cross-check, not
  part of the audit chain — that distinction is documented in the
  SKILL.md's "Independence note" section.
