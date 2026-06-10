---
name: trend-picker
description: |
  Social media research, ad intelligence, trend discovery, and persona-clone preflight
  across Instagram, TikTok, YouTube, YouTube Shorts, Meta and TikTok Ad Libraries, and
  the open web. Ranks results by engagement velocity, analyzes top videos with Gemini,
  generates content concepts. Activates on creator @username / profile URL signals,
  audience-parity briefs ("like @X but not a copy"), and multi-post / content-plan
  intents -- gates Deep (creator-DNA pipeline) vs Quick (metadata-only) internally.
  Modes: trend_discovery (default), viral_analysis (on-demand), creator_dna (persona-clone preflight).
---
# Trend Picker

Unified social media research, ad intelligence, and trend discovery. Fetches data from all
platforms, ranks by engagement, analyzes with AI, generates content concepts.

## Intents and Modes

| Intent | Mode | Trigger | Output |
|---|---|---|---|
| trend_discovery | A | default -- "find me trends", "what's working in <niche>" | top videos + 3 concepts + optional HANDOFF v2 |
| viral_analysis | D | "why is this viral?" + a single source video URL | strategy analysis (CONCEPT/HOOK/RETENTION/...) |
| creator_dna | C | persona-clone trigger detected (mode=creator_dna passed in) | aggregate creator DNA report URL + reel URLs |
| adaptation | -- | source URL + recreate / reproduce / mode=adapt | refuse here; redirect to the video-adapt skill |

## Activation Gate -- Persona-Clone vs Standard Research

Activate persona-clone flow when ALL FOUR hold:
(a) request references an existing creator (@username or platform profile URL)
(b) intent is to create a NEW persona/influencer (not a literal copy of the creator)
(c) phrasing implies audience parity ("hit the same audience", "like @X but not a copy")
(d) output is multi-post / multi-reel / content plan (not a one-shot single deliverable)

If all four hold -> ask the user via AskUserQuestion:
- Deep -- creator-DNA pipeline (~5-10 min, higher cost): proceed to Mode C
- Quick -- metadata-only research (fast, cheap): proceed to Mode A (trend_discovery)

If any of (a)-(d) fails -> not persona-clone -> use Mode A by default,
or Mode D if the user explicitly asked "why is this viral?" with a single source video URL.

Boundary examples:
- "make an avatar like @charlidamelio for our brand, need a single post" -> NO: (d) fails -- single post
- "@charlidamelio is our reference, recreate this exact reel: <url>" -> NO: single video with recreate intent -> video-adapt skill
- "make 5 reels in @charlidamelio's style, hit her TikTok audience" -> YES: all 4 conditions hold
- "make 3 reels of the same quality as @creator" -> NO: (c) fails -- "quality match" is not audience parity
- "find viral trends in beauty" -> NO: (a) fails -- no specific creator reference

When in doubt -- fall to trend_discovery (safe default).

## Research Report Delegation

When the user asks for a written research report (competitor analysis, trend report, market
overview, cited deliverable), prefer delegate_task with category='research' over manually
running the full pipeline. It spawns a dedicated research subagent pre-loaded with
instagram_research, tiktok_research, web_search, and web_extract that produces a markdown
report with inline citations.

Use delegate_task(category='research') when:
- The deliverable is a report file
- The user wants a cited, structured analysis they can share later
- The research scope is broad (multiple platforms, multiple angles)

Use the manual pipeline (Steps 0-4 below) when:
- The research feeds into content generation downstream (Mode A with Hook Extraction -> HANDOFF)
- The user wants in-conversation analysis, not a file
- The workflow requires video_analyze or ad library research
- Mode C (creator DNA) or Mode D (viral analysis) is triggered

## Tools

