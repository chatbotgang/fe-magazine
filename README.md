# Frontend Technology Digest

> A technical magazine for Crescendo Lab's frontend engineering team.

## Issues

| Issue | Date Range | Highlights | Link |
|-------|------------|------------|------|
| 26-01 | Dec 2025 - Jan 2026 | Astro joins Cloudflare, TypeScript 7.0 Go rewrite, Motif v6, Base UI 1.0 | [View](26-01/) |

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
