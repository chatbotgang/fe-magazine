# Full-Bleed Sequence POC Review

Date: 2026-05-05

## Result

The full-bleed image page works. `04-vercel-full-bleed.png` reads as a genuine full-page magazine visual hook: one big image, one title, no body copy, no book mockup, and no detailed facts consumed from the follow-up article.

The connected content page needed three attempts:

| Page | Verdict | Notes |
| --- | --- | --- |
| `05-vercel-content-followup.png` | Partial pass | Avoided the full-bleed title and image, but repeated the attack-chain graphic twice on the same page. |
| `05-vercel-content-followup-v2.png` | Better, still flawed | Switched to an OAuth audit matrix, but invented fake tool columns and reused the door/doorway metaphor in body copy. |
| `05-vercel-content-followup-v3.png` | Pass | Uses a distinct title, distinct white analytical layout, no doorway/server-corridor image, no repeated title, and no repeated visual metaphor. |

## Scores

| Page | Role fit | Unique info | Text load | Visual value | Style separation | Magazine feel | Copyright safety | Fact discipline | Total |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Full-bleed page | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 3 | 24/24 |
| Content follow-up v3 | 3 | 3 | 2 | 3 | 3 | 3 | 3 | 2 | 22/24 |

## What The Skill Should Learn

1. Add `full-bleed-image` as an explicit page role, not just an implicit form of `image-led`.
2. Add adjacent-page continuity fields to the page ledger:
   - consumed information
   - reserved information
   - visual vocabulary used
   - visual vocabulary forbidden on the next related page
   - title phrase forbidden on follow-up pages
3. For connected pages, forbid not only repeated exact titles and images, but also repeated metaphors and repeated visual systems.
4. Content follow-up pages should use a different visual grammar from the full-bleed page. Example: full-bleed cinematic metaphor -> white analytical memo / matrix.
5. If a follow-up page keeps leaking the previous page's metaphor, explicitly ban the lexical family, not only the image. Example: after `The Fast Door`, ban `door`, `doorway`, `room`, `vault`, and related access metaphors.
