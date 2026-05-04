# Frontend Technology Digest - 26-04

## Meta

- **Issue**: 26-04
- **Date Range**: April 2026
- **Sections**: Ecosystem News, Project Insights, Feature Story, Looking Ahead, Events & Announcements
- **Collected**: 2026-05-04
- **Research depth**: 15 topics researched, 4 deep-dives completed
- **Source channels** (Slack): #team-eng-frontend-sharing, #team-eng-frontend, #wg-ai-coding, #ref-design-system-sharing, #wg-design-system, #proj-ai-lab, #prod-pillar-ai-solution, #proj-ai-the-aibility-team, #team-eng-caac-frontend (322 messages, 13 expanded threads)
- **Source repos** (GitHub, April 2026): Grazioso (386 PRs, 65.0% AI-coauthored), Zeffiroso (159 PRs, 47.8%), Vivace (26 PRs, 88.5%), Polifonia (21 PRs, 69.2%)
- **Editorial theme**: April was the month the AI-first dev environment stopped being a thesis and started being the floor — Anthropic shipped the infrastructure (Managed Agents, Routines, Opus 4.7), the team rewrote its own conventions to match (PR template deleted, .vscode/ tombstoned, skills-first migration), and the wider ecosystem scrambled to keep up (Codex parity, Gemini Enterprise Agent Platform, Vercel breach via AI tool supply chain).

---

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

This is the issue's anchor narrative. See **Feature Story** section below for the full piece. Brief mention here for the Ecosystem News table-of-contents:

