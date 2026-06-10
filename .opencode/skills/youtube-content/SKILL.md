---
name: youtube-content
description: >
  Fetch YouTube video transcripts and transform them into TEXT content only
  (chapters, summaries, threads, blog posts). Use when the user asks to
  summarize a video, requests a transcript, or wants to reformat a video's
  spoken content into written form. DO NOT use when the user wants to clip,
  cut, or create short video clips (TikTok/Reels/Shorts/viral moments).
---
# YouTube Content Tool

Extract transcripts from YouTube videos and convert them into useful formats.

> For channel research, video metadata, comments, or search,
> use youtube-research instead -- that skill wraps the youtube tool and returns
> a stable envelope. This skill is for transcript->content reformatting only.

## Setup

```bash
pip install youtube-transcript-api
```

## Helper Script

The script accepts any standard YouTube URL format, short links (youtu.be), shorts, embeds,
live links, or a raw 11-character video ID.

```bash
# JSON output with metadata
python3 SKILL_DIR/scripts/fetch_transcript.py "https://youtube.com/watch?v=VIDEO_ID"

# Plain text (good for piping into further processing)
python3 SKILL_DIR/scripts/fetch_transcript.py "URL" --text-only

# With timestamps
python3 SKILL_DIR/scripts/fetch_transcript.py "URL" --timestamps

# Specific language with fallback chain
python3 SKILL_DIR/scripts/fetch_transcript.py "URL" --language tr,en
```

## Output Formats

After fetching the transcript, format based on what the user asks for:

- Chapters: Group by topic shifts, output timestamped chapter list
- Summary: Concise 5-10 sentence overview of the entire video
- Chapter summaries: Chapters with a short paragraph summary for each
- Thread: Twitter/X thread format -- numbered posts, each under 280 chars
- Blog post: Full article with title, sections, and key takeaways
- Quotes: Notable quotes with timestamps

### Chapters example
```
00:00 Introduction -- host opens with the problem statement
03:45 Background -- prior work and why existing solutions fall short
12:20 Core method -- walkthrough of the proposed approach
24:10 Results -- benchmark comparisons and key takeaways
31:55 Q&A -- audience questions on scalability and next steps
```

### Thread (Twitter/X) example
```
1/ Just watched an incredible talk on [topic]. Key takeaways:

2/ First insight: [point]. This matters because [reason].

3/ The surprising part: [unexpected finding].

4/ Practical takeaway: [actionable advice].

5/ Full video: [URL]
```

### Blog post
Full article with title, introduction, H2 sections per major topic, key quotes with timestamps,
and conclusion / takeaways.

### Quotes
```
"The most important thing is not the model size, but the data quality." -- 05:32
"We found that scaling past 70B parameters gave diminishing returns." -- 12:18
```

## Workflow

1. Fetch the transcript using the helper script with --text-only --timestamps.
2. Validate: confirm output is non-empty and in the expected language. If empty, retry without
   --language. If still empty, tell the user the video likely has transcripts disabled.
3. Chunk if needed: if the transcript exceeds ~50K characters, split into overlapping chunks
   (~40K with 2K overlap) and summarize each chunk before merging.
4. Transform into the requested output format. Default to a summary if not specified.
5. Verify: re-read the transformed output to check for coherence, correct timestamps, completeness.

## Error Handling

- Transcript disabled: tell the user; suggest they check if subtitles are available.
- Private/unavailable video: relay the error and ask the user to verify the URL.
- No matching language: retry without --language; note the actual language to the user.
- Dependency missing: run pip install youtube-transcript-api and retry.
