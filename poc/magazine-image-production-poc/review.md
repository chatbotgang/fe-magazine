# POC Review: Magazine Image Production

Date: 2026-05-05

## Summary

The skill direction works for style separation and page-role planning. The three-page POC produced clearly different editorial worlds:

- Vercel: investigative broadsheet / image-led security-growth argument
- Routines: office memo / article-body field report
- OpenSpec: technical atlas / diagram-led process page

The main weakness is text fidelity on full article-body pages. GPT Image can create magazine-like readable body text, but it may rewrite protected labels or invent acronym expansions unless the prompt includes explicit lock rules and the result is zoom-proofed.

## Page Results

| Page | Best version | Verdict | Notes |
| --- | --- | --- | --- |
| Vercel image-led | v2 | Pass with minor risk | v1 inserted the mascot even though it was not requested. v2 fixed this by explicitly saying no mascots/crustaceans/characters and limiting visible text. |
| Routines article-body | v3 | Conditional pass | v1/v2 expanded `MAAC` into an invented phrase. v3 fixed this with an opaque-label lock rule. Body text became more readable after reducing columns and using larger paragraphs. |
| OpenSpec diagram-led | v2 | Pass | v1 fabricated dates, file details, authors, and fake implementation artifacts. v2 improved by using a strict visible text inventory and blank placeholder lines. |

## Rubric Scores

| Page | Role fit | Unique info | Text load | Visual value | Style separation | Magazine feel | Copyright safety | Fact discipline | Total |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Vercel v2 | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 2 | 23/24 |
| Routines v3 | 3 | 3 | 2 | 2 | 3 | 3 | 3 | 2 | 21/24 |
| OpenSpec v2 | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 2 | 23/24 |

## Failures Observed

1. **Unrequested mascot insertion**
   - v1 Vercel added the crustacean mascot even though the page was not mascot-led.
   - Fix: prompts for non-mascot pages need explicit negative terms.

2. **Acronym expansion / label invention**
   - v1/v2 Routines expanded `MAAC` into an invented phrase.
   - Fix: mark project/team/product acronyms as opaque labels, list forbidden expansions, and proof the rendered image at full size.

3. **Diagram microcopy hallucination**
   - v1 OpenSpec invented dates, artifact names, status fields, authors, and process details.
   - Fix: diagram-led pages should use a strict visible text inventory and blank placeholder lines for nonessential microcopy.

4. **Full-body readability ceiling**
   - v3 Routines is much better than v1/v2, but still not as controlled as deterministic typesetting.
   - Fix: direct GPT Image pages are acceptable for magazine-like body text when exact wording is not critical. If exact text must be preserved, use a deterministic text overlay on top of a generated page design after two failed direct-image attempts.

## Skill Changes To Make

- Add a visible text contract per page:
  - `allowed exact text`
  - `opaque labels`
  - `forbidden text`
  - `free paraphrase zones`
- Add a page-type text strategy:
  - image-led: minimal locked labels only
  - diagram-led: strict inventory plus placeholder lines
  - article-body: locked headline/deck/sidebar labels plus paraphrased body, zoom proof required
  - exact-body: deterministic text overlay fallback
- Add zoom proof to the proofing loop:
  - inspect the page at original size
  - crop/check title, sidebar, and densest body area
  - reject if acronyms, dates, stats, names, or labels drift
- Add "two direct attempts max" before changing strategy for a failing page.