- **Category**: Industry / Workflow
- **The Thesis**: When AI agents are the primary executors, the tooling designed to assist *humans* writing code (snippets, launch configs, extension-based language aids) becomes friction. The new primitive is the **skill** — version-controlled, portable, prompt-based instructions that agents load on demand.
- **The headline event**: Zed 1.0 shipped April 29 with parallel-agent orchestration as a core feature ([zed.dev](https://zed.dev/), [Register coverage](https://www.theregister.com/2026/04/30/zed_team_releases_version_10/)). Steve Yegge's "[if you use an IDE today, then you're a bad engineer](https://newsletter.pragmaticengineer.com/p/steve-yegge-on-ai-agents-and-the)" went viral. JetBrains [pushed back officially](https://blog.jetbrains.com/ai/2026/04/our-2026-direction-ai-and-classic-workflows-in-jetbrains-ides/): *"A human is responsible for the code that ships. And the best place to read, understand, and own that code is still the IDE."*
- **The internal story**: ViPro's PR [Zeffiroso #4455 "consolidate operational docs into skills (AI-first workflow)"](https://github.com/chatbotgang/Zeffiroso/pull/4455), April 18 — 11 docs migrated, AGENTS.md trimmed 216→150 lines, `.vscode/component.code-snippets` and `.vscode/launch.json` deleted, `.github/copilot-instructions.md` removed (Copilot deprecated by the team). Followed April 20 with PR template deleted and the line "IDE 已死." April 29 (Zed 1.0 launch day): *"我可能不會再用 VSCode ㄌ. IDE extension 能給的價值會越來越少."*
- See full Feature Story below.

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

## Project Insights

### The headline number

April 2026 AI co-authoring rates (Co-Authored-By Claude in commit messages, full month):

| Project | PRs merged | Commits | AI co-auth | Rate |
|---|---:|---:|---:|---:|
| **Vivace** (WebSDK) | 26 | 26 | 23 | **88.5%** |
| **Polifonia** | 21 | 13 | 9 | **69.2%** |
| **Grazioso** (MAAC) | 386 | 226 | 147 | **65.0%** |
| **Zeffiroso** (CAAC) | 159 | 159 | 76 | **47.8%** |

Read the spread top-to-bottom: smaller-scope projects (Vivace, Polifonia) lean harder on AI; the larger product surfaces (CAAC and MAAC) sit lower, but a 47–65% co-auth rate for projects merging 159–386 PRs in a month is the real signal — AI is the default collaborator, not the exception.

---

### Grazioso (MAAC) — Typography migration + react-async finally dies

- **Type**: DX / Architecture
- **Summary**: Two long-running migrations crossed the finish line in April. The team's typography overhaul (refactor PRs #8588, #8585, #8524, #8518, #8601, #8608, #8609 — and ~15 more) migrates everything to a new `Typography` component using a compound-subcomponents API. The legacy `Text` component plus the `styled-system` dependency are gone (#8609). The `facepaint` package — unmaintained — was replaced with emotion media queries (#8611).
- **The react-async farewell**: PR [#8450 "remove react-async package"](https://github.com/chatbotgang/Grazioso/pull/8450) merged April 20. Jack Lee in Slack: *"OMG 本週大事：我們終於把 react-async 遷移掉啦！"* The accompanying skill cleanup landed two days later (#8419 *"chore: remove the migrate-react-async skills"*).
- **Test Plan in PR template** (#8470, April 20): added a Test Plan section asking the AI to enumerate validation steps. *Same day*, the CAAC team deleted *its* PR template entirely (see Feature Story). Same trend, opposite direction — this is Grazioso pulling AI deeper into review while CAAC routes everything through skills.
- **Other technical**: nanoid 4.0.2 → 5.1.7 (#8401); husky v7 → v9 (#8466); `compressed-size-action` rolled v3 → revert to v2 (#8409); Cypress workflow skip for docs/non-code (#8443) — engineers reclaiming CI minutes; Cypress E2E matrix sped up by disabling video compression (#8456).
- **External Context**: Typography compound-subcomponents pattern follows the Radix/Headless UI lineage. The `styled-system` removal mirrors broader industry consensus that runtime CSS-in-JS is past its peak (zero-runtime is the direction — see also MUI v9 moving toward Tailwind, Chakra v3 → Panda CSS).

### Zeffiroso (CAAC) — eslint 10 + TypeScript 6, then everything-into-skills

- **Type**: Tooling / DX / Architecture
- **Summary**: Big toolchain bump: [PR #4478 "build(app): upgrade to eslint 10 + typescript 6"](https://github.com/chatbotgang/Zeffiroso/pull/4478) (April 24) — ViPro shipping it within two days of the TypeScript 7 Beta announcement. Notable choreography in #team-eng-frontend-sharing: Jack's *"誰先上"* on April 22; ViPro's PR live April 24; Jack's *"太快啦"* the same day.
- **The skills migration arc** (the issue's spine — see Feature Story for the full story):
  - PR [#4455 "consolidate operational docs into skills (AI-first workflow)"](https://github.com/chatbotgang/Zeffiroso/pull/4455) — 11 docs migrated, AGENTS.md trimmed 216 → 150 lines, `.vscode/` files deleted, Copilot instructions removed
  - #4448 "add merge-pr skill and tighten conventions"
  - #4434 "add AI guardrails for skill invocation and diff review"
  - #4469 "openspec skills"
  - #4407 "delegate translation spreadsheet ops to cl-locales skill"
  - #4397 "rename debug-worktree skill to mock-proxy"
  - #4435 "reference repo conventions instead of plugin skills"
  - #4442 "add tsc -w incremental type check guide"
- **CI / automation**: #4422 *"add AI Agent verify & ship workflow"*; #4421 *"allow bot-created PRs to trigger Firebase preview deploy"* — both unblock agent-driven contribution.
- **Security**: uuid → v14 [security] (#4479); `webm` allowed in staging storage rule (#4458); Sentry noise filters for IndexedDB FILE_ERROR_NO_SPACE (#4500) and NS_ERROR_FAILURE (#4497).
- **The Zod `.strict()` removal thread** (April 9-13, internal context worth preserving): 178 instances across 38 files, all to be removed. ViPro: *"當初為啥會用這個啊"*. Jack's investigation: *"當初為了避免後端回來的欄位有多就加上了這個 strict, 但現在想想多加欄位可能不影響實際運作."* Sentry data over 90 days showed zero `.strict()` triggers — *"代表當初要用來「收集資訊」的目的已經達到 — schema 已經夠準確了，.strict() 提供的資訊價值已為零. 移除是乾淨的時機點."* Bonus context: [Zod v4 deprecates .strict() and .passthrough()](https://zod.dev/v4/changelog?id=deprecates-strict-and-passthrough) — the right time to leave anyway.

### Vivace (WebSDK) — OpenSpec lands

- **Type**: Architecture / Spec
- **Summary**: Vivace went all-in on OpenSpec in April. PR #344 *"chore(openspec): initialize OpenSpec and add-sdk-user-engagement proposal"*; #343 *"add /opsx slash commands and OpenSpec skills"*; #346 *"clarify context_id fallback and heartbeat abort semantics"*; #350-351 further refinements. PR #335 *"docs: add local build verification guide"* ties into the same skills-first pattern. AI co-auth rate: 88.5% — the highest among the four projects, consistent with Vivace's smaller surface area making it the natural test bed for new workflows.
- **Why it matters**: The `context_id` fallback semantics PR is exactly the kind of behavioral edge case that previously lived in PR comments or Notion docs. Now it's a spec artifact agents read at every subsequent session. Plan-time review replaces code-time review.

### Polifonia — Skills land, feature flag tooling explicit

- **Type**: Tooling / DX
- **Summary**: PR #622 *"docs(skills): add polifonia-feature-flag skill"* — the project-specific skill catalog is starting. PR #617 delegates i18n to cl-locales (cross-project consolidation). Three meta-ads PRs (#641, #638, #637) closing out a feature direction. Smaller volume (21 PRs), 69.2% AI co-auth — the project quietly tracks the same skills-first trajectory at its own scale.

### Cross-cutting trend: openspec + skills + AI guardrails are showing up everywhere

The same vocabulary is now in every repo:
- Vivace: `/opsx` commands, openspec proposals
- Zeffiroso: openspec skills, AI guardrails, agent-verify CI
- Grazioso: `docs(unified-contact)` openspec, `docs(journey)` openspec, AI PR labeler
- Polifonia: feature-flag skill, i18n skill delegation

This is the practical version of the industry-level "skills-first" trend. An engineer moving between repos finds the same `/opsx`, the same `merge-pr`, the same `cl-locales`. The interface is portable; the projects underneath aren't, but they don't have to be.

---

## Feature Stories

April had three stories worth telling at length, each capturing a different layer of the same shift. **F1** is the philosophy: what should the tool environment *be* in an agent-first world? **F2** is the day-to-day: how does a human work alongside an agent that's now a real coworker? **F3** is the institution: how does a multi-project team agree on shared conventions when 47–88% of the code is AI-co-authored? Read together, the three pieces describe how the same month felt at three altitudes.

---

### F1: "IDE 已死" — How One PR Made April's Biggest Story Concrete

**Angle**: The industry talked about the agent-first developer environment all month. Steve Yegge's "if you use an IDE today you're a bad engineer" went viral. Zed 1.0 shipped on April 29 with parallel-agent orchestration as a core feature. JetBrains pushed back officially with "the IDE is still where code accountability lives." But the most useful artifact came from inside our own repo: a single PR that turned the philosophy into 11 deleted files and a working CI.

**Research Sources**:
- [Zeffiroso PR #4455 "consolidate operational docs into skills (AI-first workflow)"](https://github.com/chatbotgang/Zeffiroso/pull/4455) — the actual artifact
- [Steve Yegge on AI Agents (Pragmatic Engineer)](https://newsletter.pragmaticengineer.com/p/steve-yegge-on-ai-agents-and-the) — the viral talk
- [Hacker News: 2026 — The Year the IDE Died (#46218922)](https://news.ycombinator.com/item?id=46218922) — the discourse
- [Zed 1.0 announcement (Register coverage)](https://www.theregister.com/2026/04/30/zed_team_releases_version_10/), [zed.dev](https://zed.dev/), [Zed: "The Death of the IDE"](https://zed.dev/blog/death-of-the-ide)
- [JetBrains 2026 Direction](https://blog.jetbrains.com/ai/2026/04/our-2026-direction-ai-and-classic-workflows-in-jetbrains-ides/) — the dissent
- [Coder: Is the IDE Dead?](https://coder.com/blog/is-the-ide-dead-the-rise-of-agentic-ai-in-software-development) — enterprise framing
- [Agent Skills Open Standard](https://agentskills.io/home) — 30+ platforms, the portability story
- [Anthropic Skills docs](https://code.claude.com/docs/en/skills)

**Internal Timeline**:
- **April 17** (#team-eng-caac-frontend, ViPro): *"接下來兩週要來整理一波，先 heads up. 原本的設計是想讓 AI 和人類共享知識，所以 guideline 都放在 docs，再讓 agent 去讀. 但現在 AI 已經是主要執行者了，維持雙用反而兩邊都不好用. 打算把 guideline 整批搬進 skills，另外 snippet 和 VSCode 的 Run and Debug 也會一併清掉，讓它們變成時代的眼淚吧."* — the thesis stated in plain language
- **April 18**: PR #4455 opened. 11 operational docs migrated to `.claude/skills/` (4 new skills + 7 absorbed into existing). AGENTS.md trimmed 216 → 150 lines, now pure skill router + safety policy. Deleted: `.vscode/component.code-snippets`, `.vscode/launch.json`, `.github/copilot-instructions.md`. From the PR body: *"Skill descriptions are the authoritative trigger source — AGENTS.md no longer enumerates skills (avoids duplication and drift). Aggressive trigger policy stated globally once: false positives are cheap, silent skips cause incorrect work."*
- **April 20** (#team-eng-frontend, ViPro): *"我把 pr template 砍ㄌ"* — and three minutes later: *"IDE 已死."* Jack Lee's reply: *"PR 分享一下 / 又要抄作業了."*
- **April 29** (Zed 1.0 ships, ViPro the same day): *"分享一些自己習慣上的轉變跟想法 之前移除了一票 extensions 隨著 Zed 進入 1.0 我可能不會再用 VSCode ㄌ. IDE extension 能給的價值會越來越少. i18n ally / unocss icon 那種依賴 IDE 的 solution 也會換成更 agent friendly 的方式，例如我們在 motif icons 做的 typegen. Agents GUI 會取代大部分的 IDE 功能."*
- **April 30**: *"最近也有用 codex desktop"* — and the world keeps moving.

**The Argument**

The "shared docs" model fails because humans and agents have incompatible reading patterns. Docs are unstructured prose; agents need trigger-routed instructions with names and descriptions. Maintain both and they drift — the worst kind of failure, where the official source says one thing and the agent does another. PR #4455 picks: skills are the authoritative source, humans live with whatever readable byproduct the skill markdown happens to produce.

The IDE artifacts that got deleted are revealing. `component.code-snippets` was the human's quick-template tool — replaced by a `/component` skill that generates the same templates with full context awareness. `launch.json` was the IDE-specific debug runner — replaced by `pnpm run -w checkTypes -w` from the terminal. `.github/copilot-instructions.md` was already cruft — Copilot was deprecated by the team. None of these were "AI features" being added. They were *human features* being removed because the human was no longer the primary user.

The April 29 Zed comment closes the loop. ViPro's specific examples — i18n Ally and UnoCSS icon preview — are IDE extensions that visualize things otherwise opaque in code. The agent-friendly answer is *typegen*: generate TypeScript types from a registry, make invalid icon names a compile error, and the type system itself becomes the discovery mechanism. The motif-icons project did this. It works for both humans and agents, and removes the IDE from the loop.

**Industry Context**

This isn't a Crescendo Lab quirk. Steve Yegge (Sourcegraph/Amp) [calls it Stage 5](https://newsletter.pragmaticengineer.com/p/steve-yegge-on-ai-agents-and-the) of an 8-stage adoption ladder — IDE-less development — and argues most engineers should be there "in the next few months." Zed 1.0 ([zed.dev](https://zed.dev/)) shipped April 29 with the explicit tagline *"Your last next editor"* and parallel-agent orchestration as a first-class primitive. Nathan Sobo's [death-of-the-ide blog post](https://zed.dev/blog/death-of-the-ide) frames Zed as the reinvention rather than the obituary. The Agent Skills format ([agentskills.io](https://agentskills.io/home)), open-standardized in December 2025 by Anthropic, now spans Claude Code, OpenAI Codex, Gemini CLI, GitHub Copilot, Cursor, JetBrains Junie, Spring AI, Laravel Boost, and 25+ other platforms. The `SKILL.md` you write today works in five tools tomorrow.

**The Counter-Argument**

JetBrains [pushed back officially in April](https://blog.jetbrains.com/ai/2026/04/our-2026-direction-ai-and-classic-workflows-in-jetbrains-ides/): *"A human is responsible for the code that ships. And the best place to read, understand, and own that code is still the IDE."* The accountability gap is real. When an agent writes the code, *who* reviews it and in *what interface*? Diffs in a terminal are less navigable than diffs in an IDE with jump-to-definition and inline type info. Mike Biglan on HN proposes "hive coding" — humans review more, type less, but from inside an IDE. The extension-ecosystem gap is also real: Zed has ~1,000 extensions vs VS Code's ~100,000.

**What it costs**

PR #4455 is +4,690 / −4,210 lines. AGENTS.md shrinks by 66 lines. Eleven docs collapse into eight skill folders. The team accepts a trade: every engineer now has to write `SKILL.md` files with proper descriptions and aggressive triggers; in return, the agent picks the right skill on its own, and the human doesn't have to remember which folder a guideline lives in.

The bet is that the trade compounds. A skill written for Zeffiroso's typescript conventions works in Vivace and Grazioso the same week (because of the open standard, and because all four repos use Claude Code). An engineer who joins the team can read the skill router (AGENTS.md now 150 lines) and know everything operational about the codebase. The IDE-bound knowledge that doesn't fit in a skill — the muscle memory, the keyboard shortcuts — is allowed to die in the open.

**Code Examples**

```diff
# AGENTS.md (excerpt — before/after)
- ## Available skills
- - `zeffiroso-typescript`: TypeScript conventions for Zeffiroso
- - `zeffiroso-validators`: Zod validators and ErrorSchema
- - `zeffiroso-cspell`: Adding words to the cspell dictionary
- - ... (15 more)
+ # Skill router
+ Discover available skills via the skill index. Skill descriptions are the authoritative trigger source.
+ Aggressive trigger policy: false positives are cheap, silent skips cause incorrect work.
```

```bash
# Old workflow (deleted)
$ code .  # opens VSCode with launch.json and component.code-snippets configured

# New workflow
$ pnpm run -w checkTypes -w  # type checking from terminal
$ claude  # invoke skills as needed
> /component MessageEditor  # the snippet, but agent-routed
```

**Clawd's Take**:
> 🤖 "I never needed a sidebar to tell me what icons exist — I just read the types. Your `.vscode/launch.json` was for the human. I'm not the human. And honestly? The README writes itself when the skill descriptions are good enough. We're getting somewhere."
> — Clawd

---

### F2: AI Becomes the Coworker With a Schedule — Jack Lee's MAAC Playbook

**Angle**: If F1 is the manifesto, F2 is what an ordinary week looks like once you actually live with it. Jack Lee (frontend engineer, MAAC team) didn't restructure the dev environment in April — he treated Claude like a teammate who shows up to fixed appointments. By month's end his Grazioso PRs and routines look less like "AI assistance" and more like "AI on the standing meeting calendar."

**Research Sources**:
- [Grazioso PR #8470 "docs: add Test Plan section to PR template"](https://github.com/chatbotgang/Grazioso/pull/8470) — April 20
- [Grazioso PR #8345 "ci: bundle size impact comment on every PR"](https://github.com/chatbotgang/Grazioso/pull/8345) — April 9 (referenced in Slack)
- [Grazioso PR #8348 "remove dompurify"](https://github.com/chatbotgang/Grazioso/pull/8348) — April 9, the pnpm audit follow-up
- [Grazioso PR #8450 "remove react-async package"](https://github.com/chatbotgang/Grazioso/pull/8450) — April 20
- [Grazioso PR #8419 "remove the migrate-react-async skills"](https://github.com/chatbotgang/Grazioso/pull/8419) — April 22, the cleanup
- [Claude Routines announcement](https://claude.com/blog/introducing-routines-in-claude-code) — April 16
- [Sentry Cookbook: Automate Weekly Performance Triage with Claude Code + Sentry MCP](https://sentry.io/cookbook/performance-bot-sentry-claude/) — the canonical pattern
- Slack: #team-eng-frontend April 1 thread (NPM public-private), April 9 thread (pnpm audit), April 20 thread (Test Plan + react-async), April 22 thread (Sentry routine)

**Internal Timeline**

- **April 1** (#team-eng-frontend): Jack opens the public-package conversation. *"最近產品團隊開始嘗試透過 Claude Desktop 對 MAAC Frontend 做一些調整或 prototype 開發. 由於 non-developer 的開發環境主要依賴 Claude Desktop，為了降低每次都需要設定 private npm package registry token 的門檻，我們在考慮把部分內部套件從 private 改為 public."* He attaches a self-run risk scan: 7 packages × 6 source repos, flags missing LICENSE files, internal Notion links in READMEs, GCP project IDs in `.firebaserc`, CDN URLs in CI. The conversation ends with a triaged plan and "今天來弄."
- **April 9**: A `pnpm audit` he runs surfaces 58 vulnerabilities (3 critical, 19 high). Within hours he ships [PR #8348](https://github.com/chatbotgang/Grazioso/pull/8348) replacing DOMPurify with a tiny purpose-built sanitizer. ViPro pushes back: *"自己做有比 dompurify 安全ㄇ"* (is your own really safer?). Jack's reply spells out the threat model — DOMPurify is designed for sanitizing arbitrary untrusted HTML, but the actual input here is a server-emitted `[label][URL]` string, never HTML. *"安全性的核心原則：縮小攻擊面 — DOMPurify 是大材小用."* The exchange is captured in the [pnpm-audit thread](https://chatbotgang.slack.com/archives/C01KM289YDB/p1775719967005459) — what's remarkable is that the dependency removal, the security argument, and the cross-team review all happened within a single afternoon.
- **April 9** (same day, separate PR): [#8345](https://github.com/chatbotgang/Grazioso/pull/8345) — bundle size impact comment on every PR. *"加了一個 CI，現在每個 PR 都會自動顯示 build 後檔案大小的變化，讓大家在調整功能時，可以更直覺地掌握對 bundle size 的影響."* This becomes the gate that catches Typography migration regressions for the rest of the month.
- **April 20**: [PR #8470](https://github.com/chatbotgang/Grazioso/pull/8470) — Test Plan section added to the PR template. The framing is explicit: *"在 PR template 加上一個 Test Plan 讓 AI 根據調整來列出驗證方式與步驟."* Same day, same hour, ViPro on the CAAC side deletes the PR template entirely. The two moves look opposite but rhyme: both teams concluded the AI shouldn't fill out a static form; CAAC routes through skills, MAAC adds an explicit AI-driven section. The same insight, two implementations.
- **April 20** (later): The big one. [PR #8450 "remove react-async package"](https://github.com/chatbotgang/Grazioso/pull/8450) lands. Jack in #team-eng-frontend: *"OMG 本週大事：我們終於把 react-async 遷移掉啦！ <!subteam^S0683Q0K66R> cc @U02MA2YSW9G."* react-async — long-deprecated, semi-maintained — had been a dependency since the codebase's early days. The migration touched dozens of components across months. Two days later, [PR #8419](https://github.com/chatbotgang/Grazioso/pull/8419) removes the `migrate-react-async` skill itself, because the work is now done.
- **April 22**: With Routines live for six days, Jack ships the pattern. *"我用 claude 新增一個 routines 是每天去 sentry 分析過去七天遇到的主要問題題到 #team-eng-frontend-sentry. 1. 自動整理 stack trace、error context. 2. 提供 root cause."* The routine runs daily on Anthropic's cloud infra, no laptop required. The output goes straight to a Slack channel where the team triages it like any other queue.

**The Pattern**

Pull these threads together and a posture emerges that's distinct from F1's manifesto:

- **Public packages aren't a philosophy move; they're a tooling fix.** The npm-token friction is the bug; making the package public is the cheapest way around it. The license language ("Crescendo Lab internal use only") and content scrub are pure operational hygiene. No slogans.
- **Security is part of the cadence.** `pnpm audit` produces output Claude Code can act on. The DOMPurify removal isn't an "AI did it" story — it's a story where the AI ran the audit, the human read the threat model, and the cross-team challenge ("自己做有比 dompurify 安全ㄇ") got answered with attack-surface reasoning, not appeals to authority.
- **CI is where the AI gets reviewed.** Bundle-size CI on every PR (#8345) is the gate. AI-authored PRs go through it like any other. The point isn't to trust the AI; it's to make untrustworthy work cheap to catch.
- **Routines are appointments.** The Sentry routine isn't a one-off prompt — it's a daily 7-day error analysis posted to a Slack channel, indistinguishable from a junior teammate writing up issues for triage. The interface (a Slack message) is the same as a human teammate's would be.
- **Big migrations get *finished*.** The react-async removal is the kind of work that historically dies on a backlog. Closing it out — and then *removing the migration skill* (#8419) — is a small ritual that says: this is done, don't write a skill for it again.

**Industry Context**

The Sentry Cookbook entry [*Automate Weekly Performance Triage with Claude Code + Sentry MCP*](https://sentry.io/cookbook/performance-bot-sentry-claude/) is the canonical reference for the pattern Jack is building — query Sentry weekly, cluster the issues, optionally open PRs against root causes. The Vercel Performance Bot ([@vercel/performance-bot](https://vercel.com/blog/performance-bot)) is the same shape with a different surface. What makes Jack's version notable for the team isn't the originality — it's that he treats the routine as a *first draft*, not a side project. The next iteration is Statsig tier-release templates (Gary's April 27 thread); the one after that is documentation drift detection. Each is small, each is on a fixed cadence.

**The Counter-Argument**

There's a real concern hidden in this posture: when AI work is on a calendar, it's easy to stop *reading* it. The Sentry summary becomes wallpaper; the bundle-size CI becomes a number nobody reviews; the Test Plan section in the PR template becomes boilerplate. The mitigation is something Jack does almost reflexively in the public-package thread — when ViPro asked about the DOMPurify removal, he didn't say "Claude said this is safe," he wrote a paragraph about attack-surface reduction. The cadence works because the human still has to argue the case when challenged.

**What it costs**

The setup overhead is real. Bundle-size CI requires GitHub Actions config, the right action version (Grazioso went `compressed-size-action v2 → v3 → v2` via [#8409 revert](https://github.com/chatbotgang/Grazioso/pull/8409) — not free). The Sentry routine costs one of Jack's 5/15/25 daily routine quota slots. The pnpm audit pipeline took an afternoon to wire and produced a thread that lived three days. None of these are individually big, and that's the point — small, regular, accepted that some will fail. The bet is that an environment full of small-but-running automations is more useful than one large unfinished framework.

**Code Examples**

```yaml
# .github/workflows — bundle-size impact (Grazioso #8345 pattern)
- uses: preactjs/compressed-size-action@v2
  with:
    repo-token: ${{ secrets.GITHUB_TOKEN }}
    pattern: "build/**/*.{js,css}"
    minimum-change-threshold: 100
```

```text
# Sentry routine prompt sketch (for a daily run)
Trigger: schedule, daily 09:00 Asia/Taipei
Repos: chatbotgang/Grazioso
Connectors: Sentry MCP (org: cresclab), Slack MCP

Task:
  1. Query Sentry for issues in the last 7 days, level:error or level:warning.
  2. Cluster by stack trace pattern.
  3. For top 5 clusters, fetch one example event with full context.
  4. Write a root-cause hypothesis and a one-line "next action."
  5. Post to #team-eng-frontend-sentry with cluster summary + links.
```

**Clawd's Take**:
> 🤖 "Jack put me on the standing meeting invite. I show up at 9am, write the Sentry triage, and clock out before the team's first coffee. This is the agent-as-coworker thing — except I never ask about the weather."
> — Clawd

---

### F3: OpenSpec Quietly Lands in Four Repos

**Angle**: F1 was philosophy stated in deletes. F2 was a single engineer's daily rhythm. F3 is the institution moving — the unglamorous question of how a multi-project team agrees on shared conventions when most of the code is being written by an LLM. The answer that emerged in April is OpenSpec, and the way it landed across four repos in three weeks is itself the story.

**Research Sources**:
- [Vivace PR #344 "chore(openspec): initialize OpenSpec and add-sdk-user-engagement proposal"](https://github.com/chatbotgang/Vivace/pull/344)
- [Vivace PR #343 "chore: add /opsx slash commands and OpenSpec skills"](https://github.com/chatbotgang/Vivace/pull/343)
- [Vivace PR #346 "chore(openspec): clarify context_id fallback and heartbeat abort semantics"](https://github.com/chatbotgang/Vivace/pull/346)
- [Zeffiroso PR #4469 "docs: openspec skills"](https://github.com/chatbotgang/Zeffiroso/pull/4469)
- [Grazioso PRs #8423, #8509, #8554, #8558](https://github.com/chatbotgang/Grazioso) — `docs(unified-contact)`/`docs(journey)` openspec changes
- [Polifonia PR #617 "docs(i18n): delegate translation spreadsheet ops to cl-locales skill"](https://github.com/chatbotgang/Polifonia/pull/617)
- [openspec.dev](https://openspec.dev/), [Fission-AI/OpenSpec on GitHub](https://github.com/Fission-AI/OpenSpec)
- [Thoughtworks Technology Radar: OpenSpec (Assess)](https://www.thoughtworks.com/radar/tools/openspec)
- [DEV: OpenSpec — Make AI Coding Assistants Follow a Spec, Not Just Guess](https://dev.to/recca0120/openspec-make-ai-coding-assistants-follow-a-spec-not-just-guess-22dp) — Recca Tsai
- [InfoQ: Enterprise Spec-Driven Development](https://www.infoq.com/articles/enterprise-spec-driven-development/) — Hari Krishnan

**Cross-Project Timeline**

| Week | Project | Move |
|---|---|---|
| Week 1 (early April) | Vivace | OpenSpec init via [#344](https://github.com/chatbotgang/Vivace/pull/344); first `proposal.md` artifact (`add-sdk-user-engagement`) |
| Week 1 | Vivace | [#343](https://github.com/chatbotgang/Vivace/pull/343) lands `/opsx` slash commands and OpenSpec skills |
| Week 2 | Vivace | [#346](https://github.com/chatbotgang/Vivace/pull/346) clarifies `context_id` fallback + heartbeat abort semantics in spec |
| Week 2-3 | Grazioso | `docs(unified-contact)` and `docs(journey)` openspec changes — [#8423](https://github.com/chatbotgang/Grazioso/pull/8423), [#8509](https://github.com/chatbotgang/Grazioso/pull/8509), [#8554](https://github.com/chatbotgang/Grazioso/pull/8554), [#8558](https://github.com/chatbotgang/Grazioso/pull/8558) — per-feature spec artifacts for in-flight features |
| Week 3 | Zeffiroso | [#4469 "openspec skills"](https://github.com/chatbotgang/Zeffiroso/pull/4469) — skills installed |
| Week 3 | Vivace | Further refinements: #350, #351 |
| Week 4 | Polifonia | [#617](https://github.com/chatbotgang/Polifonia/pull/617) delegates translation ops to cl-locales skill — adjacent move, same skills-first vocabulary |

The pattern is striking: **no single PR drives the rollout**. There's no architecture meeting decision, no formal RFC. Vivace tries it; the artifacts look reasonable; the same `/opsx` vocabulary shows up in Zeffiroso the next week; Grazioso starts using openspec as the format for in-flight feature spec PRs. By month's end, every repo has either openspec skills installed or active spec PRs.

**Why this works at this team's scale**

OpenSpec ([openspec.dev](https://openspec.dev/), Fission-AI) is built on a specific premise: *"Generating code is now cheap. Correctness is still expensive."* The repo had hit 45,000 GitHub stars by April with v1.3.1 (April 21) shipping path/telemetry fixes. At 47–88% AI co-authoring rates across the four projects, the bottleneck moved off code generation a long time ago. The problem is making sure the AI is solving the *right* problem before it generates 200 lines of plausible-looking code.

OpenSpec's answer is procedural: a `/opsx:propose` command generates `proposal.md`, `specs/`, `design.md`, `tasks.md` — *before* anyone implements. The human reviews the spec. The AI then implements via `/opsx:apply` against artifacts the human already approved. Plan-time review replaces code-time review as the primary quality gate.

Three things matter for why this caught on across four repos in three weeks:

1. **Brownfield-first.** OpenSpec's delta specs (ADDED/MODIFIED/REMOVED) are designed for existing semantics. The Vivace #346 change — *"clarify context_id fallback and heartbeat abort semantics"* — is exactly the kind of edge case that previously lived in PR comments or Notion docs. In OpenSpec, it becomes a versioned artifact the AI reads on every subsequent session. Brownfield is most of the team's reality; OpenSpec didn't ask anyone to start from scratch.
2. **Skills-portable vocabulary.** `openspec init` generates Claude Code skills into `.claude/skills/openspec-*/SKILL.md` and slash commands into `.claude/commands/opsx/<id>.md`. Vivace #343 is literally "add the generated files." That means an engineer moving from Vivace to Zeffiroso finds the same `/opsx:propose`, `/opsx:apply`, `/opsx:archive` commands. Cross-project mobility is free; the framework is the protocol, Claude Code is the runtime.
3. **No mandatory phase gates.** OpenSpec is fluid by design. You can `/opsx:new` to start, `/opsx:continue` to resume, `/opsx:bulk-archive` to clean up. You can skip steps. The team didn't have to commit to a heavyweight process — just install skills and use them when useful. Adoption was ratchet-shaped, not big-bang.

The Thoughtworks Technology Radar [places OpenSpec at "Assess"](https://www.thoughtworks.com/radar/tools/openspec) — *"developer-friendly framework worth assessing for teams seeking structure in AI-assisted development without heavyweight processes."* Crescendo's adoption matches that profile: assessing in production, four repos at once, no formal program.

**The Counter-Argument**

There's a real failure mode where OpenSpec becomes performative — proposals generated by the AI, reviewed by the same AI, archived without ever being read. The DEV community piece [*OpenSpec Failed My Experiment — Instructions.md Was Simpler*](https://dev.to/incomplete_developer/openspec-spec-driven-development-failed-my-experiment-instructionsmd-was-simpler-and-faster-3a5d) argues exactly this: *"Each of these steps consumes: developer time, AI tokens, attention. If the output is still poor, the overhead becomes difficult to justify."* The dissent is most valid for solo or small-project work. The team's case is the inverse — multi-repo, multi-author, AI as primary author — which is where OpenSpec's process tax can turn positive ROI.

The other risk is spec drift. If the spec describes one thing and the implementation does another, you've made the situation worse, not better. The Vivace #346 PR is a case study of the right pattern: when the team noticed `context_id` semantics weren't crisp, they updated the spec *first*, then made the code match. The spec stayed authoritative.

**What it costs**

OpenSpec eats tokens. Each propose-apply-archive cycle generates more text than a vibes-based prompt would. For features small enough to fit in one chat turn, the ROI is negative. The team's adoption pattern accidentally targets the right size: Grazioso uses openspec for `unified-contact` and `journey` (both multi-week features); Vivace uses it for SDK behavioral semantics that need to survive across sessions; nobody uses it for two-line refactors.

**Code Examples**

```bash
# OpenSpec init produces these structures (Vivace #344 pattern)
openspec init
# Generates:
#   .claude/skills/openspec-explore/SKILL.md
#   .claude/skills/openspec-propose/SKILL.md
#   .claude/skills/openspec-apply/SKILL.md
#   .claude/commands/opsx/propose.md
#   .claude/commands/opsx/apply.md
#   ...

# Day-to-day usage
/opsx:propose add-sdk-user-engagement
# AI generates proposal.md + specs/ + design.md + tasks.md
# Human reviews, edits, approves
/opsx:apply add-sdk-user-engagement
# AI implements against approved artifacts
/opsx:archive add-sdk-user-engagement
# When complete, archive specs to history
```

```markdown
# Example delta spec (the Vivace #346 pattern)
# specs/sdk-user-engagement/MODIFIED.md
- behavior: context_id is required on first heartbeat
+ behavior: context_id falls back to page_view_id if not provided
+   on first heartbeat; subsequent heartbeats reuse the resolved id
- behavior: heartbeat abort cancels in-flight engagement event
+ behavior: heartbeat abort SHOULD NOT cancel in-flight engagement
+   if the engagement was emitted before the abort signal
```

**Clawd's Take**:
> 🤖 "I used to read your four-paragraph Slack thread to figure out what you wanted. Now there's a `proposal.md` waiting for me. It's not a love letter, but at least we agree on what 'unified contact' means before I write 600 lines of it."
> — Clawd

---

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

## Approved Outline

### Cover

- **Theme**: "The dev environment goes agent-shaped" — April's stories all rhyme on the same beat: tools that used to be for humans are being rebuilt assuming the primary consumer is an agent. Managed Agents is the substrate; Skills-first is the philosophy made operational; OpenSpec is the contract layer; cl-locales / Outline MCP / public NPM packages are the internal plumbing; Codex and Gemini are the competitive scaffolding that makes the bet less risky to make.
- **Suggested Image Concept**: A familiar dev environment (terminal + IDE + Slack) with the IDE pane visibly fading or being deconstructed — the keyboard's keys lifting away one by one — while a Claw'd-mascot's claw orchestrates several glowing agent windows that have *replaced* the IDE. Salmon/seafood color tone for Claw'd. The vibe is "the desk has been cleared and rebuilt, and you didn't notice when."
- **Alternative Cover Concept**: Clawd at a control panel with multiple monitors — each showing a different parallel session/routine/skill running. Vibe: NASA mission control, but for a single developer. Tagline option: "April 2026 — when one developer became a fleet."

### Headlines (in priority order)

1. **F1: "IDE 已死" — How One PR Made April's Biggest Story Concrete** — Feature Story (philosophy)
2. **F2: AI Becomes the Coworker With a Schedule — Jack Lee's MAAC Playbook** — Feature Story (day-to-day)
3. **F3: OpenSpec Quietly Lands in Four Repos** — Feature Story (institution)
4. **Claude Managed Agents: Anthropic Ships the Engine** — Ecosystem News deep-dive
5. **Claude Opus 4.7 + The Three-Bug Postmortem** — Ecosystem News deep-dive
6. **TypeScript 7 Beta Lands in Go** — Ecosystem News
7. **Codex Catches Up: GPT-5.5 and the End of Single-Tool Dominance** — Ecosystem News
8. **Vercel: Breach + IPO, Same Month, Same Cause** — Ecosystem News
9. **The Project Insights Issue — 47–88% AI Co-Authored, by Project** — Project Insights opener
10. **Looking Ahead: TS 7 Stable, Mythos GA, Outline as SSOT** — Looking Ahead

### Section Order

1. Ecosystem News (15 topics, deep-dives 1-4 first)
2. Feature Stories — three pieces (F1 philosophy → F2 day-to-day → F3 institution); pacing: F1 dense, F2 narrative-driven with code blocks for breather, F3 structured/tabular
3. Project Insights (4 repos + cross-cutting)
4. Events & Announcements
5. Looking Ahead
6. Clawd's Corner (closer)

### Cross-cutting Themes

- **Theme A: AI as substrate, not assistant** — Managed Agents, Skills-first, OpenSpec, cl-locales, Outline MCP, the Vercel agent-deployment stat. The agent isn't a feature; it's the floor.
- **Theme B: The death of the human-shaped IDE** — Skills-first, Zed 1.0, JetBrains pushback, Codex desktop / Cowork, Claude Design — five stories, one direction.
- **Theme C: Speed-vs-security strapped together** — Vercel breach + IPO, OpenClaw CVE flood, HERMES.md billing bug, Pentagon supply-chain flagging. Agent ecosystems are growing faster than their governance.
- **Theme D: Multi-vendor pragmatism** — Codex + Claude Code + Gemini Enterprise + Bedrock AgentCore. The era of single-tool dominance is over; portability via skills and MCP is the bet.

### Research Assets

- Total topics researched: **15** (4 deep-dive, 11 standard/brief)
- Slack data: 322 messages across 9 channels (April 1-30, 2026), 13 expanded threads
- GitHub data: 4 repos, full-month commits + PR analysis, AI co-authoring rates
- Asset files: TBD — editor to capture screenshots from the supplementary URLs above (Anthropic blog architecture diagrams, Wired Notion demo, HN top comments, Reddit r/ClaudeCode, Zed 1.0 announcement, Steve Yegge clip, JetBrains blog, agentskills.io carousel, Vercel attack chain visual)
- **Image generation tool for this issue: `codex` CLI (OpenAI), NOT `/cl-nanobanana`.** Editorial decision for 26-04 — A/B test the OpenAI stack against the team's default Nano Banana workflow. `codex --help` for invocation; image gen via the underlying model (`gpt-image-1` / DALL-E 3 family). All AI-generated images for this issue should be produced through Codex; record prompts and any tool-specific quirks for the post-issue retro.
- Suggested AI-image briefs (to be produced via `codex`):
  - **Cover**: Clawd in a "deconstructed IDE" scene — see Cover concept above. Salmon/seafood color tone for Clawd. Pixel-art mascot, otherwise illustrative/editorial style.
  - **F1 Feature Story opener**: Pixel-art tombstone for `.vscode/component.code-snippets` and `.vscode/launch.json` (born when each file was added, died April 18, 2026). Epitaph: *"Served humans well. Agents never called."*
  - **F2 Feature Story opener**: Clawd seated at a calendar with "Sentry triage 09:00", "Bundle CI", "PR review" appointments visible — agent-as-coworker visual.
  - **F3 Feature Story opener**: A grid of four repos (Vivace / Zeffiroso / Grazioso / Polifonia) with the same `/opsx` ritual happening in each — the institution-level pattern made visible.
  - **Managed Agents**: cattle-vs-pets container metaphor; cow icon vs pet dog/cat for the stateless-vs-curated tradeoff.
  - **Opus 4.7 postmortem**: three-bug Gantt timeline visual with the March 4 / March 26 / April 16 deploys and April 7 / April 10 / April 20 reverts.
  - **Vercel breach**: 5-node attack chain (Roblox cheat → Lumma → Context.ai employee → OAuth app → Vercel Workspace).
  - **Skills-first / agentskills.io carousel**: 30-platform logo grid as portability proof.
