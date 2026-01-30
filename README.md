# Frontend Technology Digest

> A technical magazine for Crescendo Lab's frontend engineering team.

## Issues

| Issue | PDF |
|-------|-----|
| 26-01 | [Download](26-01/26-01.pdf) |

## About

Frontend Technology Digest is an internal publication that curates:

- **Ecosystem News** — Framework releases, tool announcements, web standards updates
- **Project Insights** — Technical development activity from MAAC, CAAC, and WebSDK
- **Feature Stories** — In-depth technical articles and interviews
- **Looking Ahead** — Upcoming initiatives and roadmap

## Creating New Issues

Use the `/magazine` command in Claude Code:

```bash
/magazine 26-02    # Create February 2026 issue
/magazine          # Auto-detect current month
```

## Project Structure

```
fe-magazine/
├── {YY-MM}/              # Issue folder
│   ├── index.html        # Magazine HTML
│   ├── {YY-MM}.pdf       # PDF export
│   └── assets/           # Issue-specific assets (images, etc.)
├── index.html            # GitHub Pages download page
├── issues.json           # Issue list (add new issues here)
├── README.md             # This file
├── CLAUDE.md             # AI assistant guidelines
└── robots.txt            # Crawler blocking rules
```

## Tech Stack

- Pure HTML/CSS (no build step)
- Print-optimized layout
- Google Fonts (Cormorant Garamond, DM Sans, JetBrains Mono, Noto Serif TC)

## License

Internal use only — Crescendo Lab
