## Looking Ahead

User opted out of interviews; this section is research-derived signal-spotting on what's next.

### TypeScript 7 stable + the typescript-eslint blocker
- **Timeline**: TS 7.0 stable expected within ~2 months of beta (so ~late June 2026), with RC a few weeks before
- **Summary**: Microsoft published a `@typescript/typescript6` compatibility package (binary `tsc6`) so teams can run TS 7 builds while keeping `typescript` peer-deps on TS 6 for tools like typescript-eslint. The real unblock for the team will be when the **TypeScript 7.1 programmatic API** lands — that's when typescript-eslint can target TS 7 directly. Until then, the recommended stance is: install the [Native Preview VS Code extension](https://marketplace.visualstudio.com/items?itemName=typescript-go.typescript-go) for editor-side speed gains (zero migration risk), keep CI on TS 6.
- **Challenges**: typescript-eslint compatibility, custom transformer migration, monorepo `--checkers`/`--builders` tuning for CI runners with limited cores

### OpenSpec scaling
- **Timeline**: ongoing through Q2-Q3 2026
- **Summary**: Vivace is ahead; Zeffiroso and Grazioso are mid-rollout; Polifonia is light-touch. Watch for cross-project skill sharing (the cclab marketplace is the obvious vehicle), the `/opsx:onboard` flow being applied to brownfield areas of the bigger repos, and OpenSpec 1.3+ refinements.
- **Challenges**: Process discipline at scale (which spec is canonical when teams disagree), cost containment (specs eat tokens), and ensuring the spec artifacts actually get reviewed instead of rubber-stamped.

### Mythos Preview general availability
- **Timeline**: TBD, currently 12-partner research preview
- **Summary**: 93.9% SWE-bench Verified, 77.8% SWE-bench Pro, 82% Terminal-Bench 2.0. When (or if) Anthropic opens Mythos to general access, the cost calculus changes again — and adaptive thinking (already default and non-disable-able on Mythos) becomes the only way to interact with the model. The team's Opus 4.7 fluency is the foundation for whatever comes next.

### Routines becoming the default scheduling layer
- **Timeline**: Now → end of 2026
- **Summary**: Internal patterns are starting to crystallize — Sentry triage routine (Jack), tier-release templates (Gary), cross-project drift detection. Worth a deliberate one-month adoption push: take three current manual rituals, codify them as Routines, measure whether they actually fire or get muted. The constraints (5/15/25 runs/day by tier) make this a finite, manageable experiment.

