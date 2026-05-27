# Doctor — pre-flight checks for gemini-bridge

Run this before invoking gemini-bridge. If any check fails, tell the
user exactly what to install. Do not proceed silently.

## Checks (in order)

### 1. Claude Code is the host
You are running inside Claude Code. If this skill is being executed
elsewhere, abort with a friendly message.

### 2. Node.js is installed
```sh
node --version    # expected: v18 or higher
```
If missing: tell the user "Install Node.js from https://nodejs.org
(LTS version). After installation, open a fresh terminal." Do not try
to install it automatically.

### 3. npm is on PATH
```sh
npm --version
```
If missing but Node is present: the user's PATH is broken. Tell them
to reinstall Node.js with the default installer options.

### 4. Gemini CLI is installed
```sh
gemini --version    # expected: 0.43.x or higher
```
If missing:
```sh
npm install -g @google/gemini-cli
```

### 5. Gemini CLI flags are compatible
Run `gemini --help` and look for these tokens in the output:
- `-p` / `--prompt`
- `-m` / `--model`
- `-o` / `--output-format`
- `--approval-mode`
- `--skip-trust`
- `--include-directories`

If any are missing, the user's gemini-cli version is incompatible.
Tell them which flag is missing and offer pinning to a tested
version: `npm install -g @google/gemini-cli@<known-good>`.

### 6. Authentication is configured

Check `~/.gemini/settings.json` and `~/.gemini/` for cached OAuth
tokens, OR check `GEMINI_API_KEY` env var. Tell the user which path
is active:

- **OAuth (most common, free 1000 req/day):** `~/.gemini/` contains
  cached tokens. On Windows this is
  `C:\Users\<user>\.gemini\`.
- **API key:** `GEMINI_API_KEY` set in environment.
- **Vertex AI (enterprise):** `GOOGLE_GENAI_USE_VERTEXAI=true` set in
  environment.

If NONE are configured, walk the user through the first-time OAuth:

```
gemini --skip-trust -p "say hi" -o json
```

(With `echo Y |` prepended on the very first call, because the CLI
asks `Opening authentication page in your browser. Do you want to
continue? [Y/n]:`.) After the browser sign-in, tokens persist and
this prompt does not appear again.

Once OAuth is cached: **every subsequent call must set
`GOOGLE_GENAI_USE_GCA=true` as an env var on the command line.** This
is per-call, not one-time. Doctor's smoke-test step does this.

### 7. Smoke-test round trip
Once auth is configured:
```sh
GOOGLE_GENAI_USE_GCA=true gemini --skip-trust -p 'Say only OK' -o json
```
Expected: a JSON object on stdout containing `"response": "OK"` (plus
`session_id` and `stats`). The preceding "Warning: Windows 10
detected..." lines are normal — the skill's output parser skips them.

If the smoke-test fails:
- Exit 41 → wrong auth env var, or no auth configured.
- Exit 55 → `--skip-trust` missing.
- JSON missing `response` → either the model declined or the prompt
  format was wrong. Run with `-d` to debug.

### 8. Disk write inside session folder
The skill will create `mad_sessions/<YYYYMMDD-HHMM-slug>/` in the
user's current working directory. Verify the cwd is writable.

## What to print to the user after checks

If all pass:
```
Pre-flight OK (gemini-bridge).
  node:        v22.x
  gemini-cli:  0.43.x
  flags:       ok
  auth path:   OAuth (1000 req/day, tokens cached)
  smoke test:  ok
Session folder will be: <path>
```

If any fail: print the failed step, the suggested fix, and stop. Do
not proceed with the gemini call until the user resolves it.

## What NOT to do

- Don't try to install Node or gemini-cli yourself. The user must
  own those installs.
- Don't suppress a failed check. The whole point of the doctor is
  visibility.
- Don't assume `gemini` is on PATH in this shell just because it
  is in another. PATH inheritance differs by terminal.
- Don't bypass the OAuth env-var requirement (`GOOGLE_GENAI_USE_GCA`)
  to "make it work" — the user's auth path needs to be explicit so
  rate-limit and privacy implications are visible.
