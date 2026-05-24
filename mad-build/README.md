# mad-build

Structured Claude + Codex collaboration for producing artifacts.

Four steps: independent drafts → cross-review → revisions → merge.
Each step writes versioned artifacts to disk so you can audit who
contributed what and which criticisms drove which changes.

See the [top-level README](../README.md) for install commands.

## What it gives you

After install, you can ask Claude:

```
MAD-build a Python script that takes a CSV of meta-analysis data
and produces a funnel plot with PEESE correction overlaid.

MAD-build a one-page response to referees for our JPE submission,
addressing the three points in their letter.

Have Codex help me design an experiment to test whether AI-generated
referee reports identify the same weaknesses as human referees.
```

Claude will scaffold the session folder, draft its version, dispatch
Codex to draft a parallel version, run a cross-critique round, run
a revisions round, and merge. The full transcript stays on your
disk.

Typical run: 10-30 minutes wall-clock, 2-3 Codex calls.

## Output structure

```
mad_sessions/<YYYYMMDD-HHMM-slug>/
├── ASSIGNMENT.md
├── claude_v1/
├── codex_v1/
├── round1_feedback/
│   ├── claude_reviews_codex.md
│   └── codex_reviews_claude.md
├── round2_revisions/
│   ├── claude_v2/
│   └── codex_v2/
├── final/                    — Claude's merged best-of-both
└── README_DEBRIEF.md         — what each agent contributed
```
