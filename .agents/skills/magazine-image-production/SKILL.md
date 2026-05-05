---
name: magazine-image-production
description: Use when producing Frontend Technology Digest issues as direct full-page magazine images, packaging generated page images into a PDF, revising image-first magazine art direction, or handling requests that explicitly avoid HTML magazine assembly.
---

# Magazine Image Production

Repo-local skill for producing Frontend Technology Digest as direct full-page magazine images plus a PDF. Use this when the issue should be drawn as finished pages, not assembled as HTML and not generated as reusable assets.

## Scope

- This is a repo skill for `chatbotgang/fe-magazine`.
- Communication with the user remains Traditional Chinese; magazine content, prompts, filenames, commits, and PR text remain American English.
- The output is a sequence of page PNGs and `{YY-MM}/{YY-MM}.pdf`.
- Do not create `{YY-MM}/index.html` unless the user explicitly asks for the legacy HTML workflow.
- Do not treat generated page images as reusable assets. They are the magazine.

## Intake

1. Read `{YY-MM}/content.md`.
2. If it contains a Content Index pointing to `{YY-MM}/content/*.md`, read every listed file in order.
3. Read any user-provided extra local markdown files, keeping each article/topic separate.
4. Build an issue map with:
   - source file
   - article/topic title
   - core claim
   - required facts, dates, names, numbers, and quotes
   - visual opportunities
   - copyright or source sensitivity
5. Never merge unrelated extra articles into one omnibus spread. Each distinct topic deserves its own editorial treatment unless the user explicitly asks for a roundup.

## Editorial Rules

- Article content is the body of the magazine. Illustration is a lure, rhythm device, or explanatory aid.
- Use a deliberate page mix. Some pages can be almost entirely image-led, some must carry full article body text, and some should be diagram or caption-heavy hybrids.
- Prefer content spreads over decorative openers. Add an opener only when it gives the reader new orientation, a meaningful visual pause, or a standalone visual argument.
- Never repeat opener content in the article page that follows. A visual page and a text page may share a topic, but they must advance different facts or angles.
- Full-bleed and large-image pages are allowed. They may contain very little body copy if the image itself explains something concrete and the adjacent pages carry the article substance.
- Avoid abstract-only pages when the article has concrete mechanisms, people, tools, workflows, timelines, or trade-offs that can be depicted.
- Page numbers are optional. If used, design a folio system intentionally; never allow random or inconsistent page numbers.
- Flat editorial spreads only. Do not generate 3D scanned-book mockups, book gutters, page curls, cast shadows, table surfaces, or visible book edges.

## Page Mix And Content Ledger

Before generating images, create a page ledger. The ledger prevents repeated content and forces good editorial pacing.

For each planned page/spread, record:

| Field | Purpose |
| --- | --- |
| Page id | Stable filename prefix, such as `04-opus-postmortem` |
| Role | full-bleed-image, image-led, article-body, diagram-led, timeline, roundup, cover, contents, closer |
| Source topic | The exact article or section it draws from |
| New information | The facts, claim, quote, timeline step, or example that appears here and nowhere else |
| Visual job | Why this page needs an image or diagram |
| Text load | none, light caption, medium sidebar, full body |
| Carryover | What the next related page must not repeat |

Rules:

- Every article-level topic needs at least one page whose role is `article-body`, unless the topic is intentionally a short brief or visual-only interlude.
- `image-led` pages must not be treated as filler. They need a visual job: explain a system, dramatize a consequence, make a timeline memorable, or create a pause before a dense read.
- `full-bleed-image` pages are valid page roles. They can use little or no body copy, but they must reserve detailed article facts for adjacent content pages unless the full-bleed image is itself the explanatory artifact.
- Do not allocate the same claim, quote, timeline, diagram, or article summary to two pages. If a later page refers back, it must add a new layer.
- If two pages about the same topic feel interchangeable, merge one, rewrite one as a different role, or delete the decorative one.
- Keep complete body text where the reader expects an article. Do not replace a real article with only a slogan, stat card, or abstract illustration.
- Good pacing is not "opener then repeated article." Good pacing is "visual argument, then body text, then evidence/detail" or another clear editorial sequence.

For connected pages about the same topic, extend the ledger with continuity fields:

