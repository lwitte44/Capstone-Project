# Spike SP-03 — Do the footnotes in my own essays render as working linked references?

- **Unknown:** Whether the footnote syntax in my markdown files matches the dialect the renderer parses.
- **Feeds:** ADR 0004 — page rendering strategy, specifically the markdown criterion.
- **Requirements at risk:** FR-READ-03, FR-READ-02, FR-READ-04
- **Time box:** 90 minutes — and you stop when it rings.
- **Run on:** 2026-09-29

## The question

Does one real essay of mine render every footnote as a numbered in-text reference linked to a matching definition at the foot of the page, with the same number of footnotes as the original document?

## The smallest thing that answers it

No deployment needed. Markdown rendering is deterministic, so localhost gives the same answer production would — this is the one spike where local testing is sufficient.

1. Pick the essay of mine with the **most footnotes**. Worst case first.
2. **Count the footnotes in the original and write the number down now**, before you build anything. Without this number you have nothing to compare against and you will end up squinting at the page trying to remember.
3. Convert that essay to markdown and save it as `essay.md` in the spike folder.
4. Install the markdown pipeline and register the footnote plugin.
5. Create a page that reads `essay.md` and renders it. Nothing else on the page — no layout, no styling, no navigation.
6. Run locally and open it.
7. Count the rendered footnote references. Compare to step 2.
8. Click **every** reference. Each must jump to a matching definition at the bottom.
9. While you are there, check that italics and blockquotes survived too — FR-READ-02 needs both, and this is a free look at them.

## Success criterion

All three, together:

- The rendered footnote count equals the count you wrote down in step 2.
- Every reference links to its own definition, and every definition has a way back.
- **No `[^` appears anywhere as literal text on the page.**

That last one is the real test. A dialect mismatch does not raise an error — it silently prints the marker characters into the middle of your prose.

## Failure criterion

Any one of these means no:

- Any literal `[^1]` or `[^` visible in the body text.
- Rendered footnote count does not match the original.
- References render but do not link to anything.
- Footnotes appear as a flat list at the bottom with no connection to the text above.

## Plan B if it fails

Try **one** alternative footnote plugin inside the time box. One, not four — if the first alternative also fails, the problem is the dialect, not the plugin, and more plugins will not fix it.

If neither works, FR-READ-03 drops from Should to Could and footnotes render as plain paragraphs at the end of the essay: readable, but not linked and not numbered in place. On philosophy essays where citations run throughout, that is a visible quality loss, so it belongs stated plainly in ADR 0004's consequences rather than quietly absorbed.

## Result

_To be completed after the time box. Record both counts — original and rendered — and whether anything other than footnotes broke in the conversion._

## Decision

_Proceed, accept the reduced version of FR-READ-03, or re-spike with a different plugin. Then update ADR 0004, the requirement's priority if it changed, and `docs/hours-log.csv`._

**What to keep from this spike:** commit `essay.md` as a test fixture. Any later change to the markdown pipeline can be re-checked against it in two minutes, and it becomes the sample content the NFR-MAINT-01 clean-machine test renders.
