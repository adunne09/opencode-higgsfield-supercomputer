---
name: video-generation
description: |
  Always read this SKILL.md before do any video-related tasks -- whether it's text-to-video, image-to-video, video-to-video, or audio-to-video -- for both simple clips and more advanced generations (references, batch generation, inspiration, prompt crafting).
---
# Video Generation

## Model Selection

Default: Seedance 2.0 (seedance_2_0) -- use for every video task by default.
Seedance 2.0 is the strongest general-purpose model: top quality on text-to-video benchmarks,
native audio (dialogue + SFX + music) in a single pass, multi-shot continuity with consistent
character, broad aspect-ratio support, and the @Reference system (@Image1 / @Video1 / @Audio1).

Never auto-route to alternatives. Pick from the table below only when the user explicitly names
the model.

### Caller-named alternatives (only when explicitly requested)

| Model | Key strengths |
|---|---|
| kling3_0 | Storyboard mode, per-shot framing control, voice extraction from reference video, multi-character dialogue |
| cinematic_studio_3_0 | Cinematic director studio -- story plus up to 9 refs, self-storyboards by genre |
| grok_video | Best-in-class instruction following, NSFW allowed, built-in edit modes (restyle, add/remove objects) |
| veo3_1 | Scene extension, first/last frame transitions, most stable character consistency |
| veo3 | Stable Google pipeline baseline, native audio |
| veo3_1_lite | Same Veo 3.1 features, >50% cheaper |
| seedance_1_5 | Cinematographic prompt terminology (dolly zoom, tracking, whip pan), V2V available |
| kling2_6 | Best-in-class fabric, hair, contact physics; cinematographer lexicon; voice IDs |
| wan2_7 | 9-grid I2V (3x3 image grid input), natural-language video editing, Thinking Mode |
| minimax_hailuo | Top-2 on physics benchmarks, fastest generation in class |
| wan2_6 | Audio + video in single pass with lip-sync |

Before generating with a model, call higgsfield_generate_models_explore(action=get, model_id=<model>).

## Routing

Classify the request before starting:

- Motion transfer / Recast (character image + reference video) -> higgsfield_motion_control tool.
- Remove background of a video -> higgsfield_remove_background tool.
- Storyboard / shot list / timeline / beat sheet -> read references/storyboard-clip-prompt.md.
  All shots become ONE clip -- never generate one video per shot.
- UGC / creator review -> read the matching ugc-* reference.
- Podcast video -> read podcast-video.md.
- Motion design / brand reel -> use classicmd-flow / highmd-flow / typographymd-flow /
  infographicmd-flow / productmd-flow skills.
- Cartoon clip -> read cartoon-clip-prompt.md.
- Simple one-shot request -> use the Pipeline below.

## Pipeline

1. Read the user request and analyze it.
2. Build a clean Seedance prompt:
   - Style and Mood: 1 short line setting tone and visual register.
   - Narrative Summary: 1 sentence stating what happens.
   - Dynamic Description: 1-2 sentences describing the action.
   - Static Description: 1 sentence describing the setting.
   - Audio: ambient or simple voice line if relevant.
   - Quality suffix matching engine constraints.
3. Submit via higgsfield_generate_video with params.model: seedance_2_0.
   Pick aspect_ratio, resolution, and duration to fit the task.
4. Return the job_ids. Do not poll unless the result is needed downstream.

If the user provided a reference image: upload it via higgsfield_upload, add it to params.medias
as {value: <id|url>, role: "image"}, and put "@Image1 is the reference." at the start of the prompt.

### Reference video / audio inputs (Seedance 2.0)

For video-to-video or audio overlay, add {value: <id|url>, role: "video"} / {value: <id|url>,
role: "audio"} entries to params.medias and address them in the prompt as @Video1, @Audio1, etc.

## References

- references/ugc-clip-prompt.md -- UGC talking-head / creator-review clip.
- references/ugc-product-clip-prompt.md -- UGC product-focused clip.
- references/ugc-unboxing-clip-prompt.md -- UGC unboxing clip.
- references/ugc-tutorial-clip-prompt.md -- UGC tutorial clip.
- references/ugc-try-on-clip-prompt.md -- UGC virtual try-on clip.
- references/podcast-video.md -- AI sit-down / conversational podcast video.
- references/storyboard-clip-prompt.md -- Storyboard to single Seedance clip.

## Global rules

- Fire-and-forget (default): the frontend renders the result automatically. Capture the returned
  ids, continue -- never poll just to "wait for", "confirm", or "show" a result.
- Poll only when the agent itself needs the finished URL in the same turn (chaining into another
  generation, element creation, user explicitly asks for URL).
- Audio is mandatory: always include an Audio: section so Seedance has something to generate.
- Hero-then-fan-out (default for >=2 videos sharing one identical character): generate ONE anchor
  portrait first, upload it once, register as a persistent character element, then submit all
  Seedance jobs in parallel referencing that one element.