| Tool | Platform |
|---|---|
| instagram_research | Instagram |
| tiktok_research | TikTok |
| youtube | YouTube (search, video, transcript, comments, channel, research) |
| trends | YouTube Shorts trending pipeline (FNF data ingestion API) |
| ads | Meta, TikTok, LinkedIn, Reddit ad libraries |
| web_search | Google-style web search (URL discovery) |
| web_extract | Fetch and extract URL content as markdown / structured JSON |
| video_analyze | OpenRouter video analysis (storyboard, viral analysis, creator DNA) |

## Instagram (instagram_research tool)

All user commands accept username directly -- the tool resolves username->ID automatically.

### User Research
```
instagram_research(action="user_research", username="handle")    # profile + 20 recent posts (preferred)
instagram_research(action="user_details", username="handle")     # canonical profile only
instagram_research(action="user_posts", username="handle", depth=2)   # recent posts
instagram_research(action="user_reels", username="handle", depth=3, sort="views")  # reels sorted by play_count
instagram_research(action="user_tagged", username="handle")      # posts where user is tagged
instagram_research(action="user_followers", username="handle")   # follower count
```

For "analyze @handle" intent, prefer user_research -- bundles user_details + ~20 recent posts.
For deeper crawling (>20 posts, paginated), call user_posts directly with higher depth.

Reels are always flat. user_reels returns {items: [{code, play_count, like_count, comment_count, caption, taken_at, ...}], count: N}.
Use sort="views" to rank by play_count descending.

### Content Discovery
```
instagram_research(action="search", query="keyword")
instagram_research(action="music_posts", id="<music_id>")
```

### Post Analysis
```
instagram_research(action="media_info", code="<shortcode>")
instagram_research(action="media_info", code="<shortcode>", comments=50)
instagram_research(action="media_comments", code="<shortcode>")
instagram_research(action="media_comments", media_id="<numeric_id>")
```

### Response Envelope

Every action returns: {kind, query, data, pagination, meta, error}

Per-kind data shapes:
- user_details: UserProfile
- user_research: {profile: UserProfile, posts: [MediaItem, ...]}
- user_posts / user_reels / user_tagged / music_posts: {items: [MediaItem, ...]}
- user_followers: {count: int}
- media_info: {media: MediaItem, comments?: [Comment, ...]}
- media_comments: {comments: [Comment, ...]}
- search: {users: [SearchUser, ...], hashtags: [{name, media_count}, ...]}

MediaItem fields: shortcode, display_url, is_video, like_count, comment_count, caption,
taken_at, video_url, video_duration, has_audio, play_count, carousel_media_count, carousel, location.

ER = (like_count + comment_count) / play_count.
Post permalink: https://instagram.com/p/<shortcode>/ (or /reel/<shortcode>/ for video reels).

## TikTok (tiktok_research tool)

### User Research
```
tiktok_research(action="user_info", username="handle")                           # profile (id, sec_uid, stats)
tiktok_research(action="user_posts", username="handle", depth=2)                 # user posts
tiktok_research(action="user_search", keyword="brand")                           # search users by keyword
tiktok_research(action="user_followers", id="<id>", sec_uid="<sec_uid>")         # followers
tiktok_research(action="user_followings", id="<id>", sec_uid="<sec_uid>")        # followings
tiktok_research(action="user_likes", sec_uid="<sec_uid>")                        # liked posts
```

Call user_info first to get id and sec_uid.

### Content Discovery
```
tiktok_research(action="keyword_search", query="skincare", period=7)
tiktok_research(action="keyword_search", query="skincare", period=7, sort=1)     # sort: 0=relevance, 1=most liked, 2=most recent
tiktok_research(action="keyword_search", query="skincare", country="US")
tiktok_research(action="keyword_full_search", query="skincare", period=7)        # all pages
tiktok_research(action="hashtag_search", name="skincare")
tiktok_research(action="hashtag_full_search", name="skincare", days=7)           # all posts within N days
```

