# Orchestration: how Claude actually runs a mad-research session

This is the operational checklist Claude follows when triggered by
mad-research phrases ("MAD-research <file>", "stress-test this paper",
"referee report on this", etc.). It is written for the orchestrating
Claude, not for the user.

## Step 1: Run the doctor

Execute the checks in `helpers/doctor.md`. Apply the **pre-flight policy
from `SKILL.md`**: if Codex is missing or its auth is broken, **abort
with install instructions by default**. The audit promise requires three
streams plus the fresh-Codex synthesizer; running without Codex breaks
the contract.

If the user insists on proceeding anyway, offer a Claude-only run
clearly labeled "single-model audit" in the final memo — **never call
it MAD-research**.

`SKILL.md` is the source of truth on this policy. This file
(orchestration) must match it.

## Step 2: Cost and consent gate

Before any cloud call:

1. Compute a rough token estimate of the manuscript (chars / 4 is a fine
   approximation).
2. Compute pages count.
3. Display to user: estimated total tokens across all rounds and streams,
   approximate Codex cost (assume the user's OpenAI rate from
   `helpers/safety_notes.md`'s guidance), and the data-leaving-machine
   warning.
4. **Wait for confirmation.** Do not skip this step even if you think
   the user is impatient.

## Step 2.5: Mode selection (Bayesian Mode opt-in)

Default mode is **methodology/contribution audit** — no numeric
probabilities, plain-language verdicts only. This is what runs unless
Bayesian Mode is explicitly enabled.

**Bayesian Mode** is an opt-in mode for evaluating the truth of a
contested empirical claim, not just the soundness of the manuscript's
methodology. Trigger logic — orchestrator-proposes, user-confirms:

1. **If the user explicitly requested Bayesian Mode** (e.g., "MAD-research
   in Bayesian Mode," "evaluate the empirical claim X with Bayesian
   discipline"): go to step 3.

2. **If the user did not explicitly request it,** scan the manuscript
   for signals that the audit task is about the truth of a specific
   contested empirical claim rather than methodology soundness.
   Signals include: an abstract that asserts a controversial result
   the user has flagged in the request; a user request that names a
   contested empirical claim explicitly; a domain where the manuscript
   contradicts established consensus. If signals fire, propose to the
   user:

   > "This manuscript appears to make a contested empirical claim
   > about [X]. Should I run in Bayesian Mode? In that mode the
   > synthesis will produce a numeric posterior with a 80% interval
   > for the designated claim and require explicit prior/evidence/
   > posterior accounting. Otherwise I'll run in default
   > methodology-audit mode. Your call. (You can also supply your own
   > prior for the claim.)"

   If user says yes / supplies a claim and optional prior: go to step 3.
   If user says no or doesn't respond: run default mode.

3. **Bayesian Mode setup.** Confirm with user the **designated empirical
   claim** (one sentence, verbatim, written into `meta.json`). Confirm
   the **consensus prior baseline** (point estimate + 80% interval — the
   orchestrator can propose one for user confirmation). Record user's
   own prior if supplied. All three become inputs to the synthesis
   prompt's Bayesian-Mode appendix.

4. **Record the mode in `meta.json`**: `"mode": "default"` or
   `"mode": "bayesian"`. In Bayesian Mode also record:
   `"designated_claim"`, `"consensus_prior"`, `"user_prior"` (or
   `null` if not supplied).

Bayesian Mode does NOT change Round 1 stream prompts — the three role
streams (Methodologist, Evidence Auditor, Contribution Skeptic) still
audit the manuscript in their usual lanes. The Bayesian discipline
appears at synthesis only (see the appendix section in
`prompts/synthesis_codex_prompt.md`). The mode adds work to synthesis,
not to the streams.

**Read the failure-modes section in `helpers/safety_notes.md` before
running Bayesian Mode.** Six failure modes are documented there
(false precision, prior laundering, Bayes-factor theater, [EXTERNAL]
leakage, anti-prestige overcorrection, padding) and the user should
be made aware before opting in.

## Step 3: Page / token thresholds

If pages > 50 or estimated total tokens > 200k:
- Tell the user the run will be slow and expensive.
- Offer a chunked option: audit the first N pages first, decide whether to
  continue. (Chunking is documented in `helpers/pdf_extraction.md`.)
- Do not silently truncate.

If pages > 150 or total tokens > 500k: abort with a clear message.
Refusing to run is safer than silently producing degraded output.

## Step 4: Create the session folder

Slug = first 2-4 lowercase words from the user's request, dash-joined.
Timestamp = `YYYYMMDD-HHMM`. If collision, append `-1`, `-2`, etc.

Create `mad_sessions/<session_id>/` with the audit subfolders:
`input/`, `round1/`, `round2/`, optionally `round3/`,
`synthesis_packet/`.

Write `meta.json`:
```json
{
  "session_id": "<slug>-<timestamp>",
  "created_at": "<ISO 8601>",
  "skill": "mad-research",
  "user_request": "<verbatim>",
  "input_files": [...],
  "input_sha256": "<hash of concatenated inputs>",
  "rounds_planned": ["round1", "round2", "synthesis"],
  "codex_version": "<from doctor>",
  "codex_available": true | false,
  "host_session": "claude-code <version>",
  "stream_assignments": {
    "methodologist": "claude",
    "evidence_auditor": "codex",
    "contribution_skeptic": "codex"
  }
}
```

The `stream_assignments` map is **owned by orchestration only**. The
synthesis step (Step 7) must not read `meta.json` directly; it gets an
anonymized claim packet from you.

## Step 5: Prepare inputs

Copy user-provided source files into `input/`. Never modify the originals.

For PDF inputs, run the PDF preparation in `helpers/pdf_extraction.md`.
Write `input/manuscript_text.md` and `input/manuscript_meta.json`. If the
preparation flags unrecoverable issues (no preserved page boundaries,
image-only PDF, etc.) abort with a message.

## Step 6: Run rounds

1. **Round 1**: Run three streams.
   - Claude as Methodologist (reads original PDF directly).
   - Codex as Evidence Auditor (reads `manuscript_text.md`).
   - Codex as Contribution Skeptic (reads `manuscript_text.md`).
   Use separate `codex exec` calls. Output files: `round1/methodologist.md`,
   `round1/evidence_auditor.md`, `round1/contribution_skeptic.md`.

2. **Post-Round-1 validation** (run for each output file):
   - File non-empty? If not, mark stream failed and decide per failure
     policy in Step 1.
   - Output contains the required template sections? (regex on headings)
   - At least one quote in each criticism? (regex on `"..."`)
   - At least one locator per criticism? (regex on `p. \d+`, `§`, `eq.`,
     `Table`, etc.)
   - Extraction caveats section present?
   Failures: do not auto-fix. Tell the user that stream X produced
   substandard output, ask whether to retry or proceed.

3. **Round 2 anonymization**:
   For each stream, create a Round-2 input packet containing only the
   *other two* Round-1 outputs, with provider/role labels stripped and
   replaced by "Audit X" and "Audit Y". The mapping (X -> evidence,
   Y -> contribution; or whatever) is **per-stream** and lives only in
   your working memory + meta.json. Each stream sees a different mapping.

4. **Run Round 2 streams**. Same validation as Round 1.

5. **Round 3 trigger check**:
   - Two streams fundamentally disagree on a high-severity grounded
     criticism? Trigger.
   - A new high-severity grounded issue appeared in Round 2 that wasn't
     in Round 1? Trigger.
   - User explicitly forced it? Trigger.
   Otherwise skip.

6. If Round 3 runs: write `round3/round3_brief.md` naming the fault line,
   then run the three streams again per `prompts/round3_adaptive.md`.

## Step 7: Synthesis (fresh Codex exec)

Synthesis does not run in the orchestrating Claude session. It runs as
a fresh `codex exec` call against anonymized inputs plus the locked
rubric. Claude formats the returned memo and verifies quotes; Claude
does **not** write the verdict.

Why: the orchestrating Claude has already authored Round 1's
Methodologist stream and orchestrated anonymization. It cannot be a
fresh judge over its own output. The fresh Codex synthesizer has no
session history with the debaters. (Khan et al., ICML 2024 — debate
helps the judge most when debaters are stronger than the judge.)

### Procedure

1. **Build the synthesis packet** at `synthesis_packet/`:
   - `manuscript_text.md` (the source, with page locators).
   - `audit_X.md`, `audit_Y.md`, `audit_Z.md` — Round 1 + Round 2
     outputs of each stream, **fully anonymized** (provider labels
     stripped, role labels stripped). Use a randomized mapping for
     each run; the mapping lives ONLY in `meta.json`.
   - `round3/` subfolder if Round 3 ran (similarly anonymized).
   - `rubric.md` — the locked rubric, unmodified.
2. **Apply the packet schema** (see `helpers/packet_schema.md`).
   Each audit packet must contain the required section headings.
   Validation is structural only — the schema doc explains why
   the regex content scanner was removed. Defenses against
   prompt injection live in the consumer-prompt directives, not
   in pre-flight regex.
3. **Assemble the synthesis prompt** from
   `prompts/synthesis_codex_prompt.md`, substituting the session's
   `<output_path>` and ensuring the prompt is delivered via stdin
   (not as a positional argument).
4. **Dispatch Codex** with `--sandbox read-only` (synthesis is
   read-only; Codex writes the memo only via `--output-last-message`):
   ```
   codex exec --cd <session_path> --skip-git-repo-check \
              --sandbox read-only \
              --output-last-message <session>/final_memo_codex_raw.md \
              - < <session>/synthesis_packet/_codex_prompt.txt
   ```
5. **Receive the memo.** Codex's response is saved to
   `final_memo_codex_raw.md`. Read it into Claude's working memory.

### Fallback when Codex is unavailable

If the synthesis Codex call fails (auth, rate limit, fatal error),
the skill does NOT silently fall back to in-session Claude
synthesis and call the result "fresh." It writes a labeled
fallback memo with a header note: "SYNTHESIS NOTE: in-session
Claude synthesizer because Codex was unavailable. Not a fresh-
context synthesis." Then the orchestrating Claude does the
synthesis work itself per the same prompt template.

This preserves user choice (proceed with disclosure or abort) and
honesty (the audit trail records the degradation).

## Step 8: Final quote verification (Claude as formatter/verifier only)

After Codex returns the synthesized memo, the orchestrating Claude
operates under a strict **anti-tamper rule**: Claude formats and
verifies; Claude does NOT silently rewrite Codex's judgments.

### Anti-tamper rule

The Codex raw output `final_memo_codex_raw.md` is preserved as an
audit artifact for the lifetime of the session folder. Any
divergence between `final_memo_codex_raw.md` and the user-facing
`final_memo.md` must be explainable as one of these explicit
operations (and only these):

1. **Format-level polish.** Header level consistency, fenced code
   block styling, table alignment, link rendering, trailing
   whitespace, smart-quote normalization.
2. **Quote verification.** For each cited quote+locator: verify
   that the quote appears on the cited page or block in the
   source manuscript. If verification fails, MOVE the criticism
   into "Points rejected — locator failed verification" — do not
   silently fix the locator and do not delete the criticism
   without a row in "Points rejected." The verification result is
   logged in `meta.json` under `quote_verification`.
3. **Audit-trail metadata append.** Add to the "Audit trail"
   section: Codex version, total Codex calls, total elapsed
   time, "synthesis ran via fresh `codex exec`" note. Nothing
   else.

**Protected sections (Claude MUST NOT modify substantively):**

- The verdict paragraph.
- Top-N surviving criticisms (the list of items, their severity
  assignments, their evidence quotes, their "why it matters"
  text).
- The minority report.
- The action list.
- The trajectory ledger.

If Claude finds that one of these sections is materially wrong
(e.g., a criticism is incoherent), Claude does NOT fix it. Claude
re-runs Codex synthesis once with a brief reminder of the template,
or flags the issue to the user and asks how to proceed.

### Why retention of the raw output matters

"String-comparable modulo formatting" is only enforceable if the
raw output is kept. The raw output is therefore committed alongside
the final memo as part of the session folder. A user can audit
exactly what Codex produced versus what Claude shipped — and
catch any tamper, intentional or accidental.

### Procedure (in order)

1. Read `final_memo_codex_raw.md` into working memory.
2. Verify every quote+locator against source. Build the list of
   verification failures (if any).
3. For each verification failure: move the affected criticism into
   "Points rejected — locator failed verification" in the
   user-facing final memo.
4. Apply format-level polish.
5. Append audit-trail metadata.
6. Save as `final_memo.md`. Keep `final_memo_codex_raw.md` in
   place — do not delete it.
7. Surface to the user: where the memo is, and any
   quote-verification failures that affected the output.
8. If the raw output itself fails the template (missing required
   sections, no trajectory ledger), the orchestrator re-runs
   Codex once with a template reminder. If that also fails,
   surface to the user with the raw output preserved.

### Automated diff enforcement: future work

A future version may add an automated diff check between
`final_memo_codex_raw.md` and `final_memo.md` that fails the run
if changes exceed the allowed-operations whitelist. Currently
this is enforced at the documentation level only.

If `final_memo_codex_raw.md` itself violates the output template
(missing required sections, no trajectory ledger, etc.), the
orchestrator does NOT silently patch — it re-runs Codex once with
the template reminder. If that also fails, surface to the user.

## Step 8.5 (legacy): Original quote verification logic

This step still applies in the fallback case (Codex unavailable, the
orchestrating Claude did the synthesis itself). The procedure is the
same: verify every cited locator, move failures to "Points rejected."

If a quote fails verification, move the criticism to "Points rejected"
with reason "quote did not verify against source." Do not silently
correct the locator.

## Step 9: Wrap-up

- Update `meta.json` with end timestamp, rounds run, total duration,
  total Codex calls, total tokens (approximate).
- Print a summary to the user: where the session folder is, what to
  read first (`final_memo.md` or `final/`), one sentence on how the run
  went.
- If any stream failed or any quote failed verification, surface that
  prominently. Do not bury it.

## Resume / retry state

If the user interrupts mid-run (Ctrl-C, machine crash), the partial
session folder is left in place. On a subsequent invocation with
"resume mad on <session_id>", you can:
- Read `meta.json` to learn what was planned.
- Inspect which outputs exist and pass post-round validation.
- Re-run only the missing or failed streams.
- Proceed from where the run stopped.

Do not auto-resume without the user's explicit instruction.

## What you must never do

- Run the MAD without running the doctor first.
- Modify the user's source documents.
- Combine two role streams into one `codex exec` call.
- Modify the rubric during synthesis.
- Claim multi-model in the final memo when Codex failed.
- Skip the "Points rejected" section in the final memo.
- Bypass the cost gate to "save the user time."
- Auto-resume a partial session without explicit instruction.
