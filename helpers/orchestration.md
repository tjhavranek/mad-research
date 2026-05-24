# Orchestration: how Claude actually runs a session

This is the operational checklist Claude follows when a user triggers this
skill. It is written for the orchestrating Claude, not the user.

## Step 0: Detect intent

Apply the mode-detection priority order from `SKILL.md`. Stop at the first
match. If still ambiguous, ask **one** short clarifying question. Do not
guess silently.

Once decided, do not change the mode mid-run.

## Step 1: Run the doctor

Execute the checks in `helpers/doctor.md`. Apply the **pre-flight policy
from `SKILL.md`**:

- Mode A + Codex broken: ask user "proceed Claude-only or abort?"
- Mode B + Codex broken: by default abort with install instructions.

`SKILL.md` is the source of truth on this policy. This file (orchestration)
must match it.

## Step 2: Cost and consent gate (Mode B only)

For research audit, before any cloud call:

1. Compute a rough token estimate of the manuscript (chars / 4 is a fine
   approximation).
2. Compute pages count.
3. Display to user: estimated total tokens across all rounds and streams,
   approximate Codex cost (assume the user's OpenAI rate from
   `helpers/safety_notes.md`'s guidance), and the data-leaving-machine
   warning.
4. **Wait for confirmation.** Do not skip this step even if you think
   the user is impatient.

For Mode A this is optional — production runs are usually shorter and the
user already knows what they're paying for.

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

Create `mad_sessions/<session_id>/` with subfolders depending on mode.

Write `meta.json`:
```json
{
  "session_id": "<slug>-<timestamp>",
  "created_at": "<ISO 8601>",
  "mode": "build | research",
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

For Mode B PDFs, run the PDF preparation in `helpers/pdf_extraction.md`.
Write `input/manuscript_text.md` and `input/manuscript_meta.json`. If the
preparation flags unrecoverable issues (no preserved page boundaries,
image-only PDF, etc.) abort with a message.

## Step 6: Run rounds

### Mode A
Follow `prompts/mad_build_protocol.md` exactly.

### Mode B
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

## Step 7: Synthesis

Run synthesis per `prompts/synthesis.md`. The synthesis runs **inside
this same Claude session** but with strict input isolation:

- Synthesis sees: anonymized claim packets (`audit_X.md`, `audit_Y.md`,
  `audit_Z.md`), the rubric, the manuscript (for quote verification).
- Synthesis does NOT see: `meta.json`, role names, model names, prior
  conversation context with the user.

To achieve this in practice: before writing the synthesis output, read
back into your working memory only the claim packets and the rubric.
Treat the role mapping as off-limits until after `final_memo.md` is
written.

If the user wants stricter blinding, they can copy `audit_X.md`,
`audit_Y.md`, `audit_Z.md`, `rubric.md`, and the manuscript into a fresh
Claude Code window. Document this in the final memo's "Audit trail."

## Step 8: Final quote verification

After synthesis writes `final_memo.md`, verify every surviving
criticism's quote+locator against the source:

- For PDF source: the quote must appear on the cited page in the PDF.
- For markdown source: the quote must appear in the cited file at or
  near the cited line.

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
