# Doctor — pre-flight checks

Run this before invoking either MAD mode. If any check fails, tell the
user exactly what to install. Do not proceed silently.

## Checks (in order)

### 1. Claude Code is the host
You are running inside Claude Code. If this skill is being executed
elsewhere, abort with a friendly message.

### 2. Node.js is installed
```sh
node --version    # expected: v18 or higher
```
If missing: tell the user "Install Node.js from https://nodejs.org (LTS
version). After installation, open a fresh terminal." Do not try to
install it automatically.

### 3. npm is on PATH
```sh
npm --version
```
If missing but Node is present: the user's PATH is broken. Tell them to
reinstall Node.js with the default installer options.

### 4. Codex CLI is installed
```sh
codex --version
```
If missing:
```sh
npm install -g @openai/codex
```
Then ask the user to run `codex` once in a terminal to authenticate
(opens a browser, takes 30 seconds).

### 5. Codex CLI flags are compatible
Run `codex exec --help` and look for these tokens in the output:
- `--cd`
- `--skip-git-repo-check`
- `--sandbox`
- `--output-last-message`

If any are missing, the user's Codex version is incompatible with this
skill. Tell them which flag is missing and offer:
- Pinning to a tested Codex version: `npm install -g @openai/codex@<known-good>`.
- Or skip Codex and run Mode B as Claude-only (clearly labeled degraded).

### 6. Codex authentication
```sh
codex exec --skip-git-repo-check --sandbox read-only "say hi" < /dev/null    # macOS/Linux
cmd /c "codex.cmd exec --skip-git-repo-check --sandbox read-only `"say hi`" < NUL"  # Windows
```
If it returns a coherent reply within 30 seconds, auth is fine. If it
prints `authentication required` or similar, ask the user to run `codex`
once interactively in their terminal.

### 7. Disk write inside session folder
The skill will create `mad_sessions/<YYYYMMDD-HHMM-slug>/` in the user's
current working directory. Verify the cwd is writable. If not, ask the
user where to put the session folder.

## What to print to the user after checks

If all pass:
```
Pre-flight OK.
  node: v22.x
  codex: v0.x.y
  flags: ok
  auth: ok
Session folder will be: <path>
```

If any fail: print the failed step, the suggested fix, and stop. Do not
proceed with the MAD until the user resolves it.

## What NOT to do

- Don't try to install Node or Codex yourself. The user must own those
  installs.
- Don't suppress a failed check. The whole point of the doctor is
  visibility.
- Don't assume `claude` and `codex` are on PATH in this shell just
  because they are in another. PATH inheritance differs by terminal.
