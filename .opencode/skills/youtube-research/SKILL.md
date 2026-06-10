---
name: youtube-research
description: >
  Research YouTube data -- search videos, look up channel info and subscribers,
  fetch video metadata, and read comments. Use when the user asks about a
  YouTube channel, creator stats, video performance, or wants to find videos
  by query. For transcript-to-content reformatting (chapters, summaries,
  blog posts) use the youtube-content skill instead.
---
# YouTube Research

Wraps the youtube tool. Every action returns the same envelope so the
client can rely on a discriminated union over kind.

## Actions and when to use them

| User intent | action | Required args |
|---|---|---|
| "Find videos about X" | search | query |
| Metadata only -- duration, author, thumbnail, view/like counts | video | video_id (URL also accepted) |
| "Tell me about this video" / "research this video" | research | video_id |
| "Get the transcript" | transcript | video_id |
| "Show comments" / "reactions" / "what do people think" | comments | video_id |
| "Research a channel" / subscribers / channel videos | channel | channel_id (accepts @handle) |

### One call per intent -- pick the right action

1. Comments-only intent (reactions, sentiment, feedback): Call action=comments only.
   Do NOT make a preliminary action=video lookup.
2. Full-context intent ("tell me about this video"): Call action=research once.
   Returns video details AND first page of comments in a single envelope.
   Do NOT call video and comments separately.
3. Metadata-only intent (duration, author, view count): Call action=video only.
   Do NOT auto-fetch comments; the user did not ask for opinions.

Transcript intent -- explicit only. Only call action=transcript when the user explicitly
asks for the transcript, captions, subtitles, or "what was said". Do NOT fetch the transcript
as part of research, video, or comment-intent flows.

Not this tool: trending content (YouTube trending tab, Shorts breakout metrics) -> use the
trends tool instead.

## Stable response envelope

```json
{
  "kind":  "search | video | transcript | comments | research | channel",
  "query": { "...echo of caller args..." },
  "data":  { "shape depends on kind" },
  "pagination": { "next_page_token": "...", "prev_page_token": "..." },
  "meta":  { "source": "searchapi", "engine": "youtube_*", "fetched_at": "ISO-8601" },
  "error": null
}
```

## data shape per kind

### kind: search
```json
{
  "videos":    "Video[]",
  "channels":  "Channel[]",
  "playlists": "object[]"
}
```

### kind: video
```json
{
  "video":   "Video",
  "related": "Video[]"
}
```

### kind: transcript
```json
{
  "video_id":            "string",
  "language":            "string",
  "cues":                "[{ start_sec, duration_sec, text }]",
  "available_languages": "[{ code, name, auto }]"
}
```

### kind: comments
```json
{
  "video_id": "string",
  "comments": "Comment[]"
}
```

### kind: research (combined video + first page of comments)
```json
{
  "video":    "Video",
  "related":  "Video[]",
  "comments": "Comment[]"
}
```

### kind: channel
```json
{
  "channel":  "Channel",
  "sections": "[{ title, kind: 'videos' | 'shorts', videos: Video[] }]"
}
```

## Channel sections

sections preserves YouTube's home-tab layout. section title is meaningful
("Recent uploads", "Popular uploads", "Shorts", ...).

- Want a single sorted slice? Pass sort_by (recently_uploaded, popular, oldest_uploaded) --
  response will have one section with the flat list.
- Want the home layout? Omit sort_by -- multiple sections in YouTube's order.

### Mapping common queries

| User query | Call | Where to read the answer |
|---|---|---|
| "Research Mr Beast's channel" | action=channel, channel_id=@MrBeast (no sort_by) | data.channel for header; data.sections[*] for home rows |
| "Mr Beast's 10 most-watched videos" | action=channel, channel_id=@MrBeast, sort_by=popular | data.sections[0].videos.slice(0, 10) |
| "How many subscribers does Mr Beast have" | action=channel, channel_id=@MrBeast | data.channel.subscribers |

## Errors

| Failure | Where it shows up |
|---|---|
| Unknown action / bad input | Top-level {"error": "..."} (tool registry) |
| Upstream SearchAPI failure | Envelope with error: {code, message, status} |
| Empty transcript / no captions | data.cues == [] -- surface to user, suggest dropping lang param |