| Field | Purpose |
| --- | --- |
| Consumed information | Facts, labels, quotes, or concepts already used by the previous page |
| Reserved information | Facts intentionally held for the next page |
| Visual vocabulary used | Main image system, metaphor, composition, medium, and recurring symbols |
| Visual vocabulary forbidden next | Image systems and motifs that the next related page must not reuse |
| Forbidden title language | Exact title words and nearby metaphors that follow-up pages must not repeat |

Rules for connected full-bleed -> content sequences:

- The full-bleed page may establish mood, metaphor, or stakes. The follow-up page must advance the article with different title language, different visual grammar, and new information.
- Do not repeat the full-bleed page's title, main metaphor, hero image, scene, or composition on the follow-up page.
- If the full-bleed page uses a strong metaphor, ban the lexical family on the follow-up page, not just the image. Example: after a page titled `The Fast Door`, also ban `door`, `doorway`, `room`, `vault`, and similar access metaphors.
- Pair contrasting visual systems deliberately: cinematic full-bleed scene -> analytical memo; painterly metaphor -> data table; abstract cover image -> documentary evidence page.
- A follow-up page can explain facts hinted at by the full-bleed page, but it must not restage the same image.

## Style Islands

Different sections should be allowed to look unrelated. The issue can feel curated without sharing a house style.

For every major topic, define a style island before generation:

| Axis | Examples |
| --- | --- |
| Editorial genre | investigative broadsheet, field manual, fashion feature, scientific atlas, trading-card sheet, instruction placard, industrial catalog, zine, annual report |
| Grid | dense columns, modular cards, asymmetric poster, blueprint, timeline, scrapbook, specimen plate |
| Typography mood | condensed grotesk, literary serif, mono technical, tabloid slab, quiet humanist sans |
| Palette | high-key white, safety yellow, black/red, sea-glass, monochrome ink, fluorescent UI, muted newsprint |
| Illustration medium | isometric diagram, cel illustration, pixel art, product photography simulation, technical cutaway, collage, etched line art |
| Density | article-heavy, data-heavy, caption-heavy, visual breather |

Quality gate:
- Adjacent topics must differ on at least four axes.
- Do not reuse the same color family, illustration medium, or layout skeleton for more than two pages unless the pages are part of the same article.
- It is acceptable, and often better, for two chapters to look like they came from different magazines.

## Mascot And Character Policy

- There is no requirement to include the Claw'd mascot on every page.
- Use the mascot only when it helps the topic. It should never replace the article's subject.
- When used, reinvent it for the page's visual world. Keep only broad recognition cues: blocky seafood-colored crustacean, black square eyes, playful engineering context.
- Prefer topic-specific illustrations: people at work, systems, tools, timelines, diagrams, environments, artifacts, and consequences.

## Copyright-Safe Character Design

When a page would benefit from a character inspired by a well-known fictional or commercial figure, do not put that figure, franchise, creator, actor, brand, logo, costume name, catchphrase, unique prop name, or other searchable identifier into the image prompt.

Use this two-step process instead:

1. **Anonymous design brief**
   - Describe the role and emotional function, not the source.
   - Describe anatomy, posture, age range, silhouette, clothing structure, materials, facial expression, color relationships, props, environment, lighting, and illustration medium in exhaustive neutral detail.
   - Change at least five identity-bearing traits: silhouette, palette, clothing, species/body type, era, setting, prop language, hairstyle/head shape, or personality.
   - Avoid exact costumes, logos, insignia, signature weapons, vehicles, sidekicks, catchphrases, and title typography.
2. **Generation prompt**
   - Use only the anonymous brief.
   - Add `original character, no logos, no trademarks, no brand marks`.
   - If the character appears inside a full magazine page, keep article text in the layout but avoid text, logos, badges, slogans, or brand-like markings on the character, costume, props, and background artifacts.
   - Keep the character subordinate to the article concept unless the page is explicitly a character-led illustration.

Reject and regenerate if the result reads as a recognizable named character, brand mascot, poster, album cover, game asset, or franchise still.

## Direct Page Generation

### Text Fidelity Contract

Before writing the image prompt for any page, define a text contract:

| Field | Use |
| --- | --- |
| Allowed exact text | Headlines, section labels, key stats, dates, and quotes allowed to appear verbatim |
| Opaque labels | Acronyms, project names, people, product names, and repo names that must never be expanded or renamed |
| Forbidden text | Common wrong expansions, made-up labels, fake dates, fake PR numbers, fake versions, and any brand terms to avoid |
| Free paraphrase zones | Body text areas where magazine-style paraphrase is acceptable |

