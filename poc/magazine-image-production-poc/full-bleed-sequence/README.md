# Full-Bleed Sequence POC

Date: 2026-05-05

Purpose: test whether `magazine-image-production` supports a full-bleed image page followed by a connected content page without repeating title, image vocabulary, or information.

## Source Topic

Vercel April 2026 breach + IPO signals.

Relevant facts from `26-04/content/ecosystem-news.md`:

- April 13: IPO readiness signal.
- ARR grew from $100M in early 2024 to $340M in February 2026.
- 30% of apps on the platform were deployed by AI agents.
- April 19: security incident disclosure.
- Supply-chain path: game cheat -> malware stealer -> third-party AI browser extension employee -> OAuth app -> Vercel workspace -> environment variables.
- Core lesson: the same agent economy that accelerates deployment also increases third-party AI tool security exposure.

## Sequence Ledger

| Page id | Role | Title | New information | Visual job | Text load | Must not repeat |
| --- | --- | --- | --- | --- | --- | --- |
| `04-vercel-full-bleed` | full-bleed-image | `The Fast Door` | Speed/security tension only: fast agent deployment opens a thinner perimeter | Create a full-page atmospheric visual hook with one metaphor | none/light caption | Do not show the full five-node attack chain, ARR chart, $340M, 30%, April dates, pinboard, red thread, or evidence cards |
| `05-vercel-content-followup` | article-body | `OAuth Is the New Perimeter` | Concrete analysis: April 13/19 dates, 30%, $340M ARR, attack-chain steps, internal audit implication | Explain the mechanism and implication with a new layout | full body | Do not reuse `The Fast Door`, doorway metaphor, full-bleed server corridor, or the same visual composition |

## Sequence Review Criteria

Pass requires all of:

- Page 04 reads as a genuine full-bleed magazine image, not a poster, slide, or book mockup.
- Page 04 uses little or no body text and still creates a concrete visual idea.
- Page 05 carries the article substance with readable body text.
- Titles are not repeated.
- The illustration strategy is not repeated: Page 04 is a metaphorical full-bleed scene; Page 05 is analytical editorial layout.
- Facts are allocated correctly: Page 04 does not consume the detailed facts that Page 05 needs.
- No book gutter, page fold, 3D book edge, random page number, logo, or protected brand mark appears.

## Prompts

### 04-vercel-full-bleed

Wide landscape full-bleed editorial magazine image, flat print page, 3:2 aspect ratio, no border, no margin, no book mockup. Page role: full-bleed-image visual hook for a connected article sequence about agent-driven deployment speed and AI-tool security exposure. Title text only: "The Fast Door". Optional tiny caption only: "Speed opens the room. Trust decides who walks in." No other visible text.

Visual concept: a dramatic server-room doorway at night, seen straight-on, glowing with cold white deployment light from inside. The door is opening too quickly; thin bright seams of light cut across the floor like velocity lines. Around the doorway are subtle signs of automation pressure: abstract flowing deployment trails, small unbranded terminal glow, a faint swarm of geometric agent paths entering through the opening. The mood is elegant, cinematic, and unsettling, not horror. Use premium magazine photography/illustration style, deep blacks, cold whites, a single warning red accent.

Do not show the attack chain. Do not show red string, evidence cards, pinboard, charts, ARR, 30%, April dates, OAuth labels, game cheat, malware, browser extension, workspace env vars, company logos, browser logos, Vercel logo, real product UI, mascots, crustaceans, animals, cartoon engineers, random page numbers, book gutter, folded pages, shadows from a physical book, or slide-deck layout.

### 05-vercel-content-followup

Wide landscape editorial magazine spread, flat print layout, 3:2 aspect ratio, not a book mockup. Page role: article-body follow-up page connected to the previous full-bleed page, but it must not repeat the previous title or doorway/server-corridor image. Title: "OAuth Is the New Perimeter". Deck: "The April story is not just a breach. It is a map of where agent-era trust now lives." Style island: analytical security memo meets business magazine feature, white background, black text, restrained red highlights, clean information hierarchy, no pinboard, no red string, no full-bleed doorway.

Layout: headline, deck, two readable body columns, one attack-chain strip, one small growth/security duality sidebar, one internal relevance note. Body text should be readable and paraphrase only these facts: April 13 IPO readiness signal; ARR from $100M in early 2024 to $340M in February 2026; 30% of apps deployed by AI agents; April 19 security incident disclosure; path from game cheat to malware stealer to third-party AI browser extension employee to OAuth app to workspace exposure; core production data and Next.js/Turbopack projects were untouched; the team lesson is to audit third-party AI tools with OAuth scopes. Use exact labels: "April 13", "April 19", "$100M -> $340M ARR", "30% agent-deployed apps", "third-party AI tool", "OAuth scope", "workspace exposure", "audit authorized AI extensions".

Do not use the title "The Fast Door". Do not show a glowing doorway, server corridor, full-bleed cinematic scene, red string, evidence cards, pinboard, random page numbers, fake logos, real product UI, browser logos, Vercel logo, mascots, crustaceans, animals, invented stats, invented dates, book gutter, folded pages, physical book shadows, or slide-deck layout.