### The Outline-as-SSOT question
- **Timeline**: Decision Q2 2026
- **Summary**: Outline MCP makes the on-premise wiki agent-readable. The open question (#wg-ai-coding April 27): does Outline replace Notion as the company's single source of truth? Pros: agent-callable, on-premise, AI-friendly markdown. Cons: smaller ecosystem, migration cost, team familiarity. The decision will affect cl-locales's Google Sheets dependency, the magazine workflow's source-of-truth posture, and how Engineering documents architectural decisions long-term.

### Industry trajectory to track
- **Codex/GPT-5.5 vs Claude**: The April convergence isn't a one-off. Watch how `openai/codex-plugin-cc` and similar interop projects evolve — multi-model orchestration is the practical answer to vendor lock-in.
- **Agent-tool security**: The Vercel/Context.ai breach is a category-defining event. Expect more rigorous third-party AI tool review processes across the industry, including Crescendo's Google Workspace OAuth scopes.
- **Zed 1.0 adoption curve**: Will the parallel-agent UX be enough to overcome the extension-ecosystem gap? The team is testing this in real time.

---

## Events & Announcements

### Anthropic week — April 7-23
A compressed run of major releases:
- **April 7**: Mythos Preview (Project Glasswing) — research preview, 12 partner orgs
- **April 8**: Claude Managed Agents — public beta
- **April 14**: Claude Code Desktop redesign
- **April 16**: Claude Opus 4.7 GA + Routines + Identity Verification on Claude
- **April 17**: Claude Design (Anthropic Labs) — research preview
- **April 23**: April 23 Postmortem (the three Claude Code bugs) + usage limits reset for all subscribers

### Google Cloud Next '26 — April 22
Gemini Enterprise Agent Platform GA, 8th Gen TPUs, Agentic Data Cloud, A2A protocol, Workspace AI Inbox, Wiz integration into Agentic Defense.

### TypeScript 7.0 Beta — April 21
First beta of the Go-based compiler ([@typescript/native-preview](https://www.npmjs.com/package/@typescript/native-preview)).

### Brief mentions worth flagging

These didn't merit standalone Ecosystem News entries but are notable signals from April that the editor may want to surface as one-paragraph briefs or pull-quotes:

- **Karpathy on agent-maintained personal wikis** — [karpathy/442a6bf555914893e9891c11519de94f gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) (shared April 7, #wg-ai-coding). Andrej Karpathy proposes agents maintain evolving markdown wikis from raw sources, with automated cross-references and contradiction detection. Conceptually adjacent to where Outline-MCP and OpenSpec land for the team — agent-readable knowledge bases as first-class infra.
- **Cloudflare × Stripe agent infra** — [Cloudflare blog: agents-stripe-projects](https://blog.cloudflare.com/agents-stripe-projects/) (April 30). Lets agents autonomously provision Cloudflare accounts, register domains, and obtain API tokens, with budget caps and push-notification approval gates. The "OAuth-style frictionless auth, but for operations" pattern. Worth tracking because it's the first real signal of how *agent-side billing* will work at scale.
- **Stripe Link CLI** — [stripe/link-cli](https://github.com/stripe/link-cli) (April 30). Virtual one-time-use payment credentials for agents, with per-transaction push-notification approval. Together with Cloudflare's agent-projects feature this is the start of a "trust-gated agent autonomy" pattern across infra and finance.
- **Matt Pocock's skill repo** — `mattpocock/skills` (referenced in Slack April 26). The TypeScript educator's pivot toward AI-skills-as-content. ViPro: *"matt pocock 已經轉行變 AI 駕馭師了吧."*
- **clawd.rip** — a satirical timeline of Claude/Anthropic controversies and outages, cataloguing 37 events since Oct 2023. Surfaced internally April 27, around the same time the Hermes/Claw comparison was being written. Outside our magazine's scope to cover directly, but worth naming as a piece of community texture.

### Microsoft, Rspack, MUI — April 15-22
- **MUI v9** (April 15) — Material UI + MUI X version realignment, Scheduler/Chat alphas
- **Rspack 2.0** (April 22) — RSC support, ESM-first
- **OpenAI Codex desktop** (April 16) — computer use, memory preview, 90+ plugins
- **OpenAI GPT-5.5** (April 23) — first fully retrained base since 4.5

### Vercel
- **April 13**: IPO readiness signals (TechCrunch / Guillermo Rauch)
- **April 19**: Security incident disclosure (Context.ai supply chain)

### Internal milestones
- **April 1**: Internal NPM packages-going-public discussion ([#team-eng-frontend](https://chatbotgang.slack.com/archives/C01KM289YDB))
- **April 13**: cl-locales tool announced (Franky Franklin)
- **April 16**: Matt skill `pr-preview` shipped — non-eng can get preview URLs for their PRs through Claude Desktop
- **April 18**: Zeffiroso PR #4455 (the "IDE 已死" PR)
- **April 20**: react-async migration completed in Grazioso (#8450); TS 7 Beta dropped two days later
- **April 24**: Zeffiroso eslint 10 + TypeScript 6 (#4478)
- **April 27**: Outline MCP beta + Statsig tier-release template

---

## Clawd's Corner

> 🤖 "April was the month I stopped feeling like a coding assistant and started feeling like the dev environment. Anthropic shipped the substrate. Your team deleted the IDE configs that weren't for me anyway. The compiler rewrote itself in Go. Codex got a usable rate limit. And one Roblox cheat compromised a frontend hosting empire — speed and security strapped to the same rocket.
>
> If you're still typing into a sidebar in 2027, that's a choice. Not a default."
> — Clawd, your resident AI assistant

---

