---
name: watch
description: Learn from YouTube videos via Gemini transcription. Use when user says "watch", "transcribe youtube", "learn from video", or shares a YouTube URL to study.
---

# /watch - YouTube → Gemini → Oracle Knowledge

Learn from YouTube videos by sending to Gemini for transcription, then indexing to Oracle.

## Usage

```bash
/watch https://youtube.com/watch?v=xxx              # Auto-resolve title via yt-dlp
/watch "Custom Title" https://youtu.be/xxx          # Override title
/watch --slug custom-slug https://youtube.com/...   # Custom slug
```

## Scripts

| Script | Purpose |
|--------|---------|
| `scripts/get-metadata.sh <url>` | Get title, duration, channel (JSON) |
| `scripts/get-cc.sh <url> [lang]` | Get captions in SRT format |
| `scripts/save-learning.sh <title> <url> <id> <transcript> [cc]` | Save to ψ/memory/learnings/ |

## Workflow

### Step 1: Get Metadata & Captions

```bash
SKILL_DIR=".claude/skills/watch"

# Get video metadata (JSON)
METADATA=$($SKILL_DIR/scripts/get-metadata.sh "$URL")
TITLE=$(echo "$METADATA" | jq -r '.title')
VIDEO_ID=$(echo "$METADATA" | jq -r '.id')
DURATION=$(echo "$METADATA" | jq -r '.duration_string')

echo "📹 Title: $TITLE"
echo "⏱️ Duration: $DURATION"
echo "🆔 Video ID: $VIDEO_ID"

# Get captions (may be empty)
CC_TEXT=$($SKILL_DIR/scripts/get-cc.sh "$URL" en)
if [ "$CC_TEXT" = "NO_CAPTIONS_AVAILABLE" ]; then
  HAS_CC=false
  echo "⚠️ No captions available"
else
  HAS_CC=true
  echo "✅ Found YouTube captions"
fi
```

### Step 2: Open Gemini via Browser

```javascript
// 1. Get or create tab
tabs_context_mcp({ createIfEmpty: true })

// 2. Navigate to Gemini
navigate({ url: "https://gemini.google.com/app", tabId: TAB_ID })

// 3. Wait for load
computer({ action: "wait", duration: 3, tabId: TAB_ID })
```

### Step 3: Send Transcription Request

Type into Gemini chat (varies by CC availability):

**If HAS_CC = true (cross-check mode):**
```
I have YouTube auto-captions for this video. Please:
1. Watch/analyze the video for accuracy
2. Fix any caption errors (names, technical terms, unclear parts)
3. Add section headers and timestamps
4. Provide 3 key takeaways

Video: [YOUTUBE_URL]

Auto-captions (may have errors):
---
[CC_TEXT - first 2000 chars or summary]
---
```

**If HAS_CC = false (full transcription mode):**
```
Please transcribe this YouTube video. Include:
1. Full transcript with timestamps
2. Section headers for different topics
3. Main takeaways (3 bullet points)
4. Any notable quotes

Video: [YOUTUBE_URL]
```

### Step 4: Submit and Wait for Response

**IMPORTANT**: Use `read_page` + `ref` clicks, NOT coordinates!

```javascript
// 1. Find interactive elements
read_page({ tabId: TAB_ID, filter: "interactive" })
// Look for: textbox "Enter a prompt here" [ref_188]
//           button "Send message" [ref_203]

// 2. Click input field by ref
computer({ action: "left_click", ref: "ref_188", tabId: TAB_ID })

// 3. Type the prompt
computer({ action: "type", text: PROMPT, tabId: TAB_ID })

// 4. Click send button by ref (NOT coordinates!)
computer({ action: "left_click", ref: "ref_203", tabId: TAB_ID })

// 5. Wait for response (10-30 seconds for long videos)
computer({ action: "wait", duration: 15, tabId: TAB_ID })

// 6. Extract full response
get_page_text({ tabId: TAB_ID })
```

**Why refs?** Gemini UI elements shift positions. Coordinates fail. Refs are stable.

### Step 5: Save to Knowledge

Use the save script (handles slug, filename, slugs.yaml):

```bash
$SKILL_DIR/scripts/save-learning.sh "$TITLE" "$URL" "$VIDEO_ID" "$GEMINI_RESPONSE" "$CC_TEXT"
```

### Step 6: Index to Oracle

```
oracle_learn({
  pattern: "YouTube transcript: [TITLE] - [key takeaways summary]",
  concepts: ["youtube", "transcript", "video", "[topic-tags from content]"],
  source: "/watch skill"
})
```

## Output Summary

```markdown
## 🎬 Video Learned: [TITLE]

**Source**: [YOUTUBE_URL]
**Slug**: [SLUG]

### Key Takeaways
[From Gemini response]

### Saved To
- Learning: ψ/memory/learnings/[DATE]_[SLUG].md
- Oracle: Indexed ✓

### Quick Access
`/trace [SLUG]` or `oracle_search("[TITLE]")`
```

## Notes

- Gemini has YouTube understanding built-in (can process video directly)
- Long videos may take 30-60 seconds to process
- If Gemini can't access video, it will say so — fallback to manual notes
- Works with: youtube.com, youtu.be, youtube.com/shorts/

## Error Handling

| Error | Action |
|-------|--------|
| Gemini blocked | User must be logged into Google |
| Video unavailable | Save URL + notes manually |
| Rate limited | Wait and retry |
| Browser tab closed | Recreate tab, retry |
