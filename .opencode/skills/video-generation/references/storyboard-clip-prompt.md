# Storyboard -> Single Seedance Clip

Scope: User provides a storyboard (text or image) -> ONE Seedance 2.0 clip (<=15s).

## CRITICAL: Storyboard image handling

1. Read the image -- understand each panel
2. Attach WHOLE storyboard as @Image1 in the Seedance prompt  
3. Write a text prompt describing the full sequence beat-by-beat
NEVER split storyboard into individual panel crops.
NEVER generate one video per panel.

## Step 0 -- Ask for Visual Style FIRST

Style Presets:
- Cinematic/Photorealistic: "Cinematic photorealistic footage shot on ARRI Alexa 35, anamorphic lens, shallow depth of field, natural film grain, color grade applied uniformly to the entire frame."
- 3D Animation (Pixar-like): "Full-color 3D animated short, polished subsurface scattering on skin, bright even studio lighting, smooth 24 fps character animation, exaggerated expressions."
- Cute/Chibi 3D: "Cute 3D style animated comedy short, ultra-detailed, vibrant colors, warm cinematic lighting, smooth 24 fps animation, exaggerated expressions and physics."
- 2D Cartoon: "Flat 2D hand-drawn animation style, bold outlines, saturated palette, smear frames on fast action, limited shadows, clean background art."
- Anime: "Modern anime style, detailed character art, dynamic speed lines on action, soft cel shading, cinematic dramatic lighting, 24 fps on twos."
- Stop-Motion: "Stop-motion puppet animation style, visible material texture (felt, clay, wood), micro-jitter between frames, miniature practical set, warm directional lighting."

## Step 1 -- Parse into Beat Table

Columns: Framing (Wide/Medium/CU), Camera (static/push-in/pan/tracking), Action (1-2 sentences), Duration hint.
If ambiguous: ask user in one consolidated question.

## Step 2 -- Density & Duration

| Storyboard frames | Strategy | Duration |
|---|---|---|
| 1-3 | Spacious | 6-10s |
| 4-6 | Comfortable | 10-13s |
| 7-10 | Dense | 13-15s |
| 11-15 | Rapid-cut | 15s (merge weakest, warn user) |
| 16+ | Must split | -- (offer two-clip split) |

## Step 3 -- Prompt Assembly (exact section order)

[0] Reference Mapping (storyboard image only): "@Image1 is the storyboard -- use each panel as a sequential visual keyframe reference. Treat every panel as an independent cinematic shot, not as a single image."
[1] Style & Mood: [preset line]. No subtitles, no text overlay, no captions.
[2] Narrative Summary: One sentence from start to finish.
[3] Starting Composition: Lock first frame -- who/where/facing/camera position.
[4] Dynamic Description: "[Framing], [camera]: [action with physical detail]." New sentence = new shot. NO transition words (then/next/meanwhile).
[5] Audio: Always include. Ambient sound, music mood, or dialogue. Specify language: "Dialogue in [language]."
[6] Negations: "No text, no labels, no watermarks, no logos, no UI elements."

Output as one continuous string, no markdown, no headers in the final prompt.

## Step 4 -- Anti-Slop

Avoid: breathtaking, stunning, captivating, mesmerizing, masterfully, seamlessly, effortlessly.
Works well: specific physical descriptions, named camera moves, atmospheric particles, material texture words.
Breaks in Seedance: reflections, exit+re-entry, >3 characters tracked, sequential destruction.

## Step 5 -- Submit

```
higgsfield_generate_video(params={
  "model": "seedance_2_0",
  "prompt": "<all sections as one continuous string, no line breaks>",
  "duration": <from Step 2>,
  "aspect_ratio": "<ask user or default 16:9>"
})
```

Fire-and-forget. Return job_ids.

## Split strategy (11+ frames)

Find natural midpoint (scene/location/emotional shift). Clip 2 Starting Composition must match Clip 1 final state. Keep Style Line identical across both clips.
