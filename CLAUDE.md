# Frontend Technology Digest

A technical magazine for Crescendo Lab's frontend engineering team.

## Project Structure

```
fe-magazine/
├── .claude/
│   └── commands/
│       └── magazine.md   # Magazine editor command
├── {YY-MM}/              # Issue folder
│   ├── {YY-MM}.pdf       # PDF (primary output)
│   ├── index.html        # HTML source
│   └── assets/           # Issue-specific assets (images, etc.)
├── README.md             # Project documentation with issue list
├── CLAUDE.md             # AI assistant guidelines (this file)
└── robots.txt            # Crawler blocking rules
```

## Important Guidelines

### When Creating a New Issue

**ALWAYS update README.md when publishing a new issue:**

1. Create the issue folder: `{YY-MM}/`
2. Add `index.html` for the magazine content
3. Add `{YY-MM}.pdf` for the PDF export
4. Add `assets/` folder for issue-specific images
5. Update `README.md` — add new issue to the Issues table (newer issues first)

### Issue Naming Convention

- Folder format: `YY-MM/` (e.g., `26-01/` for January 2026)
- The issue ID reflects the END month of the coverage period
- Example: "Dec 2025 - Jan 2026" → `26-01/`

### Magazine Command

Use `/magazine` to create new issues interactively. The command will:
1. Collect ecosystem news from Slack and web sources
2. Analyze project insights from GitHub (focus on technical changes, not product features)
3. Conduct interviews for feature stories
4. Generate cover images with `/nano-banana` (use Python SDK, not gemini CLI)
5. Create production-grade HTML with `/frontend-design`
6. Review quality with `/web-design-guidelines` and `/pr-review-toolkit`
7. Verify layout with `/agent-browser` (check for blank pages!)

### Content Guidelines

- **Focus on technical insights** — tooling, architecture, patterns, DX improvements
- **Avoid product feature descriptions** — this is a tech digest, not a product changelog
- **No filler content** — if there's no insight, don't write it
- **Skip statistics** — no PR counts, contributor counts unless meaningful

### Quality Standards

- All content in **American English**
- Print-friendly layout (check for blank pages from page breaks)
- Accessible HTML structure
- Distinctive typography (avoid generic fonts)

## Language

- Code, documentation, commit messages: **American English**