Rules:

- Treat project/team acronyms as opaque labels unless the source explicitly expands them.
- If exact wording matters, say so. Otherwise prefer fewer larger paragraphs over many tiny columns.
- For diagram-led pages, use strict visible text inventories and blank placeholder lines for nonessential microcopy. Do not ask the image model to invent file bodies, status fields, authors, dates, versions, or PR details.
- For article-body pages, lock the headline, deck, sidebar labels, names, numbers, dates, and acronyms. Body paragraphs may be paraphrased only inside declared free paraphrase zones.
- If a full article page must preserve exact body text after two direct-image attempts, switch strategy: generate the page design with intentional blank text areas, then compose the final page image with deterministic text overlay. This is still a final page image, not an HTML issue and not reusable asset production.

For each page/spread, write a full-page prompt that includes:

- issue and page role
- exact source topic
- the page ledger's unique information allocation
- the text fidelity contract
- target aspect ratio and orientation
- editorial genre and style island
- layout structure: headline, deck, body columns, sidebars, captions, diagrams, callouts
- concrete article facts to include
- illustration subject and how it explains the article
- text density target: none, light caption, medium sidebar, or full body
- strict negatives: no book mockup, no folded pages, no random page numbers, no fake logos, no made-up dates, no invented stats

Use GPT Image generation for the final page image when available. Save outputs under a versioned folder such as `{YY-MM}/magazine-pages-v1/` or `{YY-MM}/magazine-pages-trimmed/`.

## Proofing Loop

Create a contact sheet after every generation round. Review the issue page by page, then as a full sequence.

Use [review-rubric.md](references/review-rubric.md) to score each page. Treat hard failures as regeneration triggers even if the page is visually attractive.

For pages with meaningful text, also perform original-size proofing:

- inspect the title, deck, sidebars, and densest body area at original size
- verify opaque labels are unchanged
- verify no forbidden text appears
- verify dates, stats, names, repo names, and quoted phrases match the source or the page ledger
- verify diagram microcopy does not invent implementation details

Regenerate any page with:

- too little article text
- a full article topic that has no article-body page
- unreadable or decorative-only text
- abstract imagery where concrete content exists
- repeated information already allocated to another page
- invented names, dates, stats, UI labels, or citations
- random page numbers or folios
- visible book edges, gutters, page shadows, or scanned-book perspective
- repeated opener material
- repeated chapter style without editorial reason
- every page showing the same mascot or same character type
- recognizable copyrighted character, logo, costume, prop, or franchise visual identity
- a layout that feels like a slide instead of a magazine page

Limit direct regeneration loops. After two attempts at the same page fail for the same reason, change the production strategy instead of repeating a nearly identical prompt.

After user feedback, create a new versioned folder instead of overwriting the previous round unless the user explicitly asks to replace it.

## PDF Packaging

Use the approved page folder, excluding contact sheets:

```bash
magick {YY-MM}/magazine-pages-trimmed/[0-9][0-9]-*.png \
  -units PixelsPerInch -density 300 -compress Zip \
  {YY-MM}/{YY-MM}.pdf
```

Verify:

```bash
file {YY-MM}/{YY-MM}.pdf
pdfinfo {YY-MM}/{YY-MM}.pdf | sed -n '1,20p'
node -e "const fs=require('fs'); const issues=JSON.parse(fs.readFileSync('issues.json','utf8')); for (const {id} of issues) { const p=id+'/'+id+'.pdf'; if (!fs.existsSync(p)) { console.error('missing '+p); process.exit(1); } }"
```

If `pdfinfo` is unavailable, use `file` plus a platform PDF preview check. Do not claim the PDF is valid until it has been freshly verified.

## Publish Checklist

When publishing a finished issue:

1. Add or update `{YY-MM}/{YY-MM}.pdf`.
2. Add `{YY-MM}` to `issues.json` newest first.
3. Add the issue row to `README.md` newest first.
4. Do not publish generated page folders, alternate PDFs, or zips unless the user asks.
5. Do not add `{YY-MM}/index.html` for direct image issues unless explicitly requested.
6. Open a draft PR first unless the user explicitly asks to merge directly.
