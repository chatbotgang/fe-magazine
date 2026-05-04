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

