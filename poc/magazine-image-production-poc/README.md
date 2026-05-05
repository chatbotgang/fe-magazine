# Magazine Image Production POC

Date: 2026-05-05

Purpose: test whether the repo-local `magazine-image-production` skill produces the intended editorial behavior before relying on it for another issue.

## Review Rubric

Score each page from 0-3 for each criterion.

| Criterion | 3 | 2 | 1 | 0 |
| --- | --- | --- | --- | --- |
| Page role fit | Role is obvious and useful | Role is mostly clear | Role is muddled | Role is decorative only |
| Unique information | Adds facts not repeated elsewhere | Minor overlap | Major repeated summary | Same content as another page |
| Text load fit | Text amount matches role | Slightly too much or little | Text fights the role | Text is absent where needed, or unreadable |
| Concrete visual value | Image explains content | Image supports mood and some detail | Image is generic | Abstract filler |
| Style separation | Clearly unrelated to adjacent topics | Distinct but shares some moves | Too similar | Same template/style |
| Magazine feel | Reads as editorial print | Mostly magazine-like | Slide/poster-like | Looks like a presentation slide |
| Copyright safety | Original, no brand/character risk | Minor generic resemblance only | Noticeable resemblance risk | Recognizable protected character/brand |
| Fact discipline | No invented facts visible | Only harmless atmospheric labels | Some suspicious labels | Fabricated stats/dates/names dominate |

Hard failures requiring regeneration:

- recognizable copyrighted character, logo, or brand mark
- visible book gutter, folded page, cast shadow, or 3D book mockup
- random page numbers or unmanaged folios
- article-body page with unreadable body text
- image-led page that says nothing concrete
- duplicated core claim from another page without a new angle

## POC Page Ledger

| Page id | Role | Source topic | New information | Visual job | Text load | Carryover |
| --- | --- | --- | --- | --- | --- | --- |
| `01-vercel-image-led` | image-led | Vercel breach + IPO duality | April 13 IPO readiness, April 19 breach, 30% agent-deployed apps, attack chain as one visual argument | Show speed and security tied to the same agent economy | light caption | Follow-up pages must not restate the entire attack chain; they must analyze implications |
| `02-routines-article-body` | article-body | Jack Lee's MAAC playbook | Daily Sentry routine, bundle-size CI, Test Plan section, react-async closure, human challenge loop | Make the article readable; illustration only frames the coworker-with-a-schedule metaphor | full body | No separate opener repeating "AI coworker with a schedule" |
| `03-openspec-diagram-led` | diagram-led hybrid | OpenSpec in four repos | Propose-review-apply-archive ritual, four-repo adoption, "correctness is still expensive" bottleneck | Make the process visible as a field-guide/specimen page | medium sidebar | Do not repeat F1/F2 philosophy; focus on institution/process |

## Copyright-Safe Character Brief For Page 02

An original small office automaton character used as a scheduling metaphor. It has a compact rectangular torso made from layered off-white paper calendar cards, short hinged arms with rounded metal joints, small boot-like feet, a simple dark glass faceplate with two tiny square light dots for eyes, a paperclip-shaped antenna, and a waist ring holding tiny colored tabs. It wears no logo, no uniform, no badge, and no recognizable costume. It stands beside a wall of pinned routine cards and lightly points to a 09:00 slot with a plain wooden pointer. The expression is neutral, diligent, and slightly overworked. The design language is mid-century office equipment crossed with a handmade zine puppet, rendered in ink, risograph grain, and muted office-paper colors.

## Generation Prompts

### 01-vercel-image-led

Wide landscape editorial magazine spread, flat print layout, 3:2 aspect ratio, not a book mockup. Page role: image-led visual argument for an article about Vercel's April 2026 breach and IPO signals. Style island: investigative business-security broadsheet, black ink on warm newsprint, harsh red annotation marks, forensic pinboard composition, thin rule lines, financial chart fragments, cinematic but still editorial. Layout: oversized headline area, one deck line, one small caption block, one attack-chain diagram integrated into the illustration. Concrete facts to include as large readable editorial fragments: "April 13: IPO readiness", "April 19: security incident", "30% agent-deployed apps", "$340M ARR", "OAuth supply chain". Illustration subject: a five-node attack chain drawn as physical evidence cards connected with red thread: game cheat, malware stealer, third-party AI browser extension employee, OAuth app, workspace environment variables. No real company logos, no browser logos, no brand marks, no fake screenshots, no fake citations, no random page numbers. No book gutter, no folded pages, no shadowed book edges. The page should feel like a magazine opening image that explains the duality: agent-driven growth and AI-tool security risk are tied together.

### 02-routines-article-body

Wide landscape editorial magazine spread, flat print layout, 3:2 aspect ratio, not a book mockup. Page role: article-body page for "AI Becomes the Coworker With a Schedule — Jack Lee's MAAC Playbook". Style island: quiet operational field report meets mid-century office memo, pale green ledger paper, black typewriter text, red pencil edits, small risograph illustration, dense but readable columns. Layout: headline, deck, three body-text columns, one sidebar titled "The Pattern", one small original office automaton illustration from this neutral brief: compact rectangular torso made from layered off-white paper calendar cards, short hinged arms with rounded metal joints, small boot-like feet, dark glass faceplate with two tiny square light dots for eyes, paperclip-shaped antenna, waist ring with tiny colored tabs, no logo, no uniform, no badge, no recognizable costume, pointing to a 09:00 routine slot with a plain wooden pointer. Body copy should be large and readable, not tiny filler. Include these article points without adding new facts: public-package friction became an npm-token tooling fix; pnpm audit found 58 vulnerabilities and led to a smaller sanitizer threat model; bundle-size CI comments on every PR; the Test Plan section asks AI to list verification steps; react-async was removed and the migration skill was deleted afterward; a daily Sentry routine posts 7-day error analysis to Slack. Text density target: full body. No brand logos, no copyrighted characters, no fake dates, no fake stats, no random page numbers, no book gutter, no folded pages, no 3D book perspective.

### 03-openspec-diagram-led

Wide landscape editorial magazine spread, flat print layout, 3:2 aspect ratio, not a book mockup. Page role: diagram-led hybrid article page for "OpenSpec Quietly Lands in Four Repos". Style island: scientific field guide plus technical standards atlas, bright white background, cobalt blueprint lines, black serif captions, small orange inspection stamps, specimen labels, process arrows, calm institutional tone. Layout: headline, short deck, central process diagram, medium sidebar, four small repo adoption strips. Concrete facts to include: OpenSpec premise "Generating code is cheap. Correctness is expensive."; ritual flow "propose -> human review -> apply -> archive"; four repos: Vivace, Zeffiroso, Grazioso, Polifonia; bottleneck shifts from writing code to specifying intent correctly; process tax is useful mainly for multi-repo, multi-author, AI-primary work. Illustration subject: a clean technical specimen plate showing markdown spec artifacts as labeled sheets moving through a review gate into implementation. No real logos, no fake GitHub screenshots, no invented star counts, no random page numbers. No book gutter, no folded pages, no cast shadow, no slide-deck look.
