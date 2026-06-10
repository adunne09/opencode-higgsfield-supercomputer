---
name: image-generation
description: |
  Always read this SKILL.md before any image task -- text-to-image or image-to-image, simple or advanced. Routes the request to the right model and its reference file.
---
# Image Generation

Pick the right model, read its reference file, craft the prompt accordingly.

## Model Selection

Route by matching the user task to Best for, checking Don't use for, whether the model accepts
an input image, and whether it supports the requested aspect ratio.
- Caller specified model: -> use that model, skip the table.
- User wants to remove / cut out the background -> use the higgsfield_remove_background tool.
- User wants to expand an existing image / outpaint / uncrop -> use the higgsfield_outpaint tool.
- Otherwise route from Auto-routed first. Use a Caller-named model only when the user explicitly names it.

### Auto-routed

| Model | Best for | Don't use for | Input image | Aspect ratios | Reference |
|---|---|---|---|---|---|
| GPT Image 2.0 (gpt_image_2) -- DEFAULT | Default for everything; text / typography on the image; design (posters, banners, ads, thumbnails, infographics, carousels, UI/UX mockups, storyboards); editing existing images; product / e-commerce / lifestyle | Generating new characters / people | yes | 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3 | references/gpt-image-2.md |
| Nano Banana 2 (nano_banana_2) | Cartoon / illustrated characters (2D and 3D), cartoons; heavily textured photos | Design where text and layout must be exact | yes | 1:1, 3:2, 2:3, 4:3, 3:4, 4:5, 5:4, 9:16, 16:9, 21:9 | references/banana.md |
| Soul 2.0 (text2image_soul_v2) | New original character / person -- UGC creator, influencer, editorial, fashion, vibe-driven portraits | Any text / typography (NEVER); editing existing images | yes (1 ref) | 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3 | references/soul-v2.md |
| Soul Cinematic (soul_cinematic) | Cinematic stills -- epic / film-style frames, mood frames | Any text / typography (NEVER) | yes (1 ref) | 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9 | references/soul-cinematic.md |
| Seedream V4.5 (seedream_v4_5) | Face edits on real photographs (identity stays intact); strong at vector graphics | From-scratch cartoon / illustrated generation | yes | 1:1, 4:3, 16:9, 3:2, 21:9, 3:4, 9:16, 2:3 | references/seedream.md |
| Soul Cast (soul_cast) | Character creation for reuse in a video -- output saved as a character element, referenced via <<<element_id>>> in later shots | Any text / typography (NEVER); standalone delivery images; image-to-image | no | 16:9 | references/soul-cast.md |
| Soul Location (soul_location) | Location / environment for video pipelines; best choice for cinematic-location requests -- saved as an environment element | Any text / typography (NEVER) | no | 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9, 9:21 | references/soul-location.md |

### Caller-named (only when the user explicitly names the model)

| Model | Best for | Input image | Aspect ratios | Reference |
|---|---|---|---|---|
| Seedream V5 Lite (seedream_v5_lite) | Alternative high-quality generation with input image | yes | 1:1, 16:9, 9:16, 4:3, 3:4 | references/seedream.md |
| Flux 2 (flux_2) | Versatile general-purpose flagship | yes | 1:1, 4:3, 3:4, 16:9, 9:16 | references/flux-2.md |
| Kling Omni Image (kling_omni_image) | Photorealistic generation with strong input-image support | yes | auto, 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 21:9 | references/kling-omni-image.md |
| Grok Image (grok_image) | Expressive, high-contrast generation; best for NSFW | yes | 1:1, 4:3, 3:4, 16:9, 9:16 | references/grok-image.md |
| Z Image (z_image) | Fast stylized text-to-image | no | 1:1, 4:3, 3:4, 16:9, 9:16 | references/z-image.md |

### How to generate

Generate via higgsfield_generate_image. Before generating, call higgsfield_generate_models_explore
(action=get, model_id=<model>) to load the model live parameters, then call higgsfield_generate_image.

higgsfield_generate_image is single-model and single-prompt -- it takes one model, one prompt, and
count (1-4 copies of that same prompt). For multiple different images, issue multiple
higgsfield_generate_image calls in parallel -- not one batched call.

## Input handling

- Plain text -> pick the model, read its reference, generate.
- Reference image (attached or a direct image URL) -> download, higgsfield_upload, pass returned id
  in medias: {"value":"<id>","role":"image"}. For an image from a previous generation in this session,
  pass its job_id as value instead of re-uploading.
- Product page URL (Amazon, Shopify, AliExpress, ...) -> load skill_view("product-analyzer",
  "references/url-extract.md"), extract and visually verify product images, write a product
  description, deliver.

## Format references

| Format | Reference |
|---|---|
| Avatar / influencer / person portrait | references/soul-v2-avatar.md |
| UGC creator / persona for a video pipeline | references/soul-v2-ugc-character.md |
| Posters / banners / launch images | references/poster-design.md |
| YouTube thumbnails | references/youtube-thumbnail.md |
| UGC storyboard (standalone fallback) | references/ugc-boards.md |

## Upload reuse

Before higgsfield_upload(files=[<path>]), check artifact_get(key="upload:<sha256_of_file>").
On hit, reuse the stored {id, url} -- re-uploading the same file produces a different media_id
and breaks reference consistency across a set.
After a fresh upload, artifact_put(key="upload:<sha256>", value={"id": ..., "url": ...}).

## Polling -- fire-and-forget

higgsfield_generate_image is non-blocking and returns {"job_ids": [...]} immediately; the frontend
renders the finished image automatically. Do not poll to "show" or "confirm".
Poll only when the agent itself needs the finished URL inside the same turn.

## Error Handling

| Error | Fix |
|---|---|
| status 401 | Proxy auth error |
| status 429 | Rate limited -- wait and retry |
| failed | Retry #1: same prompt. Retry #2: simplify prompt. Retry #3: switch model |
| nsfw | Remove suggestive elements, retry once |
