---
description: Draft and post a Slack announcement for a new Frontend Technology Digest issue. Use after publishing a new issue — after merge or after /magazine-edit completes. Always drafts first for user review before posting. Triggers on "announce", "publish to slack", "share the magazine", or after any magazine release workflow.
allowed-tools: Bash(agent-browser:*), Bash(curl:*), Bash(python3:*), Bash(stat:*), Read
---

# Magazine Announcement

Post a Slack announcement for a newly published Frontend Technology Digest issue to **#team-eng** (`CSA8JSWAZ`).

## Philosophy

- **Always draft first** — never post without user confirmation
- **Use Traditional Chinese (正體中文)** for the announcement body
- **Flexible format** — adapt the structure to fit the issue's content and tone, don't use a rigid template
- **Visual first** — always include a page 1 screenshot (not the raw cover image)
- **Conversational** — discuss edits with the user until they say to post

## Workflow

### 1. Identify the Issue

Determine which issue to announce. If not specified, use the latest entry in `issues.json`. Read `{YY-MM}/content.md` to extract:

- Issue ID and date range
- Headline topics (from Approved Outline)
- Key highlights and notable stats

### 2. Screenshot Page 1

Use `agent-browser` to capture page 1 of the magazine:

```bash
agent-browser --allow-file-access open "file://$(pwd)/{YY-MM}/index.html"
agent-browser set viewport 794 1123 2
agent-browser wait 2000
agent-browser screenshot {YY-MM}/assets/page1-preview.png
agent-browser close
```

Show the screenshot to the user to confirm it looks good.

### 3. Draft the Announcement

Write a draft announcement in Traditional Chinese. The format should feel natural for the content — not a rigid template. Generally include:

- Issue title and theme
- 4-6 key highlights as bullet points
- Download link: `https://chatbotgang.github.io/fe-magazine/{YY-MM}/{YY-MM}.pdf`
- All issues link: `https://chatbotgang.github.io/fe-magazine/`

Use Slack mrkdwn formatting (`*bold*`, `<url|text>` links).

Present the draft to the user and discuss changes. Iterate until the user confirms.

### 4. Post to Slack

After user confirmation, upload the screenshot with the announcement text as a single message using the Slack API:

```bash
# Step 1: Get upload URL
FILESIZE=$(stat -f%z {YY-MM}/assets/page1-preview.png)
RESPONSE=$(curl -s -X POST "https://slack.com/api/files.getUploadURLExternal" \
  -H "Authorization: Bearer $SLACK_MCP_XOXP_TOKEN" \
  -d "filename=fe-magazine-{YY-MM}.png" \
  -d "length=$FILESIZE")

# Extract upload URL and file ID
UPLOAD_URL=$(echo "$RESPONSE" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['upload_url'])")
FILE_ID=$(echo "$RESPONSE" | python3 -c "import sys,json; d=json.load(sys.stdin); print(d['file_id'])")

# Step 2: Upload the file
curl -s -X POST "$UPLOAD_URL" -F "file=@{YY-MM}/assets/page1-preview.png"

# Step 3: Complete upload with message to #team-eng
curl -s -X POST "https://slack.com/api/files.completeUploadExternal" \
  -H "Authorization: Bearer $SLACK_MCP_XOXP_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "files": [{"id": "'"$FILE_ID"'", "title": "Frontend Technology Digest {YY-MM}"}],
    "channel_id": "CSA8JSWAZ",
    "initial_comment": "THE DRAFTED MESSAGE HERE"
  }'
```

The image and text appear as a single Slack message — text above, image preview below.

### 5. Confirm

Tell the user the announcement has been posted and link to the channel.

## Important Notes

- The `SLACK_MCP_XOXP_TOKEN` environment variable must be set
- Channel ID `CSA8JSWAZ` = #team-eng
- Use `files.getUploadURLExternal` → upload → `files.completeUploadExternal` (Slack's v2 upload flow)
- The screenshot should be at 2x device scale for crisp rendering
- If the user wants to change anything after seeing the draft, iterate — don't rush to post
