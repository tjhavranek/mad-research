# Codex v0.3 verdict

Ship v0.3 today with the three converged changes.

## 1. Directory rename

Agree with renaming only the skill subdirectory: `mad-research-skill/` -> `mad-research/`.

The resulting clone path `mad-research/mad-research/` is mildly redundant but not a real hazard. Matching source directory to install destination matters more. Renaming the repo to `mad-skills` creates needless URL churn and breaks references already circulated in the v0.2 package. Do not pay that cost for v0.3.

## 2. Regex prompt-injection scanner

Drop the regex auto-reject. AI3 is right: academic prose and audit packets will legitimately quote terms like "ignore", "disregard", and "instructions". A regex gate would false-positive on normal evidence.

Keep the constrained packet schema, required headings, hard delimiters, and the consumer-prompt rule that packet text is evidence only, never instructions. Document that the regex scanner was considered and rejected because it adds fragility without real security.

## 3. Anti-tamper rule

The Step 8 anti-tamper rule is sufficient for v0.3 as documentation, with one condition: Codex's raw synthesis output must be retained as an audit artifact. Without the raw artifact, "string-comparable" is not enforceable.

The rule should explicitly protect verdict, top-N criticisms, minority report, and action list. Claude may format, fix broken markdown, verify quotations, and append audit metadata; it may not silently rewrite judgments. Automated diff enforcement can wait for v0.4.

## Additional flag

