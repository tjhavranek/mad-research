# Synthesis — Claude as blinded structured synthesizer

**Important framing.** Claude (this session) has already seen the
orchestration of every round. We do not claim "fresh judge" — that would be
misleading. We claim **blinded structured synthesizer**: synthesis follows
the locked rubric, treats inputs as anonymized claim packets (Audit X / Y / Z
without role or model labels), and cannot add new evidence beyond what the
rounds produced.

If the user wants a truly blind synthesis, they can copy the anonymized
claim packets + `rubric.md` into a fresh Claude Code window and run
synthesis there. This is documented in the README as the high-stakes
option.

## Inputs (what synthesis sees)

1. The manuscript (read-only, for verifying quote+locator pairs).
2. All three Round 1 outputs, **anonymized** as Audit X, Audit Y, Audit Z.
   The mapping is stored in `meta.json` but not read here.
3. All three Round 2 outputs, anonymized the same way.
4. Round 3 outputs if Round 3 ran.
5. The locked `rubric.md` (must not be modified during synthesis).

## Locked rubric

Synthesis scores the manuscript on six equally-weighted criteria. **Do not
add, remove, or re-weight them.** See `rubric.md`. The rubric is locked
because a moving rubric defeats the auditability promise.

## Synthesis procedure

1. **Read all inputs.** Build a single list of every grounded criticism
   across all rounds, tagged by source (Audit X / Y / Z / Round 2 emergent /
   Round 3 emergent).

2. **Apply the eight non-negotiables.**
   - Strip any criticism without a quote+locator → "Points rejected — no
     locator."
   - Strip any criticism with a fabricated locator (page that does not
     exist) → "Points rejected — fabricated locator."
   - Tag external claims separately.

3. **Merge near-duplicates** across audits. When two audits raise the same
   point, the merged criticism is *stronger*; cite both as supporting
   sources. Never inflate severity beyond the highest individual rating.

4. **Score against the rubric.** For each criterion: prose verdict (no
   numeric score, no letter grade — this is intentional, see
   `rubric.md`).

5. **Identify the minority report.** The Contribution Skeptic stream (or
   whichever stream Round 3 designated) should have flagged at least one
   criticism for minority preservation. Include it under "Minority
   report" with the surviving counter-arguments named.

6. **Build the action list.** 3-7 prioritized fixes, each tied to a
   surviving criticism. Action items must be specific (not "improve
   identification").

## Output: `final_memo.md`

```
# MAD-research final memo

## Verdict
[4-6 sentences. Plain language. What is the paper's standing after audit?
What changes if every surviving criticism is addressed?]

## Top 5 surviving criticisms
For each: short claim, severity (High/Medium/Low), evidence
(quote+locator), supporting audit(s) (X / Y / Z / Round 2 / Round 3),
one-line "why it matters."

## Minority report
One grounded objection preserved even though it lost majority support.
Include: the claim, the source audit, the evidence, and the surviving
counter-arguments named.

## Points rejected under scrutiny
List every criticism dropped during synthesis. For each: short claim, one
of the rejection reasons (no locator / fabricated locator / contradicted by
a counter-quote at p. X / contradicted by author response in §Y / merged
with a stronger version). Do not omit this section — it is the audit trail.

## External considerations
Any [EXTERNAL]-tagged claims, kept separate from grounded criticism.

## Action list
3-7 specific, prioritized fixes. Each tied to a surviving criticism by
number.

## Audit trail
- Session ID: [from meta.json]
- Mode: default | --quick | --full
- Rounds run: [list]
- Codex version: [from doctor output]
- Streams: [3 if all ran; note any degradation]
- Extraction caveats from all streams: [merged list]
- Unverified references from all streams: [merged list]
```

## Anti-patterns in synthesis

- Do not add new evidence to rescue a weak criticism. Synthesis works with
  the round outputs only.
- Do not modify the rubric.
- Do not give numeric scores or letter grades.
- Do not silently merge external claims into grounded criticism.
- Do not omit "Points rejected" even if it looks unflattering.
- Do not let the verdict be more optimistic than the surviving criticisms
  warrant. If three high-severity issues survive, the verdict says so.