### Built-in Ranking
```
tiktok_research(action="rank_results", data=<search_envelope>, top_k=3)
tiktok_research(action="rank_results", data=<envelope>, top_k=10, min_views=0)   # trend discovery preset
tiktok_research(action="rank_results", data=<envelope>, top_k=3, min_views=50000) # brand/competitor preset
```

Viral score = velocity (views/day) x engagement rate ((likes+comments)/views*100).

### Music / Sounds
```
tiktok_research(action="music_search", query="viral")
tiktok_research(action="music_posts", music_id="<music_id>")
tiktok_research(action="music_details", id="<music_id>")
```

### Post Analysis
```
tiktok_research(action="post_info", url="https://www.tiktok.com/@user/video/123")
tiktok_research(action="post_multi", ids="123;456;789")
tiktok_research(action="post_comments", aweme_id="<aweme_id>")
tiktok_research(action="post_comment_replies", aweme_id="<aweme_id>", comment_id="<cid>")
```

## YouTube (youtube tool)

Actions: search, video, transcript, comments, research, channel.
All return: {kind, query, data, pagination, meta, error}.

One call per intent -- never chain video + comments to fake a research call.
Transcript intent -- explicit only. Only call action=transcript when user explicitly asks.

Data shapes per kind:
- search: {videos: Video[], channels: Channel[], playlists: object[]}
- video: {video: Video, related: Video[]}
- transcript: {video_id, language, cues: [{start_sec, duration_sec, text}], available_languages}
- comments: {video_id, comments: Comment[]}
- research: {video, related, comments} (combined video + first page of comments; costs 2 credits)
- channel: {channel, sections: [{title, kind: 'videos'|'shorts', videos: Video[]}]}

Channel sort_by options: recently_uploaded, popular, oldest_uploaded.
Omit sort_by for the home-tab layout (multiple sections).
Pass sort_by for a single flat sorted section.

### OpenRouter Video Analysis

For video analysis call video_analyze directly:
```
video_analyze(video_source="<youtube_url>", category="analysis_templates")   # scene-by-scene storyboard
video_analyze(video_source="<youtube_url>", category="viral_analysis")       # why-is-this-viral
```

For Instagram reels, resolve the direct video URL first:
```
instagram_research(action="media_info", code="<shortcode>")
# Extract video_url from response, then:
video_analyze(video_source="<video_url>", category="analysis_templates")
```

## YouTube Shorts Trends (trends tool)

Returns breakout shorts and growing channels from the internal FNF Data Ingestion API.
No external API quota cost -- all data is from the internal DB.

```
trends(action="viral_now", hours=6, limit=20)                        # videos breaking out right now
trends(action="growth_leaders", days=30, min_subs_growth=10000)      # fastest-growing channels
trends(action="competitor", channel_id="<channel_id>", limit=20)     # all tracked videos of a channel
trends(action="videos", min_breakout=70, min_vph=1000, limit=50)
trends(action="channels", country="US", min_subs=100000)
```

Use trends for pre-ranked YouTube Shorts trending data.
Use youtube for live search/fetch of arbitrary YouTube content.
All IDs (channel_id, video_id, run_id) must come from a prior trends response.

## Ad Libraries (ads tool)

### Meta (Facebook/Instagram)
```
ads(action="meta_search", query="keyword", active_status="active", sort_by="impressions_high_to_low")
ads(action="meta_search", page_id="<id>", active_status="active")
ads(action="meta_page", page_id="<id>")
```

### TikTok Ads
```
ads(action="tiktok_search", query="keyword", sort_by="unique_users_seen_high_to_low")
ads(action="tiktok_advertiser", advertiser_id="<advertiser_id>")
```

### LinkedIn Ads
```
ads(action="linkedin_search", query="keyword", time_period="last_30_days")
ads(action="linkedin_advertiser", advertiser="<company name>")
```

### Reddit Ads
```
ads(action="reddit_search", query="keyword", industry="TECH_B2C", post_type="VIDEO")
```

