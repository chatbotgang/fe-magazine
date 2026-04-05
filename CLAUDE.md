# Frontend Technology Digest

A technical magazine for Crescendo Lab's frontend engineering team.

## Project Structure

```
fe-magazine/
├── .claude/
│   └── commands/
│       ├── magazine.md           # Full workflow (orchestrator)
│       ├── magazine-collect.md   # Content collection (journalist)
│       └── magazine-edit.md      # Editorial design (art director)
├── {YY-MM}/              # Issue folder
│   ├── {YY-MM}.pdf       # PDF (primary output)
│   ├── index.html        # HTML source
│   ├── content.md        # Collected content (handoff between commands)
│   └── assets/           # Issue-specific assets (images, etc.)
├── index.html            # GitHub Pages download page
├── issues.json           # Issue list for GitHub Pages (update this!)
├── README.md             # Project documentation
├── CLAUDE.md             # AI assistant guidelines (this file)
└── robots.txt            # Crawler blocking rules
```

## Important Guidelines

### When Creating a New Issue

**ALWAYS update these files when publishing a new issue:**

1. Create the issue folder: `{YY-MM}/`
2. Verify `content.md` exists (created by `/magazine-collect`)
3. Add `index.html` for the magazine content
4. Add `{YY-MM}.pdf` for the PDF export
5. Add `assets/` folder for issue-specific images
6. Update `README.md` — add new issue to the Issues table (newer issues first)
7. Update `issues.json` — add new issue to the array (newer issues first)

### Issue Naming Convention

- Folder format: `YY-MM/` (e.g., `26-01/` for January 2026)
- The issue ID reflects the END month of the coverage period
- Example: "Dec 2025 - Jan 2026" → `26-01/`

### Magazine Commands

The magazine workflow is split into two commands:

| Command | Role | Output |
|---------|------|--------|
| `/magazine` | Full workflow (orchestrator) | Runs collect then edit |
| `/magazine-collect` | Content collection (journalist) | `{YY-MM}/content.md` |
| `/magazine-edit` | Editorial design (art director) | `{YY-MM}/index.html` + PDF |

**`/magazine-collect`** handles:
1. Collect ecosystem news from Slack and web sources
2. Analyze project insights from GitHub (focus on technical changes, not product features)
3. Research topics in depth with parallel subagents
4. Conduct interviews for feature stories
5. Save structured content to `content.md`

**`/magazine-edit`** handles:
1. Explore design trends and assign per-section visual themes
2. Create pacing plan (opener → dense → breather rhythm)
3. Generate visual assets: AI images (`/cl-nanobanana`), screenshots, custom HTML visuals
4. Produce HTML with `/frontend-design` (each section has distinct visual identity)
5. Review quality with `/web-design-guidelines`, `/pr-review-toolkit`, and `/agent-browser`

Use `/magazine` to run both in sequence within one session.

### Content Guidelines

- **Focus on technical insights** — tooling, architecture, patterns, DX improvements
- **Avoid product feature descriptions** — this is a tech digest, not a product changelog
- **No filler content** — if there's no insight, don't write it
- **Skip statistics** — no PR counts, contributor counts unless meaningful
- **Never fabricate names** — all person names must be verified from Slack profiles (`mcp__slack__slack_get_user_profile`) or GitHub. If unknown, ask the user. Never guess.
- **AI-assisted development stats** — use `Co-Authored-By` commit metadata as the primary metric, not labels (labels are repo-specific and inconsistent)

### Quality Standards

- All content in **American English**
- **PDF is the source of truth** — always verify layout in exported PDF, not browser preview (browser has margins/gaps that don't appear in print)
- **WCAG AA contrast minimum** — all text must meet 4.5:1 contrast ratio (normal text) or 3:1 (large text ≥18pt). Calculate and verify, don't eyeball.
- **No native emoji** — use image-based emoji (Twemoji CDN or local assets) for cross-platform consistency
- Accessible HTML structure
- Distinctive typography (avoid generic fonts)
- **Claw'd mascot** — use inline SVG pixel art (salmon #cc8b6e body, black eyes, blocky legs). Each appearance should have a different expression to reduce repetitiveness.

### After Publishing

After merging a new issue, always suggest: "Want to announce this issue? Run `/magazine-announce`"

## Language

- Code, documentation, commit messages: **American English**
