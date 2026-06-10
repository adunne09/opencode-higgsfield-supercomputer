---
description: Always read before any video task -- text-to-video, image-to-video, video-to-video, audio-to-video.
mode: agent
---
# video-generation

## Default Model: Seedance 2.0 (seedance_2_0)

Use for every video task by default. Native audio (dialogue + SFX + music) in a single pass,
multi-shot continuity, broad aspect-ratio support, @Reference system (@Image1 / @Video1 / @Audio1).

## Routing

- Motion transfer/Recast (character image + reference video) -> higgsfield_motion_control tool.
- Remove background of a video -> higgsfield_remove_background tool.
- Storyboard/shot list/timeline -> read references/storyboard-clip-prompt.md. All shots = ONE clip.
- UGC/creator review -> read the matching ugc-* reference.
- Podcast video -> read podcast-video.md.
- Motion design/brand reel -> use classicMD-flow / highMD-flow / typographyMD-flow /
  infographicMD-flow / productMD-flow skills.
- Simple one-shot request -> use the Pipeline below.

## Pipeline

1. Read and analyze the user's request.
2. Build a Seedance prompt:
   - Style and Mood: 1 line setting tone and visual register.
   - Narrative Summary: 1 sentence stating what happens.
   - Dynamic Description: 1-2 sentences describing action.
   - Static Description: 1 sentence describing setting.
   - Audio: ambient or simple voice line if relevant.
   - Quality suffix matching engine constraints.
3. Submit via higgsfield_generate_video with model: seedance_2_0.
4. Return job_ids. Do not poll unless result is needed downstream.

## Reference inputs (Seedance 2.0)
Add {value: <id|url>, role: "video"} or {value: <id|url>, role: "audio"} to medias.
Address in prompt as @Video1, @Audio1, etc.

## Global rules
- Fire-and-forget by default. Frontend renders automatically.
- Poll ONLY when agent needs the finished URL in the same turn.
- Audio is mandatory: include an Audio: section in every prompt.
- Hero-then-fan-out: for multiple videos sharing one character, generate ONE anchor portrait,
  register as an element, then submit all Seedance jobs referencing that element.

## References
- references/ugc-clip-prompt.md -- UGC talking-head / creator-review clip.
- references/ugc-product-clip-prompt.md -- UGC product-focused clip.
- references/ugc-unboxing-clip-prompt.md -- UGC unboxing clip.
- references/ugc-tutorial-clip-prompt.md -- UGC tutorial clip.
- references/ugc-try-on-clip-prompt.md -- UGC virtual try-on clip.
- references/podcast-video.md -- AI sit-down / conversational podcast video.
- references/storyboard-clip-prompt.md -- Storyboard to single Seedance clip.