### Finding Winners
1. Active ads only: active_status="active" (Meta)
2. Sort by reach: sort_by="impressions_high_to_low" (Meta) / sort_by="unique_users_seen_high_to_low" (TikTok)
3. Longevity filter: only present ads running 3+ days
   - 3-7 days: testing phase
   - 7-14 days: likely profitable
   - 14-30 days: proven winner
   - 30+ days: evergreen creative

## Web Content (web_search + web_extract)

```
web_search(query="latest news")                                              # discover URLs
web_extract(urls=["https://example.com"])                                    # fetch URL -> markdown
web_extract(urls=["https://example.com"], summary=True)                      # LLM abstract
web_extract(urls=["https://example.com"], highlights_query="pricing")        # relevant excerpts only
web_extract(urls=["https://example.com"], extract_prompt="Extract key data") # structured JSON
web_extract(urls=["https://example.com"], subpages=5, subpage_target="pricing") # walk subpages
```

For social-media research (IG/TikTok/YouTube) use the dedicated tools -- never web_search/web_extract.

## Hook Extraction for Generation Downstream

Mandatory when: intent=trend_discovery (A) OR intent=viral_analysis (D) AND the brief contains
a generation verb (make / create / generate / produce / adapt / build) paired with a countable
video output (N ads / N reels / N videos / UGC ad / campaign / creative set).

For every selected top-N video, run Gemini-backed video analysis to extract the real hook
(visual + text + audio + script), not inferred from captions.

Before calling video_analyze on a URL, check the session artifact registry:
```
artifact_get(key="video_analyze:<video_url>")
```
If hit: true -- reuse the cached value. After a fresh call, store the result:
```
artifact_put(key="video_analyze:<video_url>", value=<video_analyze_result>)
```

Run all N analyses in a single message with parallel video_analyze calls -- do NOT serialize.
Top-10 analyses in parallel ~= 45s wall-clock. Top-5 ~= 30s.

## Viral Content Analysis Pipeline

### Step 0: Identify Product
Extract: Brand (exact name), Product (specific product), Category (broad niche).
Do NOT invent creative search phrases. Just extract what the product IS.

### Step 1: Discover Content (run ALL in parallel)

1a. Brand/product search on platforms:
```
tiktok_research(action="keyword_search", query="<brand>", period=7)
instagram_research(action="search", query="<brand>")
tiktok_research(action="keyword_search", query="<brand> <product>", period=7)
instagram_research(action="search", query="<brand> <product>")
```

1b. Trending hashtags + sounds in category:
```
tiktok_research(action="hashtag_search", name="<category>")
tiktok_research(action="music_search", query="<category>")
```
Then use top 3 returned hashtags to search further.

1c. Ad library -- proven winners:
```
ads(action="meta_search", query="<category>", active_status="active", sort_by="impressions_high_to_low")
ads(action="tiktok_search", query="<category>", sort_by="unique_users_seen_high_to_low")
```

1d. Web context:
```
web_search(query="<category> trending tiktok 2026")
```

### Step 2: Rank Videos

```
tiktok_research(action="rank_results", data=<keyword_search_result>, top_k=3)
tiktok_research(action="rank_results", data=<hashtag_search_result>, top_k=3)
```

Freshness defaults by intent:
- Trend discovery: top_k=10, max_age=3, min_views=0
- Brand/product research: top_k=3, max_age=7, min_views=50000 (default)
- Deep historical: top_k=10, max_age=30, min_views=10000

### Step 3: Analyze Video

YouTube / TikTok:
```
video_analyze(video_source="<video_url>", category="analysis_templates")
```

Instagram reels:
```
# 1. Get direct video URL
instagram_research(action="media_info", code="<shortcode>")
# Extract shortcode from: https://www.instagram.com/reel/DGONU8bvIZJ/ -> DGONU8bvIZJ

# 2. Analyze
video_analyze(video_source="<video_url>", category="analysis_templates")
```

