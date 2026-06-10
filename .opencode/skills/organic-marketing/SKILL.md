---
name: organic-marketing
description: >
  Run Organic Marketing Employee research and generation planning: competitor
  discovery, current trends, winning formats, content briefs, and Higgsfield
  generation request plans.
version: 0.1.0
tags: [marketing, research, organic, social-media]
---
# Organic Marketing

Use organic_marketing to plan campaign research, format structured research
reports, and prepare content generation plans. Use existing platform tools to
fetch data and higgsfield_generate for media creation.

## Setup

If organic_marketing is not available, load the toolset first:

```
tool_search(action="load", toolsets=["organic_marketing"])
```

## Flow

1. Call organic_marketing(action="plan_research") with campaign config.
2. Run the suggested tool calls using instagram_research, tiktok_research,
   youtube, trends, web_search, or video_analyze.
3. Summarize each research task into a compact snapshot.
4. Call organic_marketing(action="build_research_report") with the snapshots.
5. Return the report object in chat as the structured research result, or
   call organic_marketing(action="generate") when content generation is requested.
6. For generation, write content_ideas from the research report and campaign
   strategy, call organic_marketing(action="generate"), then run the returned
   higgsfield_generate tool calls for video platforms. Capture returned job IDs.
   For X, use the returned text payload; no media request is emitted.
   Batch large campaigns by account or platform.

## Output Rules

- For full research requests, call plan_research before executing research
  unless the user already provided the exact task list.
- If campaign fields or competitor handles are inferred, label them as assumptions.
- Return the report object after build_research_report; do not replace it with
  prose-only summary when structured output is requested.
- Do not say research was saved, stored, persisted, or added to artifacts unless
  a persistence or artifact tool actually ran.
- generate returns content briefs and Higgsfield request plans for video platforms;
  it does not submit media jobs by itself.
- Content briefs reference generation requests by tool_call_index and
  tool_call_request_index; the full media request lives in tool_calls.
- If ai_influencer_job_id is provided, generate may load that completed AI influencer
  image job to build reference media before returning the plan.
- Generation should feel native and organic, not like a traditional ad:
  creator-led, casual, useful, story/demo first, no hard sell or corporate slogan framing.

## Generate Input Shape

```json
{
  "campaign_id": "campaign_123",
  "campaign": {
    "campaign_id": "campaign_123",
    "brand_name": "Crypto.com",
    "objective": "make crypto easier for beginners",
    "platforms": ["tiktok", "youtube"]
  },
  "research_report": {"generation_context": []},
  "accounts": [
    {
      "account_id": "ai_lena",
      "display_name": "Lena",
      "persona": "curious crypto learner",
      "platforms": ["tiktok", "youtube"],
      "ai_influencer_job_id": "completed_ai_influencer_job_id",
      "persona_references": [
        {"id": "media_123", "type": "media_input", "url": "https://..."}
      ]
    }
  ],
  "content_ideas": [
    {
      "platform": "tiktok",
      "account_id": "ai_lena",
      "hook": "One clear viewer-facing hook.",
      "concept": "Concrete scene/action for the clip.",
      "caption": "Platform caption.",
      "hashtags": ["crypto", "cryptocom"]
    }
  ],
  "limits": {"image2video_concurrent_jobs": 5}
}
```

For video or audio persona references, include type (video_input, audio_input, or a *_job type).
For a saved Higgsfield AI influencer, pass either ai_influencer_job_id or inline ai_influencer data.
Do not pass only ai_influencer_id; Hermes needs the completed image job ID.

## Snapshot Shape

```json
{
  "campaign_id": "campaign_123",
  "kind": "competitors | trends | whats_working | strategy_notes",
  "platform": "instagram | tiktok | youtube | x",
  "summary": "Short human-readable findings.",
  "items": [
    {
      "url": "https://...",
      "handle": "@example",
      "why_it_matters": "..."
    }
  ],
  "recommendations": [
    "Use direct hook in first 2 seconds.",
    "Avoid dense product explanation in opening."
  ],
  "source_tool_calls": [
    {"tool": "tiktok_research", "arguments": {"action": "keyword_search"}}
  ]
}
```

Competitors are stale after 14 days by default.
Trends and winning-format research are stale after 24 hours by default.
All snapshots in one build_research_report call must belong to the same campaign.
