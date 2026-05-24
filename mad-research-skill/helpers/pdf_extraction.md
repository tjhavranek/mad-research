# PDF handling for the Codex streams

## The problem

Claude (this session) reads PDFs natively, preserving page numbers and
section structure. Codex CLI does **not** reliably read PDFs as of the
versions this skill targets. So when two of the three streams are Codex,
they need a text representation of the manuscript.

Naive text extraction (via `pdftotext`, `pdfminer`, etc.) damages page
numbers, section boundaries, and tables. That breaks the quote+locator
discipline that is non-negotiable for the audit.

## The compromise this skill uses

When the user provides a PDF as input to MAD-research:

1. Claude reads the PDF and writes a structured text version to
   `<session>/input/manuscript_text.md`. The structure is:

   ```
   <!-- PAGE_INDEX 1; PRINTED_LABEL i -->
   [front matter]

   <!-- PAGE_INDEX 2; PRINTED_LABEL ii -->
   [...]

   <!-- PAGE_INDEX 5; PRINTED_LABEL 1 -->
   # Introduction

   [paragraph]

   <!-- PAGE_INDEX 6; PRINTED_LABEL 2 -->
   ...
   ```

   **Pages are preserved in PDF order.** Never reorder. Never lift the
   table of contents, references, or appendices out of position — that
   shifts page indices and silently breaks every locator downstream.

2. **Dual locators.** Many academic PDFs have a `PAGE_INDEX` (the
   absolute position in the file) that differs from the `PRINTED_LABEL`
   (the human-visible page number, often "1" after several pages of
   front matter, sometimes Roman for the front matter). The skill
   records BOTH. Codex streams are told: "Cite either the printed page
   label (`p. 7`) or the PDF page index (`PAGE_INDEX 11`) — both are
   acceptable. The skill will reconcile during synthesis."

3. `input/manuscript_meta.json` records, per page:
   ```json
   {
     "page_index": 11,
     "printed_label": "7",
     "first_15_chars": "We propose a met",
     "last_15_chars": "...this paper.",
     "char_count": 3104,
     "has_table": true,
     "has_figure": false,
     "extraction_status": "ok | partial | failed"
   }
   ```
   This allows synthesis to (a) verify quotes appear on the cited page,
   and (b) detect locator drift.

4. **Extraction caveats.** If a page is image-only, has heavy tables that
   garbled in extraction, or has non-Latin text that the extractor
   mangled, record `extraction_status: partial` or `failed`. List the
   affected pages prominently at the top of `manuscript_text.md` under
   `## Extraction caveats`.

5. **Hard abort conditions.** If any of these are true, abort with a
   clear message — do not proceed with a degraded audit:
   - More than 20% of pages have `extraction_status: failed`.
   - Any cited page's `first_15_chars` cannot be located in the
     extracted text.
   - Page indices in the extracted text appear non-monotonic.

## Quote verification (used by synthesis)

For each surviving criticism in `final_memo.md` that cites `p. N`:

1. Convert `p. N` (printed label) to `PAGE_INDEX X` via the meta map.
2. Read the extracted text between `<!-- PAGE_INDEX X -->` and
   `<!-- PAGE_INDEX X+1 -->`.
3. Search for the quoted substring (allowing modest whitespace tolerance).
4. If found, the locator passes.
5. If not found, move the criticism to "Points rejected" with reason
   "quote not verified on cited page."

If the user prefers, also re-open the original PDF and verify against
it directly — this catches extraction errors that the dual-locator scheme
might miss.

## What Claude must do per run

Before Round 1 begins:

1. Read the PDF page by page in original order.
2. Write `manuscript_text.md` with `<!-- PAGE_INDEX N; PRINTED_LABEL X -->`
   separators.
3. Write `manuscript_meta.json` with the per-page record above.
4. Verify the extraction caveats don't trigger any hard-abort condition.
5. Note the total page count, characters, and approximate tokens in the
   session's `meta.json`.

## Anti-patterns (do not do)

- Do not paraphrase or summarize the manuscript before Round 1. Codex
  streams need the verbatim text.
- Do not skip the page-separator comments. They are the locators.
- Do not reorder content (TOC, references, appendix — leave in place).
- Do not OCR a scanned PDF in v1. If a PDF is image-only, abort and tell
  the user to provide a text-PDF version.
- Do not strip footnotes silently — flag them in extraction caveats if
  the extractor mangled them, but keep the text.

## Non-PDF inputs

- `.md`, `.txt`, `.tex` -> pass through as-is. For long files, insert
  artificial separators every ~400 lines so locators are still meaningful:
  `<!-- BLOCK 1 -->`, `<!-- BLOCK 2 -->`, etc.
- `.docx` -> if `pandoc` is available, convert to markdown then proceed
  as above. If not, ask the user to export to PDF.
- `.ipynb` -> extract markdown + code cells in order, treat each cell as
  a block.

If the input is something else (HTML, .Rnw, .RData, etc.), ask the user
to convert before retrying.

## Chunking long manuscripts

If the manuscript exceeds the threshold in `helpers/orchestration.md`
Step 3:

1. Tell the user the manuscript is long enough to slow the run.
2. Offer to chunk: run Round 1 on pages 1-N first, decide whether to
   continue.
3. If chunked, the meta.json records `chunk: {pages: "1-25", of: 87}`
   and the final memo is clearly labeled "partial audit, pages 1-25."

Chunking is offered, not imposed.
