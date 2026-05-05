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