Outputs a hyper-detailed scene-by-scene production blueprint: Scene Label, Shot Type,
Visual Description, Audio Script, timestamps.

### Step 4: Generate Concepts

Generate 3 NEW video concepts based on analyzed references + client profile.
Concepts include HOOK, RETENTION, REWARD, AUDIO, SCRIPT.

## Video Adaptation -- NOT IN THIS SKILL

Full video adaptation lives in the video-adapt skill. If a brief includes a source video URL
with an explicit adapt signal (recreate / reproduce / mode=adapt), this skill refuses
adaptation and the model should load the video-adapt skill instead.

## Viral Analysis (on-demand)

When user explicitly asks "why is this video viral" / "analyze why this works":
```
video_analyze(video_source="<video_url>", category="viral_analysis")
```

Returns: CONCEPT, HOOK, RETENTION MECHANISMS, REWARD, AUDIO, MUSIC, SCRIPT.
This is a strategy analysis, not a production blueprint.

## Mode C -- Creator DNA (persona-clone preflight)

Activation: see Activation Gate above. Mode C execution:

Entry gate (mandatory):
1. Read the persona-clone reference before any tool call in Mode C.
2. Run AskUserQuestion to pick Deep vs Quick depth.
3. Persist state: artifact_put(key="mode_c:state", value={"phase": "entry"})

Purpose: extract cross-reel behavioral + kinetic + audio DNA from a reference creator.

Workflow:
1. Pull reels respecting the user's scope:
   - "top reels", "viral", or no scope hint: user_reels(depth=2, sort="views")
   - "last N posts", "recent", "latest": user_posts(depth=ceil(N/10))
   - "recent reels": user_reels(depth=2, sort="date")
   Within the fetched set, take top 3 by play_count for Gemini DNA.

2. Analyze each reel with video_analyze using the creator_dna category -- in parallel:
   ```
   video_analyze(video_source="<reel_url>", category="creator_dna")
   ```

3. Aggregate into cross-reel DNA -- compute signatures that repeat across 2+ reels.

4. Write the aggregate report into the active project, then upload:
   higgsfield_upload(files=["./generations/creator-dna-<handle>.json"])
   Return the public url plus reel URL list.

Output contract (what trend-picker returns with mode=creator_dna):
```json
{
  "mode": "creator_dna",
  "creator": "@handle",
  "reels_analyzed": 3,
  "reel_urls": ["url1", "url2", "url3"],
  "dna_report_url": "https://...",
  "aggregate_summary": {
    "stable_visual": ["3-5 bullets"],
    "stable_kinetic": ["3-5 bullets"],
    "replicable_signatures": ["top 3 traits to transplant"]
  }
}
```

What Mode C does NOT do:
- Does not train Soul IDs (the soul-id skill owns that)
- Does not generate images or videos
- Does not run video-adapt Step A per-reel storyboard adaptation

## video_analyze Categories

| Category | Purpose | Used by |
|---|---|---|
| analysis_templates | Scene-by-scene storyboard (Step A) | trend-picker, video-adapt |
| viral_analysis | Strategic "why is this viral" | trend-picker (Mode D) |
| creator_dna | Cross-reel DNA extraction (strict JSON) | trend-picker (Mode C) |
| adapt_product | Legacy product-adaptation (kept for direct callers) | (none) |
| adapt_avatar | Legacy avatar-replacement (kept for direct callers) | (none) |

## Error Handling

- Toolset unavailable (missing API key): surface to user, don't retry
- status 401: invalid API key
- status 429: rate limited, wait and retry
- yt-dlp fails inside video_analyze: video may be private or region-locked

## Data Integrity Rules

- Work only with user-provided inputs. Do not pull from cache or prior sessions.
- Retry transient tool failures silently up to 3 times.
- URL fetching fallback chain: (1) web_extract (up to 3 retries), (2) web_search
  to find an alternative entry point, (3) browser tool as final fallback.
- After all tiers are exhausted, escalate with alternatives.
