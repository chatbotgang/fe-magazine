---
description: Create a Frontend Technology Digest issue (runs both collection and editing phases)
argument-hint: Optional issue identifier (e.g., "26-01" for January 2026)
allowed-tools: Bash(gh:*), Bash(python:*), Bash(npx:*), Bash(node:*), Bash(git:*), Bash(ls:*), Bash(cat:*), Bash(mkdir:*), Bash(open:*), mcp__slack__*, WebFetch, WebSearch
---

# Frontend Technology Digest - Full Workflow

This command runs the complete magazine production pipeline. For more control, use the split commands:

| Command | Purpose | Output |
|---------|---------|--------|
| `/magazine-collect {YY-MM}` | Collect news, analyze projects, conduct interviews | `{YY-MM}/content.md` |
| `/magazine-edit {YY-MM}` | Design direction, visual production, HTML/PDF output | `{YY-MM}/index.html` + PDF |

## Workflow

1. **Run `/magazine-collect` first** — invoke using the Skill tool with the issue identifier
2. After content collection is complete, **verify `{YY-MM}/content.md` exists** before proceeding
3. **Run `/magazine-edit` next** — invoke using the Skill tool with the same issue identifier

The split allows you to:
- Collect content in one session, edit in another
- Re-edit the same content with different design directions
- Have different people handle collection vs editing

## Quick Start

If `$ARGUMENTS` is empty, ask the user for the issue identifier (or derive from current date per CLAUDE.md naming convention) before proceeding.

Once you have the issue identifier, invoke `/magazine-collect {YY-MM}`, then when it completes, invoke `/magazine-edit {YY-MM}`.
