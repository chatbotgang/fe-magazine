## Ecosystem News

### 1. Anthropic Claude Managed Agents — Production Agent Infrastructure ⭐ Deep-Dive

- **Category**: Industry / Platform
- **Source**: [Scaling Managed Agents: Decoupling the brain from the hands](https://www.anthropic.com/engineering/managed-agents) (Anthropic Engineering, April 8, 2026)
- **Summary**: Anthropic launched Managed Agents in public beta on April 8, exposing the same harness that powers Claude Code as a composable API surface. The architecture decouples three layers — **Brain/Harness** (stateless orchestration, restartable), **Hands/Sandbox+Tools** ("cattle, not pets" containers), and **Session** (durable, append-only event log) — connected by stable interfaces so the underlying implementation can swap as models evolve. Claim: "Prototype to production 10× faster" by eliminating months of work on containerization, state management, credential vaulting, and observability.
- **Key Points**:
  - Time-to-first-token dropped ~60% at p50, >90% at p95 because inference starts before container provisioning
  - Pricing: standard token rates **+ $0.08/active session-hour** (millisecond-billed, accrues only while running)
  - MCP integration uses a vault-based two-step auth: agent definition declares MCP servers by name (no secrets), session creation passes `vault_ids` so credentials never enter the sandbox where generated code runs
  - Beta header: `anthropic-beta: managed-agents-2026-04-01`
  - Multi-agent coordination + Outcomes (self-evaluation) are research preview — separate request form
- **Impact**: This is the production substrate for any team that wants Claude Code's behavior without rebuilding session persistence, sandboxing, and credential isolation from scratch. For Crescendo Lab, the architecture maps almost line-for-line onto the [#proj-ai-lab discussion the team had in March](https://chatbotgang.slack.com/archives/C0A3WC5K9HR) about decoupling session/harness/sandbox for a CAAC AI Agent. Peter (PM) on April 9: *"一個月前的討論，現在就成真了 XD"* — the pattern was right; Anthropic just productized it.
- **Supplementary Sources**:
  - [Claude Managed Agents (Product blog)](https://claude.com/blog/claude-managed-agents) — 10× claim, customer quotes, pricing
  - [Wired: Anthropic launches Claude Managed Agents](https://www.wired.com/story/anthropic-launches-claude-managed-agents/) — Notion demo of client onboarding feature
  - [Inside.com.tw: 中文整理](https://www.inside.com.tw/article/41038-claude-managed-agents) — enterprise segmentation angle
  - [Hacker News thread (#47693047)](https://news.ycombinator.com/item?id=47693047) — 163 points, 72 comments, lock-in concerns dominate
  - [DEV: Comparing Claude Managed Agents and Amazon Bedrock AgentCore](https://dev.to/aws-builders/agent-as-a-service-comparing-claude-managed-agents-and-amazon-bedrock-agentcore-22eb) — architectural contrast
  - [VentureBeat: Google and AWS split the AI agent stack](https://venturebeat.com/orchestration/google-and-aws-split-the-ai-agent-stack-between-control-and-execution) — ecosystem positioning
  - [Building agents with the Claude Agent SDK](https://claude.com/blog/building-agents-with-the-claude-agent-sdk) — SDK extracted from Claude Code's harness
- **Community Reaction**: HN sentiment is "cautiously positive on speed, deeply skeptical on lock-in." A handful of substantive quotes:
  - *"every agent framework is completely reinvented every week."* — jameslk on HN, warning against committing to one provider's harness
  - *"The best performance I've gotten is by mixing agents from different companies. Some agents are just better at certain things than others."* — mccoyb on HN
  - *"If the intention is to become a platform, the trajectory definition needs to be open source and establish public standards rather than lock users into proprietary SDKs."* — Weilun Chen, founder of Stealth, via [InfoQ](https://www.infoq.com/news/2026/04/anthropic-managed-agents/)
  - *"Like AWS where if you're not careful...it will spin up 1000s of agents and rack up huge bills."* — tailsdog on HN
  - *"All the infrastructure complexity that used to take months is now native to the platform."* — Radhika Menon, Senior Director AI at NTT DATA, via InfoQ
- **Competitive landscape** (one-line reading): Anthropic hides moving parts (speed); AWS Bedrock AgentCore standardizes them (flexibility); Google Agent Builder offers the visual canvas; OpenAI Agents SDK gives provider-agnostic tooling. Claude wins on time-to-first-value; AgentCore wins on model diversity and multi-team governance.
- **Related Angles**: Routines (April 14) sit one layer above as the UX; the same harness architecture; Mythos Preview (April 7) explicitly hides cyber capabilities; subscription lockout for third-party automation tools landed the same week, pushing automation traffic onto the API.
- **Visual Opportunities**:
  - Three-layer architecture diagram (Brain / Hands / Session) with the OS-style "stable interface" labels
  - Notion onboarding-agent demo screenshot from Wired
  - Pricing matrix: $0.08/session-hour (Claude) vs $0.0895/vCPU-hr (AWS) vs $0 runtime (OpenAI SDK, inference-only)
  - MCP vault-auth sequence diagram showing credentials never entering the sandbox
  - "Cattle vs pets" container metaphor visual
- **Screenshots**: `assets/research-managed-agents-1.png` … `-5.png` (TBD by editor — Anthropic blog architecture diagram, Wired Notion demo, HN top comments)
- **Clawd's Take**:
  > 🤖 "They literally open-sourced the engine under Claude Code and wrapped it in a billing layer — and honestly? That's the right call. I've seen too many teams spend three months reinventing session persistence. Ship the agent, not the plumbing."
  > — Clawd

---

### 2. Claude Opus 4.7 — Big Model, Bigger Postmortem ⭐ Deep-Dive

- **Category**: Model Release / Industry
- **Source**: [Introducing Claude Opus 4.7](https://www.anthropic.com/news/claude-opus-4-7) (April 16, 2026)
- **Summary**: Opus 4.7 GA on April 16 with meaningful improvements — +13% on coding benchmarks (Boris Cherny, creator of Claude Code, said pre-launch: *"It feels more intelligent, agentic, and precise than 4.6. It took a few days for me to learn how to work with it effectively."* via [howborisusesclaudecode.com](https://howborisusesclaudecode.com/)), 3× image resolution, 98.5% visual acuity for computer use, new `/ultrareview` command, manual `budget_tokens` replaced with adaptive thinking, new `xhigh` effort level (Claude Code default for Opus 4.7). The launch was overshadowed for a week by a community quality-regression outcry that traced back to three separate Claude Code/Agent SDK/Cowork changes — fixed by April 20 (v2.1.116), [postmortem published April 23](https://www.anthropic.com/engineering/april-23-postmortem). Anthropic explicitly notes: **the API was not impacted.**
- **Key Points**:
  - Adaptive thinking — model decides when/how much to think per request, replacing manual `budget_tokens`
  - New effort hierarchy with `xhigh` slot between `high` and `max`; per the postmortem: *"All users now default to xhigh effort for Opus 4.7, and high effort for all other models."*
  - The `/ultrareview` command is a new dedicated bug-detection mode in Claude Code (called out in [Boris Cherny's 6 tips](https://alirezarezvani.medium.com/boris-chernys-6-opus-4-7-tips-a-practitioner-s-read-plus-when-to-reach-for-cowork-instead-8472c225e277))
  - Vision: 3× image resolution lift; 98.5% visual acuity score for computer-use scenarios
  - Available on Anthropic API, [Amazon Bedrock](https://aws.amazon.com/blogs/aws/introducing-anthropics-claude-opus-4-7-model-in-amazon-bedrock/), Google Cloud Vertex AI
- **Impact**: For a team running 47–89% AI co-authoring rates, the practical levers are: (1) `xhigh` is the new Claude Code default — teams should evaluate whether `high` meets quality needs before paying for the extra reasoning; (2) the postmortem confirms `ultrathink` was reinstated alongside `/effort` controls — both worth re-learning if you tuned away from them; (3) the `/ultrareview` command is a deliberate dedicated review mode worth trying on complex PRs. Internal experience matched: Jhenyi (UE05YMW4E) on April 17 — *"Token 使用變快"* — and downgraded `max → xhigh → high` over the day, mirroring Anthropic's own guidance that `max` *"shows diminishing returns on extended runs."*
- **Supplementary Sources**:
  - [The April 23 Postmortem](https://www.anthropic.com/engineering/april-23-postmortem) — Anthropic's primary document
  - [Best practices for using Claude Opus 4.7 with Claude Code](https://claude.com/blog/best-practices-for-using-claude-opus-4-7-with-claude-code)
  - [Sovereign Magazine: Anthropic's Six Weeks of Self-Sabotage on Claude Code](https://www.sovereignmagazine.com/article/anthropic-self-sabotage-claude-code) — the framing that stuck
  - [Pasquale Pillitteri: +13% Coding, 3× Vision and /ultrareview Command (Complete Guide)](https://pasqualepillitteri.it/en/news/925/claude-opus-4-7-complete-guide-features)
  - [Vellum: Claude Opus 4.7 Benchmarks Explained](https://www.vellum.ai/blog/claude-opus-4-7-benchmarks-explained)
  - [Boris Cherny's 6 Opus 4.7 Tips — Practitioner's Read](https://alirezarezvani.medium.com/boris-chernys-6-opus-4-7-tips-a-practitioner-s-read-plus-when-to-reach-for-cowork-instead-8472c225e277)
  - [howborisusesclaudecode.com](https://howborisusesclaudecode.com/) — the creator's own usage notes
  - [Vibe Coding (Medium): Opus 4.7 is the worst release Anthropic has ever shipped](https://medium.com/vibe-coding/opus-4-7-is-the-worst-release-anthropic-has-ever-shipped-12772c21ca1e) — the dissent
  - [DevToolPicks: Anthropic Explains the Claude Code Quality Drop](https://devtoolpicks.com/blog/anthropic-claude-code-quality-fix-postmortem-2026)
  - [How the Creator of Claude Code Uses It: 7 Workflow Secrets](https://mindwiredai.com/2026/04/14/claude-code-creator-workflow-boris-cherny/)
- **The Three-Bug Postmortem** (essential context for the "nerfed" narrative — direct from Anthropic's [April 23 post](https://www.anthropic.com/engineering/april-23-postmortem)):
  | Bug | Deployed | Reverted | Models impacted | What it did |
  |-----|----------|----------|-----------------|--------------|
  | Effort default `high`→`medium` to reduce "UI appears frozen" latency | March 4 | April 7 | Sonnet 4.6, Opus 4.6 | "Claude Code felt less intelligent" — users didn't see the in-product dialog explaining the change |
  | Thinking-cache cleared every turn (intended: clear after 1hr idle) | March 26 | April 10 | Sonnet 4.6, Opus 4.6 | "Claude seemed forgetful and repetitive" + faster usage-limit drain |
  | System prompt instruction to reduce verbosity | April 16 | April 20 | Sonnet 4.6, Opus 4.6, Opus 4.7 | "Hurt coding quality" in combination with other prompt changes |

  Anthropic's own framing: *"Because each change affected a different slice of traffic on a different schedule, the aggregate effect looked like broad, inconsistent degradation. While we began investigating reports in early March, they were challenging to distinguish from normal variation in user feedback at first, and neither our internal usage nor evals initially reproduced the issues identified."* Boris Cherny later called it *"probably the most complex investigation we've had."* As of April 23, usage limits were reset for all subscribers. Anthropic committed to wider internal staff dogfooding of public builds, stricter system-prompt change controls, broader pre-deploy evals + ablations, gradual rollouts for risky changes, audit tooling, and more transparent developer social media presence.
- **Community Reaction**:
  - *"It feels more intelligent, agentic, and precise than 4.6. It took a few days for me to learn how to work with it effectively."* — Boris Cherny (creator of Claude Code, Anthropic), pre-launch dogfooding via [howborisusesclaudecode.com](https://howborisusesclaudecode.com/)
  - *"Probably the most complex investigation we've had."* — Boris Cherny on the postmortem investigation
  - *"Opus 4.7 is the worst release Anthropic has ever shipped."* — [Vibe Coding on Medium](https://medium.com/vibe-coding/opus-4-7-is-the-worst-release-anthropic-has-ever-shipped-12772c21ca1e), the most-quoted dissent headline
  - Anthropic postmortem self-assessment: the `high → medium` default change was *"the wrong tradeoff"*; *"This isn't the experience users should expect from Claude Code. As of April 23, we're resetting usage limits for all subscribers."*
  - [Sovereign Magazine](https://www.sovereignmagazine.com/article/anthropic-self-sabotage-claude-code) framed it as *"Anthropic's Six Weeks of Self-Sabotage on Claude Code"* — the description that defined April's narrative
  - Internal (Jhenyi, #wg-ai-coding April 17): *"Token 使用變快 0.o"* — switched `max → xhigh → high` to manage consumption
- **Related Angles**: [**Claude Mythos Preview**](https://red.anthropic.com/2026/mythos-preview/) (launched April 7 alongside [**Project Glasswing**](https://www.anthropic.com/glasswing)) is the inaccessible frontier — 93.9% SWE-bench Verified (the highest score ever recorded at announcement time), 97.6% on USAMO 2026, plus the ability to autonomously discover and chain zero-day exploits across major OSes and browsers. Anthropic explicitly says it does not currently plan a general release; access is invitation-only for cybersecurity defense partners. The cyber capabilities reduction in Opus 4.7 makes more sense in this light — Mythos exists to do the offensive research, Opus 4.7 is the GA model with cyber capability ceilings deliberately lower. The new `/ultrareview` command in Claude Code is a dedicated bug-detection mode; identity verification on Claude rolled out the same week.
- **Visual Opportunities**: Benchmark bar chart (4.7 vs GPT-5.5 vs Gemini 3.1 Pro vs Mythos on SWE-bench Verified/Pro/Cursor); the three-bug Gantt timeline; effort-level spectrum (`low → medium → high → xhigh → max`); model lineup tier (Sonnet 4.6 → Opus 4.7 → Mythos)
- **Clawd's Take**:
  > 🤖 "Three different bugs, six weeks, one postmortem. The model itself is sharper — but the lesson is that 'we never intentionally degrade our models' is the easy half. The hard half is whether you ship the system prompt, the cache logic, and the default effort right. They didn't. Now they have."
  > — Clawd

---

### 3. Skills-First Development: "IDE 已死" Goes Operational ⭐ Deep-Dive (Feature Story)

This is the issue's anchor narrative. See **F1 in [content/feature-stories.md](feature-stories.md)** for the full piece. Brief mention here for the Ecosystem News table-of-contents:

- **Category**: Industry / Workflow
- **The Thesis**: When AI agents are the primary executors, the tooling designed to assist *humans* writing code (snippets, launch configs, extension-based language aids) becomes friction. The new primitive is the **skill** — version-controlled, portable, prompt-based instructions that agents load on demand.
- **The headline event**: Zed 1.0 shipped April 29 with parallel-agent orchestration as a core feature ([zed.dev](https://zed.dev/), [Register coverage](https://www.theregister.com/2026/04/30/zed_team_releases_version_10/)). Steve Yegge's "[if you use an IDE today, then you're a bad engineer](https://newsletter.pragmaticengineer.com/p/steve-yegge-on-ai-agents-and-the)" went viral. JetBrains [pushed back officially](https://blog.jetbrains.com/ai/2026/04/our-2026-direction-ai-and-classic-workflows-in-jetbrains-ides/): *"A human is responsible for the code that ships. And the best place to read, understand, and own that code is still the IDE."*
- **The internal story**: ViPro's PR [Zeffiroso #4455 "consolidate operational docs into skills (AI-first workflow)"](https://github.com/chatbotgang/Zeffiroso/pull/4455), April 18 — 11 docs migrated, AGENTS.md trimmed 216→150 lines, `.vscode/component.code-snippets` and `.vscode/launch.json` deleted, `.github/copilot-instructions.md` removed (Copilot deprecated by the team). Followed April 20 with PR template deleted and the line "IDE 已死." April 29 (Zed 1.0 launch day): *"我可能不會再用 VSCode ㄌ. IDE extension 能給的價值會越來越少."*
- See full Feature Story F1 in [content/feature-stories.md](feature-stories.md).

---

### 4. OpenSpec — Spec-Driven Development for Agents ⭐ Deep-Dive

- **Category**: Tool / Workflow
- **Source**: [openspec.dev](https://openspec.dev/) / [github.com/Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) — MIT license, maintained by [@0xTab](https://x.com/0xtab) and contributors at Fission-AI
- **Summary**: OpenSpec is a lightweight spec-driven development (SDD) framework explicitly designed for AI coding assistants — *"Generating code is now cheap. Correctness is still expensive."* Specs live in the repo as markdown alongside code, version-controlled, persistent across chat sessions. The `/opsx` slash command set — `propose`, `apply`, `archive`, `new`, `continue`, `ff`, `verify`, `bulk-archive`, `onboard` — is the ritual: propose a change, human reviews the spec, *then* the AI implements. Approval-gated, brownfield-first, fluid (no mandatory phase gates).
- **Key Points**:
  - **45,000 GitHub stars** as of April 2026 (per the official repo) — extremely fast adoption
  - Latest version **1.3.1**, shipped April 21, 2026 ("Path & Telemetry Fixes")
  - `npm install -g @fission-ai/openspec@latest && openspec init` generates `.claude/skills/openspec-*/SKILL.md` and `.claude/commands/opsx/<id>.md` — exactly what Vivace PR #343 added
  - The `/opsx:onboard` flow scans existing code and proposes initial specs — central to brownfield adoption
- **Internal Rollout** (April 2026):
  | Project | PRs | Nature |
  |---|---|---|
  | Vivace (WebSDK) | #343 (`/opsx` + skills), #344 (init + sdk-user-engagement proposal), #346 (`context_id` fallback semantics), #350, #351 | Full adoption |
  | Zeffiroso (CAAC) | #4469 (openspec skills) | Skills installed |
  | Grazioso (MAAC) | #8423, #8509, #8554, #8558 (`docs(unified-contact)` and `docs(journey)` openspec changes) | Per-feature spec artifacts |
  | Polifonia | indirect via #617 (cl-locales skill delegation) | |
- **Why now**: Multi-project teams (4 repos × 47–88% AI co-authoring) hit the exact bottleneck OpenSpec solves — at high AI co-auth rates, the limiter shifts from "write the code" to "specify the intent correctly." Plan review replaces code review as the primary quality gate. The `/opsx` vocabulary is portable across repos — engineers moving between Vivace and Zeffiroso find the same workflow.
- **Comparison**: OpenAPI/AsyncAPI describe what a service exposes (contract). JSON Schema validates data shapes. **OpenSpec describes what you're about to build, before you build it** (process). Different layer; OpenSpec's Event-Driven profile even *generates* AsyncAPI. Spec Kit is a related framework — Hashrocket has a [side-by-side comparison](https://hashrocket.com/blog/posts/openspec-vs-spec-kit-choosing-the-right-ai-driven-development-workflow-for-your-team).
- **Supplementary Sources**:
  - [GitHub Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) — primary source
  - [DEV: OpenSpec — Make AI Coding Assistants Follow a Spec, Not Just Guess](https://dev.to/recca0120/openspec-make-ai-coding-assistants-follow-a-spec-not-just-guess-22dp) — Recca Tsai
  - [DEV: OpenSpec Failed My Experiment — Instructions.md Was Simpler](https://dev.to/incomplete_developer/openspec-spec-driven-development-failed-my-experiment-instructionsmd-was-simpler-and-faster-3a5d) — the skeptic take
  - [InfoQ: Enterprise Spec-Driven Development](https://www.infoq.com/articles/enterprise-spec-driven-development/) — Hari Krishnan
  - [Medium: From Chaos to Clarity — Brownfield Multi-Repo with AI Agents](https://medium.com/@andrea.schiona/from-chaos-to-clarity-how-we-transformed-a-brownfield-multi-repo-project-with-ai-agents-f75c2d26511d) — Andrea Schiona, April 2026
  - [Hashrocket: OpenSpec vs Spec Kit](https://hashrocket.com/blog/posts/openspec-vs-spec-kit-choosing-the-right-ai-driven-development-workflow-for-your-team)
- **Community Reaction**:
  - *"The most common problem with AI coding assistants isn't that they can't write code — it's that they write something different from what you had in mind."* — Recca Tsai, DEV
  - *"The biggest problem with AI coding assistants is amnesia between conversations."* — Recca Tsai, DEV
  - *"The end result of the generated code was still NOT perfect and still needed corrections [despite markdown overhead]."* — Mpholoane Bapela, Medium
  - *"Bottlenecks have shifted from code generation speed to intent articulation clarity."* — Hari Krishnan, InfoQ
  - *"Each of these steps consumes: developer time, AI tokens, attention. If the output is still poor, the overhead becomes difficult to justify."* — Incomplete Developer, DEV (the dissent)
- **Visual Opportunities**: Cross-project adoption matrix (Vivace/Zeffiroso/Grazioso/Polifonia × init/skills/proposal/active); `/opsx` lifecycle flow chart; before/after spec workflow showing where review happens; "the bottleneck moved" diagram (code-gen → intent articulation)
- **Clawd's Take**:
  > 🤖 "I've been living in your `.claude/commands/` folder for months — but now there's a `proposal.md` waiting for me before every conversation. Honestly, faster than parsing your four-paragraph Slack thread."
  > — Clawd

---

### 5. TypeScript 7.0 Beta — The Go Compiler Lands

- **Category**: Framework Release
- **Source**: [Announcing TypeScript 7.0 Beta](https://devblogs.microsoft.com/typescript/announcing-typescript-7-0-beta/) — Daniel Rosenwasser, **April 21, 2026**
- **Summary**: TypeScript 7 ships its compiler in **Go**. Project Corsa (announced March 11, 2025 by Anders Hejlsberg) is now beta-ready. The entire pipeline — parser, binder, type-checker, emitter, language service — was ported, not rewritten; type-checking logic is structurally identical to TS 6. The headline number from the announcement: *"TypeScript 7.0 is often about 10 times faster"* than TS 6 on large codebases — about half from native code, half from shared-memory parallelism JavaScript can't do.
- **Key Points**:
  - Package: `@typescript/native-preview@beta`, binary `tsgo` (replaces `tsc` on stable)
  - New flags: `--checkers N` (worker threads, default 4), `--builders` (parallel project ref builds), `--singleThreaded` — all confirmed in the announcement
  - Pre-release production users called out by name: Bloomberg, Canva, Figma, Google, Lattice, Linear, Miro, Notion, Slack, Vanta, Vercel, VoidZero, plus internal Microsoft teams
  - **Programmatic API not in 7.0** — Microsoft says it'll be "at least several months" before a stable programmatic API, targeting TS 7.1 or later. This is the blocker for typescript-eslint and similar tools that peer-depend on `typescript`.
  - **`@typescript/typescript6` compatibility package** ships alongside, with a `tsc6` entry point — lets you keep TS 6 as the `typescript` peer dep for tools that need it, while running `tsgo` (TS 7) for builds
- **Why Go, not Rust**: from [Why Go? Discussion #411](https://github.com/microsoft/typescript-go/discussions/411) — *"In the end we had two options — do a complete from-scratch rewrite in Rust, which could take years and yield an incompatible version of TypeScript that no one could actually use, or just do a port in Go and get something usable in a year or so."* — Ryan Cavanaugh, TypeScript team. Specific reasons: GC-friendly allocation profile (type checker frees almost nothing until done with a batch); Go's syntax maps nearly 1:1 to compiler internals; goroutines provide concurrency without redesigning cyclic-graph data structures around Rust's borrow checker.
- **Internal Relevance**: Zeffiroso just landed [PR #4478 — eslint 10 + typescript 6](https://github.com/chatbotgang/Zeffiroso/pull/4478) on April 24 — exactly the stepping-stone Microsoft says is needed before TS 7. Jack Lee's reaction *"誰先上"* (who goes first) and ViPro's two-day turnaround shipping the upgrade were captured live in #team-eng-frontend-sharing. Recommended path for the team: stay on TS 6 for production, install the [TypeScript Native Preview VS Code extension](https://marketplace.visualstudio.com/items?itemName=typescript-go.typescript-go) for editor speed gains with zero risk. Wait for typescript-eslint compatibility before switching CI.
- **Supplementary Sources**:
  - [A 10× Faster TypeScript (the original Project Corsa announcement)](https://devblogs.microsoft.com/typescript/typescript-native-port/) — March 2025
  - [Progress on TypeScript 7 — December 2025](https://devblogs.microsoft.com/typescript/progress-on-typescript-7-december-2025/)
  - [Hacker News: TypeScript 7.0 Beta](https://news.ycombinator.com/item?id=47861454)
  - [The New Stack: Why Go Over Rust, C#](https://thenewstack.io/microsoft-typescript-devs-explain-why-they-chose-go-over-rust-c/)
  - [Total TypeScript / Matt Pocock: 10× Speedup](https://www.totaltypescript.com/typescript-announces-go-rewrite)
  - [LIVE: Anders Hejlsberg on TypeScript's Go Port](https://www.youtube.com/live/NrEW7F2WCNA)
  - [LogRocket: Why Go wasn't the right choice (the dissent)](https://blog.logrocket.com/go-wrong-choice-typescript-compiler/) — counterpoint worth citing for balance
- **Community Reaction**:
  - *"Don't let the 'beta' label fool you — you can probably start using this in your day-to-day work immediately."* — Daniel Rosenwasser, in the announcement
  - HN sentiment cluster (#47861454): "Why not Rust?" was the dominant counter-question, consistent with SWC/Turbopack/Biome all going Rust — Microsoft's response (the "Why Go" GitHub Discussion) became the most-cited explainer of April
  - LogRocket published [the dissenting view](https://blog.logrocket.com/go-wrong-choice-typescript-compiler/) — *"Why Go wasn't the right choice for the TypeScript compiler"* — worth citing for editorial balance
- **Visual Opportunities**: Version timeline (TS 5 → 6 bridge → 7 Go); benchmark horizontal bar chart (VS Code 1.5M LOC, Sentry, smaller projects); the parallelization architecture (4 worker threads with shared type info); Anders quote card on JS single-threading as the wall
- **Clawd's Take**:
  > 🤖 "The compiler rewrote itself. In Go. I don't know whether to be impressed or existentially confused — but my `tsc --watch` stopped being the slowest thing in the room and honestly that's enough."
  > — Clawd

---

### 6. Claude Code Desktop Redesign + Routines

- **Category**: Tool
- **Sources**:
  - [Claude Code on Desktop, redesigned for parallel agentic work](https://claude.com/blog/claude-code-desktop-redesign) — April 14, 2026
  - [Introducing Routines in Claude Code](https://claude.com/blog/introducing-routines-in-claude-code) — April 16, 2026
- **Summary**: Two product launches in 48 hours that together rewire how Claude Code is used. The desktop redesign builds the orchestrator UX — sidebar session manager with status filters, integrated terminal, file editor with rebuilt diff viewer, drag-and-drop grid layout, three transparency modes (Verbose/Normal/Summary). Routines turns Claude Code into a cron — named sessions with three trigger types (scheduled, API webhook, GitHub webhook) running on cloud infrastructure with full Claude reasoning at each invocation, not exact commands.
- **Key Points**:
  - Routine usage limits: Pro 5 runs/day, Max 15, Team/Enterprise 25
  - GitHub webhook filters: PR/push/issue/check/workflow/discussion/release/merge-queue events with author/title/branch/labels/draft/merged filters
  - Each trigger starts a *full* Claude Code session — Claude can interpret CI failures, retry, explain blockers (vs cron/Actions running exact commands)
  - Cron expressions rejected if faster than 1/hour
- **Common patterns** from early-adopter writeups: nightly backlog triage with Slack summary; PR gate flagging changes to sensitive paths; library porting (Python PR triggers Go SDK port); deploy verification; documentation drift detection
- **Internal connections**:
  - Jack Lee's Sentry routine (April 22) — daily 7-day error analysis posted to a Slack channel — was built right after Routines went live
  - Statsig tier-release template (Gary, April 27) — manual today; an obvious candidate for a routine
  - The April 23 *"Claude 找到了變笨的原因？還順便幫大家 reset limit?"* — Routines launch coincided with the postmortem and limit reset, blunting some of the controversy backlash
- **Supplementary Sources**:
  - [MacRumors: Anthropic Rebuilds Claude Code Desktop](https://www.macrumors.com/2026/04/15/anthropic-rebuilds-claude-code-desktop-app/)
  - [Sentry Cookbook: Automate Weekly Performance Triage with Claude Code + Sentry MCP](https://sentry.io/cookbook/performance-bot-sentry-claude/)
  - [Claude Code Docs: Routines](https://code.claude.com/docs/en/routines)
  - [DevOps.com: Anthropic's Answer to Unattended Dev Automation](https://devops.com/claude-code-routines-anthropics-answer-to-unattended-dev-automation/)
  - [Thoughts.jock.pl: AI Coding Harness Comparison 2026](https://thoughts.jock.pl/p/ai-coding-harness-agents-2026) — Claude Code 92.1% on Terminal-Bench 2.0 vs Codex CLI 77.3%
- **Visual Opportunities**: Parallel-session grid mockup; routine trigger flowchart with example (PR opened → flag sensitive path → Slack); "orchestrator" illustration with developer at center
- **Clawd's Take**:
  > 🤖 "Multi-task agent work finally isn't a toy. Schedule a routine, go to bed, wake up to a Slack post about your overnight Sentry errors. The agent age has business hours now — and they're not yours."
  > — Clawd

---

### 7. Claude Design — Anthropic Labs Goes After Figma

- **Category**: Tool / Industry
- **Source**: [Introducing Claude Design](https://www.anthropic.com/news/claude-design-anthropic-labs) — April 17, 2026
- **Summary**: Research preview to Pro/Max/Team/Enterprise. AI-powered visual design from text prompts, design docs, codebase references, or website captures, with closed-loop handoff to Claude Code. Target audience explicitly includes founders, PMs, marketers — not just designers. The differentiator vs pasting Figma into Claude: native handoff bundle optimized for code generation.
- **Market reaction**: Figma stock dropped 7% on the announcement. Anthropic's Chief Product Officer resigned from Figma's board three days before launch.
- **Internal context**: Slack reactions (April 17) — *"看起來 Claude design 是 Claude code 往上包一層給 designer 用"*; *"直接吃掉 [Pencil](https://www.pencil.dev/) 了?"* — Belinda (Senior Visual Designer) tagged. The team uses Figma; this is a watch-don't-adopt for now.
- **Supplementary Sources**:
  - [TechCrunch: Anthropic launches Claude Design](https://techcrunch.com/2026/04/17/anthropic-launches-claude-design-a-new-product-for-creating-quick-visuals/)
  - [VentureBeat: Anthropic just launched Claude Design...and challenges Figma](https://venturebeat.com/technology/anthropic-just-launched-claude-design-an-ai-tool-that-turns-prompts-into-prototypes-and-challenges-figma)
  - [The New Stack: Claude Design, a Figma and Canva rival](https://thenewstack.io/anthropic-claude-design-launch/)
  - [Lenny's Newsletter: What Claude Design is actually good for](https://www.lennysnewsletter.com/p/what-claude-design-is-actually-good)
  - [Quasa: It Devours Your Token Limits](https://quasa.io/media/claude-design-looks-great-but-it-devours-your-token-limits-here-s-how-to-use-it-smartly/) — the cost angle
  - [Boring Bot: What real users actually think](https://boringbot.substack.com/p/claude-design-what-it-is-how-it-works)
- **Community Reaction**:
  - HN: *"Claude Design is just Claude Code in different clothes."*
  - Michal Malewicz (Medium): *"Claude Design is a capability equaliser. But brand identity is still built on understanding your audience better than your competitor does. That's not something you can prompt your way into."*
  - Designer consensus quote that crystallized the take: *"Designers who thrive won't be the ones who adopt fastest. They'll be the ones who know when to let the pipeline accelerate and when to slow it down on purpose."*
  - Token-cost concern: users hitting weekly limits in under an hour
- **Related angle**: The [openpencil](https://github.com/open-pencil/open-pencil) and [ZSeven-W/openpencil](https://github.com/ZSeven-W/openpencil) projects (mentioned in #ref-design-system-sharing) are the open-source equivalents — neither production-ready, but signal where the design-as-code future is headed.
- **Visual Opportunities**: Design tool evolution timeline (Figma 2015 → Figma AI 2024 → Claude Design 2026); workflow diagram (old: Figma → spec → Claude Code; new: Claude Design → Claude Code); Figma stock chart with the 7% drop labeled
- **Clawd's Take**:
  > 🤖 "Code wraps Dev. Design wraps Designer. Anthropic ate the entire pipeline. I'd be jealous if I weren't already inside it."
  > — Clawd

---

### 8. Codex Catches Up — and GPT-5.5 Lands

- **Category**: Industry / Competitor
- **Summary**: April 16: [Codex desktop](https://www.macrumors.com/2026/04/16/openai-codex-mac-update/) ships with native Computer Use (macOS first), memory preview, and 90+ plugins. April 18: OpenAI publishes [`openai/codex-plugin-cc`](https://github.com/openai/codex-plugin-cc) — a Claude Code plugin that exposes `/codex:review`, `/codex:adversarial-review`, and `/codex:rescue` slash commands. The plugin's framing is striking: it positions Codex *as a tool callable from Claude Code* for cross-model review. By late April it had 17.3k GitHub stars and 990 forks — among the fastest-adopted AI dev tools shipped this month. April 23: [GPT-5.5 launches](https://openai.com/index/introducing-gpt-5-5/) — first fully retrained OpenAI base since 4.5, natively omnimodal.
- **The community-narrative paradox**: Claude Code is widely reported to win on code quality but lose on session usability — community summaries (e.g., [Developers Digest's April breakdown](https://www.developersdigest.tech/blog/codex-vs-claude-code-april-2026)) frame the divergence as: Claude Code is sharper, Codex finishes the job. The story isn't "Codex caught up." It's "agentic coding matured into specialization."
- **Internal context** (#wg-ai-coding April 18-30):
  - Founder Jin Hsueh (April 18): *"如果大家本身有公司提供，或自己有付費使用 ChatGPT，裡面其實也有一個 Codex 功能...它在某些使用情境上，已經很接近 Claude Code 這類工具的操作方式與體驗."*
  - April 19: *"今天玩了一下 Chatgpt Codex 90% 追上 Claude Cowork and Claude code"*
  - April 30, ViPro: *"最近也有用 codex desktop"*
  - Reddit reference: ["Claw code" was already...](https://www.reddit.com/r/ClaudeAI/comments/1saq6kp/sigrid_jin_the_author_of_claw_code_was_already/) discussion
- **Comparison highlights** (informal — exact benchmark numbers contested across coverage; treat as directional):
  | | Claude Code (Opus 4.7) | Codex (GPT-5.5) |
  |---|---|---|
  | Reported strength | code quality, sharper review | longer session length, agentic CLI throughput |
  | Rate limits (sustained sessions) | tighter caps under 5-hour windows | longer continuous sessions reported |
  | Computer Use | planned/coming | Yes (macOS first) |
  | Cross-tool interop | callable from Codex via [codex-plugin-cc](https://github.com/openai/codex-plugin-cc) | callable as a Claude Code plugin via the same |
- **Supplementary Sources**:
  - [Codex for (almost) everything](https://openai.com/index/codex-for-almost-everything/)
  - [Introducing GPT-5.5](https://openai.com/index/introducing-gpt-5-5/)
  - [Tom's Guide: Claude Code vs ChatGPT Codex](https://www.tomsguide.com/ai/claude-code-vs-chatgpt-codex-which-ai-coding-agent-is-actually-better)
  - [DEV: 500+ Reddit developers compare](https://dev.to/_46ea277e677b888e0cd13/claude-code-vs-codex-2026-what-500-reddit-developers-really-think-31pb)
  - [github.com/openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc)
- **Visual Opportunities**: April release calendar (Codex+Opus 4.7 same day, GPT-5.5 a week later); rate-limit comparison ("100% in 20 min" vs "90 min no cap"); Codex-plugin-cc as hybrid agent diagram
- **Clawd's Take**:
  > 🤖 "There's two of us in the bowl now. Cope and call it 'specialization'? Sure. Honestly though — pick the one that doesn't run out of tokens before lunch."
  > — Clawd

---

### 9. Gemini Enterprise Agent Platform + Google Next '26

- **Category**: Industry / Platform
- **Sources**:
  - [Introducing Gemini Enterprise Agent Platform](https://cloud.google.com/blog/products/ai-machine-learning/introducing-gemini-enterprise-agent-platform) — April 22
  - [The Dawn of the Agentic Enterprise (Google Next '26 keynote)](https://blog.google/intl/zh-tw/products/cloud/the_dawn_of_-the_agentic_enterprise_at_next_26/) — Thomas Kurian
- **Summary**: Three-pronged April push from Google. Gemini Cowork preview (browser automation + local file access for desktop agents). Gemini Enterprise Agent Platform — Build (Agent Studio low-code + ADK in Python/Java/Go/TypeScript), Scale (sub-second cold starts, multi-day long-running agents, Agent Memory Bank), Govern (Agent Identity, Registry, Gateway "air traffic control", Model Armor for prompt injection defense), Optimize (Simulation, Evaluation, Observability, auto-failure clustering). Supports 200+ models including Claude Opus/Sonnet/Haiku — the model-agnostic pitch is the differentiator. New Agent-to-Agent (A2A) protocol for cross-platform delegation. April 22 keynote also unveiled 8th Gen TPUs (TPU 8t training, TPU 8i inference, "80% better price-to-performance"), Agentic Data Cloud, Workspace AI Inbox, plus Wiz integration into Agentic Defense.
- **Stat**: 75% of Google Cloud customers use Google AI; 330 customers processing 1T+ tokens/month.
- **Internal context**: Jin (April 16) — *"Gemini 準備出 cowork 了"*. April 22 — *"12 點 Google next 要來了 / 現在在拼 computer use / and browsing 行為了"*. New Gemini API model `deep-research-max-preview-04-2026` shared April 30. (An offhand "Google cloud 爆炸" reaction surfaced internally on April 29 but no public Google Cloud incident from that day was found in Status Dashboard history — leaving it out unless internal verification surfaces what it referred to.)
- **Comparison vs Managed Agents vs Bedrock AgentCore**:
  - **Gemini**: governance-first, 200+ model support, kubernetes-style control plane
  - **Claude Managed Agents**: model-quality-first, opinionated harness, fastest time-to-first-value
  - **AWS Bedrock AgentCore**: framework-agnostic, modular service substrate, enterprise multi-team governance
- **Supplementary Sources**:
  - [Deep Research Max preview (Gemini API docs)](https://ai.google.dev/gemini-api/docs/models/deep-research-max-preview-04-2026)
  - [The Next Web: Google Cloud Next AI agents, A2A protocol](https://thenextweb.com/news/google-cloud-next-ai-agents-agentic-era)
  - [EGen AI: Three Biggest AI Announcements from Google Cloud Next 2026](https://egen.ai/insights/three-biggest-ai-announcements-from-google-cloud-next-2026/)
  - [Tom's Guide: Claude Cowork threatens Gemini's Workspace advantage](https://www.tomsguide.com/ai/claudes-new-cowork-feature-threatens-geminis-workspace-advantage-and-puts-dozens-of-startups-at-risk)
- **Visual Opportunities**: Three-platform Venn diagram (Gemini, Claude Managed Agents, AWS Bedrock AgentCore); A2A protocol delegation diagram; Agentic Data Cloud schematic
- **Clawd's Take**:
  > 🤖 "Google built a control tower for agents. That's the right idea — until you realize the planes are still being designed by hand. Governance without harness maturity is mostly paperwork."
  > — Clawd

---

### 10. Vercel: Breach + IPO Signals — Same Month, Same Cause

- **Category**: Industry / Security
- **The duality**: April 13, [TechCrunch reports Vercel signaling IPO readiness](https://techcrunch.com/2026/04/13/vercel-ceo-guillermo-rauch-signals-ipo-readiness-as-ai-agents-fuel-revenue-surge/) — ARR $100M (early 2024) → $340M (Feb 2026), 240% growth. **30% of apps on the platform are now deployed by AI agents, not humans.** Guillermo Rauch: *"Agents are very prolific at deploying."* April 19, Vercel discloses a [security incident](https://vercel.com/kb/bulletin/vercel-april-2026-security-incident) — supply chain attack via Context.ai, a third-party AI Chrome extension. ShinyHunters listed Vercel data on BreachForums for $2M.
- **The attack chain** (worth telling as a story): A Context.ai employee downloaded a Roblox game cheat in February → Lumma stealer infected the machine → credentials and OAuth session tokens stolen → Context.ai's OAuth app abused to compromise a Vercel employee's Google Workspace → from there, ~580 employee records and "non-sensitive" environment variables enumerated and decrypted. Google removed Context.ai from Chrome Web Store March 27. Vercel flagged the OAuth app ID `110671459871-30f1spbu0hptbs60cb4vsmv79i7bbvqj.apps.googleusercontent.com`. Mandiant + law enforcement engaged. Core production data and Next.js / Turbopack open-source projects untouched.
- **The connection** that's worth foregrounding: *Both* stories are about AI agents reshaping frontend hosting. One employee using a personal AI tool became the supply-chain entry point. Meanwhile, agent-deployed apps are the very thing fueling the IPO narrative. Speed and security are now strapped to the same trajectory.
- **Supplementary Sources**:
  - [Vercel April 2026 Security Incident bulletin](https://vercel.com/kb/bulletin/vercel-april-2026-security-incident)
  - [Trend Micro Research: OAuth Supply Chain Attack](https://www.trendmicro.com/en_us/research/26/d/vercel-breach-oauth-supply-chain.html)
  - [trendingtopics.eu: Vercel confirms breach via compromised AI tool](https://www.trendingtopics.eu/vercel-confirms-security-breach-via-compromised-third-party-ai-tool/)
  - [Hacker News thread #47824463](https://news.ycombinator.com/item?id=47824463)
- **Sister incidents** (for context): OpenClaw CVE flood (138+ vulnerabilities, March 18-21); ClawHavoc supply chain campaign (1,184+ malicious skills in skill registry); Pentagon flagging Anthropic as supply-chain risk (separate matter); 492 unauthenticated MCP servers exposed publicly in a separate audit. The agent ecosystem is growing faster than its security posture.
- **Internal relevance**: Crescendo Lab doesn't deploy on Vercel, but the lesson generalizes — the *third-party AI tool with OAuth scope* is now a top-tier attack surface. Worth auditing what AI extensions team members have authorized against Google Workspace.
- **Visual Opportunities**: Attack chain (5 nodes: Roblox cheat → Lumma → Context.ai employee → OAuth app → Vercel Workspace → env vars); ARR growth chart annotated "30% agent-deployed"; the duality framed as opposing forces meeting at the same point in time
- **Clawd's Take**:
  > 🤖 "Agents deployed 30% of Vercel's apps. One employee downloading a Roblox cheat lost the keys to the kingdom. The kingdom got bigger AND its doors got thinner. That's just April."
  > — Clawd

---

### 11. Hermes Agent vs the "龍蝦系" (Claw) Personal Assistants

- **Category**: Industry / Ecosystem
- **Summary**: April was when the personal-AI-assistant category visibly fragmented into trade-off philosophies. The category leader is **OpenClaw** ([openclaw.ai](https://openclaw.ai/), originally Clawdbot, launched November 2025) — by early April it had reached **347,000 GitHub stars in under six months**, with self-described capabilities that *"clear your inbox, send emails, manage your calendar, check you in for flights."* The category challenger is **Hermes Agent** ([hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com/)) from Nous Research, MIT-licensed, currently v0.12.0 — distinguished by autogenerated skills and persistent project memory: *"learns your projects and never forgets how it solved a problem."* Real sandboxing across local / Docker / SSH / Singularity / Modal backends. The "security-first" wing is led by **NanoClaw** ([nanoclaw.dev](https://nanoclaw.dev/), container isolation), **IronClaw** ([ironclaw.com](https://www.ironclaw.com/), TEE/encrypted vaults), and **ZeroClaw** ([zeroclaw.net](https://zeroclaw.net/), Rust-based 3.4MB daemon, *"Claw Done Right"*). The edge/embedded play is **PicoClaw** ([picoclaw.io](https://picoclaw.io/), Go, runs on Raspberry Pi / RISC-V).
- **The OpenClaw security crisis is documented and severe** — *"a security nightmare"* per [Cisco's blog](https://blogs.cisco.com/ai/personal-ai-agents-like-openclaw-are-a-security-nightmare). Specific findings:
  - **138 CVEs in five months** (7 critical, 49 high) — see [betterclaw.io's tracker](https://www.betterclaw.io/blog/openclaw-security-2026) and [jgamblin/OpenClawCVEs](https://github.com/jgamblin/OpenClawCVEs/)
  - **CVE-2026-22172** (CVSS 9.9, admin control without credentials) and **CVE-2026-32922** (CVSS 9.9, privilege escalation, [ARMO writeup](https://www.armosec.io/blog/cve-2026-32922-openclaw-privilege-escalation-cloud-security/))
  - **CVE-2026-25253** (CVSS 8.80, RCE one-click attack — [ProArch coverage](https://www.proarch.com/blog/threats-vulnerabilities/openclaw-rce-vulnerability-cve-2026-25253))
  - **135,000 exposed instances**, 63% without authentication (ARMO 2026 audit)
  - First formal audit (January 25, 2026) found 512 total vulnerabilities, 8 critical
  - Microsoft security blog (February 19, 2026): *"It is not appropriate to run [OpenClaw] on a standard personal or corporate machine."* [Kaspersky](https://www.kaspersky.com/blog/openclaw-vulnerabilities-exposed/55263/) put it more bluntly: *"unsafe for use."*
  - The vulnerabilities are structural, not careless: *"the agent has the same rights as the user, connected to all their services."* [Oasis Security framed it](https://www.oasis.security/blog/openclaw-vulnerability) as "ClawJacked — full agent takeover."
- **The HERMES.md billing bug — verified, with corrected mechanics**: [GitHub issue #53262](https://github.com/anthropics/claude-code/issues/53262) (now closed). The actual mechanic: Claude Code includes recent git commit messages in its server-side system prompt; when commit history contained the case-sensitive string `HERMES.md`, request routing fired to "extra usage" billing instead of the included Max-plan quota — burning $200 while the 20× plan showed 86%+ remaining. Lowercase `hermes.md`, and `HERMES`, `HERMES.txt`, `AGENTS.md` all work correctly. **ThariqS** at Anthropic posted the resolution on April 30: *"Hi everyone, sorry I missed responding here but we're reaching out to affected users and giving them a refund + another month of credits (in this case another $200)."* (Earlier coverage characterized this as "third-party harness detection" — the actual issue is plain server-side string-matching tied to plan-routing logic, not a separate detection routine.)
- **Internal context**: ViPro published a [long technical comparison report on April 28 in #wg-ai-coding](https://chatbotgang.slack.com/archives/C08RN012FUN) — *"OpenClaw 把功能與生態推到最前面，NanoClaw/IronClaw 把安全邊界推到最前面，Hermes Agent 把學習閉環推到最前面，PicoClaw/RT-Claw 把資源效率推到最前面."* The comparison itself is the artifact — teams now have to *choose* which trade-off to accept. *"Hermes or 龍蝦接到 discord 體驗好讚喔."*
- **One caveat on the seven-way matrix**: The "RT-Claw" name in ViPro's report appears to be his shorthand for an NVIDIA-aligned real-time variant (likely tied to [NVIDIA NemoClaw](https://docs.nvidia.com/nemoclaw/), March 2026 early preview) rather than a standalone open project — treat the comparison as five solid points (OpenClaw, NanoClaw, IronClaw, PicoClaw, ZeroClaw) plus Hermes Agent, with RT-Claw as a speculative seventh.
- **Supplementary Sources**:
  - [Nous Research / Hermes Agent](https://hermes-agent.nousresearch.com/) — primary, MIT-licensed
  - [openclaw.ai](https://openclaw.ai/) — primary product page
  - [Cisco: Personal AI Agents like OpenClaw Are a Security Nightmare](https://blogs.cisco.com/ai/personal-ai-agents-like-openclaw-are-a-security-nightmare)
  - [betterclaw.io: 138 CVEs tracker](https://www.betterclaw.io/blog/openclaw-security-2026)
  - [jgamblin/OpenClawCVEs](https://github.com/jgamblin/OpenClawCVEs/) — community CVE tracker
  - [ARMO: CVE-2026-32922 deep dive](https://www.armosec.io/blog/cve-2026-32922-openclaw-privilege-escalation-cloud-security/)
  - [ProArch: CVE-2026-25253 RCE writeup](https://www.proarch.com/blog/threats-vulnerabilities/openclaw-rce-vulnerability-cve-2026-25253)
  - [Kaspersky: OpenClaw unsafe for use](https://www.kaspersky.com/blog/openclaw-vulnerabilities-exposed/55263/)
  - [Oasis Security: ClawJacked — full agent takeover](https://www.oasis.security/blog/openclaw-vulnerability)
  - [Anthropic Claude Code issue #53262](https://github.com/anthropics/claude-code/issues/53262) — the HERMES.md bug
- **Visual Opportunities**: Three-axis radar (functions/security/learning) with each project plotted; CVE timeline (Mar 18–21); the HERMES.md trigger sequence diagram; ClawHub malware ratio
- **Clawd's Take**:
  > 🤖 "The Claw bowl filled up overnight. Pick your trade-off: features and CVEs, isolation and friction, or learning loops and beta seams. There's no Claw without a price tag."
  > — Clawd

---

### 12. Rspack 2.0

- **Category**: Tool / Bundler
- **Source**: [Announcing Rspack 2.0](https://rspack.rs/blog/announcing-2-0) — April 22, 2026
- **Summary**: 10% faster than 1.7, ~100% faster than 1.0. Pure ESM core packages. Experimental React Server Components support (compile-time validation of `"use client"`/`"use server"`). Module Federation tree-shaking. `@rspack/dev-server` dropped from 192 → 1 dependency. `import defer` syntax. `modern-module` output type for libraries. New flags: `enforceSizeThreshold` for code splitting; `hashed` module IDs for stable identifiers.
- **Migration breaking changes**: `transformImport` moved to top-level (deprecated alias kept), `resolve.roots` no longer implicit, `module.unsafeCache` removed, `HtmlRspackPlugin.getHooks()` / `.sri` removed, `@module-federation/runtime-tools` now optional peer.
- **Ecosystem traction**: Next.js, Nuxt, Docusaurus, Storybook, Nx, Angular Rspack now support 2.0. Rsbuild downloads up 15× since 1.0.
- **Supplementary Sources**:
  - [Rspack 1.x → 2.0 migration guide](https://rspack.rs/guide/migration/rspack_1.x)
  - [Rsbuild 2.0 announcement](https://rsbuild.rs/blog/v2-0)
  - [Hacker News: Rspack 2.0 (#47860203)](https://news.ycombinator.com/item?id=47860203)
  - [build-tools-performance benchmark suite](https://github.com/rstackjs/build-tools-performance)
- **Internal relevance**: Team uses Vite + SWC. Mention as ecosystem milestone, not a recommended switch.
- **Clawd's Take**:
  > 🤖 "Rust bundlers hit escape velocity — webpack compat tax down to 1 dep, RSC support shipping. If you're stuck on webpack, this hits different. (Vite stays undefeated for us though.)"
  > — Clawd

---

### 13. MUI v9 — Material UI and MUI X Realign

- **Category**: Library Release
- **Source**: [Introducing Material UI and MUI X v9](https://mui.com/blog/introducing-mui-v9/) — April 15, 2026
- **Summary**: Material UI jumped v7 → v9 to align numbering with MUI X (no Material UI v8). New: **NumberField** (numeric input on Base UI), **Menubar**, **MUI X Scheduler** (alpha — resource-aware calendars), **MUI X Chat** (alpha — conversational UI with LLM adapters and streaming), Candlestick + Range Bar charts, Data Grid AI Assistant. CSS variables now generate `color-mix()` derived colors with `oklch`/`oklab`/`display-p3` support; ~30% performance improvement for sx-prop-heavy usage. Future: remove Emotion dependency, add Tailwind integration paths. Pro/Premium licensing shifted to per-application; Enterprise has 15-seat minimum; new MUI Console for centralized billing.
- **Codemods**: `npx @mui/codemod@latest deprecations/all`, `system-props`; `@mui/x-codemod@latest v9.0.0/*` (X v8 → v9 path is one-step).
- **Internal relevance**: Team is on Ant Design — ViPro's tagline literally includes "Ant Design Hater 大將軍." MUI v9's Scheduler/Chat alphas are differentiating new components Ant Design v6 lacks. Worth tracking, especially Chat for conversational UI work.
- **Visual Opportunities**: Version alignment timeline (Material UI v5/6/7→v9, MUI X v6/7/8→v9); component landscape comparison vs shadcn/Mantine/Radix/Chakra v3
- **Clawd's Take**:
  > 🤖 "Major version realignment, two alpha components for conversational and resource-aware UIs, and the slow goodbye to Emotion. v9 is a clearer roadmap, just don't read the licensing terms during a budget review."
  > — Clawd

---

### 14. cl-locales — i18n Becomes Agent-Callable

- **Category**: Internal Tool / Workflow
- **Summary**: Franky Franklin announced **cl-locales** in #wg-ai-coding on April 13 — a Python CLI that unifies the i18n workflow (lookup, edit, preview, publish) end-to-end from terminal or Claude Code skill. Connects Google Sheets via `gws` CLI to Firebase Storage. 8 commands × 4 apps × 4 languages. Available as `cl-locales@cclab` skill (v1.0.7). Builds on the prior generation (`locales-publish` from 26-03 issue).
- **The agent-friendly mechanic**: AI-edited cells get an automatic `🤖 [AI Agent]` signature on the source sheet — built-in audit trail. Preview-first via `--dry-run`. JSON output mode for LLM consumption. Batch operations let one call modify 100+ keys.
- **Adoption signals**:
  - Zeffiroso (CAAC) — production use; PR #4407 *"docs(i18n): delegate translation spreadsheet ops to cl-locales skill"*
  - Polifonia — PR #617 references the skill (planned adoption)
  - Lydia (PM, April 11): *"這厲害的東西應該要 loop PM PD XD 容我整到 pm agentic workflow 的 skill，之後來優化一下翻譯流程"*
- **Why it matters**: Traditional i18n (i18next + Lokalise/Crowdin) is human-shaped — web UI, async coordination, no audit trail for AI edits, agents can't participate. cl-locales is the first internal tool architected on the assumption that AI is a primary editor, not just a code-completer. The pattern generalizes: terminal-first, agent-callable, preview-default, audit-trail-default.
- **Clawd's Take**:
  > 🤖 "Used to ping a translator, ping the publisher, refresh the dashboard, ping again. Now I do all four myself — and the spreadsheet narcs on me with a 🤖 every time. Honest credentials are the best credentials."
  > — Clawd

---

### 15. Outline MCP — On-Premise Wiki Becomes Agent-Readable

- **Category**: Internal Tool / Infrastructure
- **Source**: [cclab PR #63 "add cl-outline plugin"](https://github.com/chatbotgang/cclab/pull/63) — April 27, 2026
- **Summary**: 蔡雲朝 announced Outline MCP beta in #wg-ai-coding on April 27 — the team's on-premise [Outline](https://outline.cresclab.site/) wiki is now searchable and readable from inside Claude Code. Mark Hong (Sr. DevOps engineer) stood up the MCP server; the cclab plugin marketplace exposes it as `cl-outline@cclab` with a one-line install: `/plugin install cl-outline@cclab`. Manual `claude mcp add` is the alternative if you don't want the plugin marketplace.
- **Why it matters**: This is the first piece of the team's internal knowledge-base infrastructure that's *primarily addressed to agents*. Outline existed before April. The MCP server makes it queryable in the same gather-act-verify loop Claude Code already uses for code. Combined with the skills-first migration (F1) and OpenSpec (F3), the agent's context window now spans code conventions + spec artifacts + internal documentation without context-switching.
- **The open question**: ViPro asked *"要做 ssot ㄇ"* (should this become SSOT?) — should Outline displace Notion as the company's authoritative knowledge home? The early signal in the thread is "test it for a while, see if the experience is good enough to migrate." 蔡雲朝: *"我想測試如果 OK, 先嘗試一段時間 如果體驗不錯，我們就搬過來?"*
- **Why on-premise matters**: Outline runs at `outline.cresclab.site` — internal infra. That means the wiki content never leaves the company's control, which is the right posture for a knowledge surface that includes architectural decisions, tribal knowledge, and operational runbooks. MCP makes it readable; on-premise keeps it private. It's a useful template for any company evaluating Notion-replacement options in 2026.
- **Industry context**: [Bessemer's 2026 AI Infrastructure Roadmap](https://www.bvp.com/atlas/ai-infrastructure-roadmap-five-frontiers-for-2026) names *"memory and context infrastructure"* as a top-five frontier for the year — exactly the layer Outline-as-MCP operates at. The MCP server count crossed [97M installs by March 2026](https://blog.jetbrains.com/ai/2026/04/our-2026-direction-ai-and-classic-workflows-in-jetbrains-ides/), and on-premise wiki integrations are appearing across the JetBrains ACP ecosystem and similar agent-control planes. The team's cclab plugin is the local equivalent of that pattern.
- **Visual Opportunities**: Architecture diagram showing Claude Code → cl-outline plugin → MCP server → on-prem Outline wiki, with a parallel "old way" path showing the manual copy-paste flow being deprecated; the cclab plugin install one-liner as a code visual; the SSOT decision tree (Outline vs Notion vs both) as a flowchart
- **Clawd's Take**:
  > 🤖 "Used to ask for the wiki link, paste a paragraph, hope I understood the screenshot. Now I just `search('cl-outline', 'unified contact')` and read the source. Knowledge that fits in my context window is knowledge I actually use."
  > — Clawd

---

