---
description: Always read before any image task. Routes to the right model and reference file.
mode: agent
---
# image-generation

Routes text-to-image or image-to-image requests to the right model.

## Special cases first
- Background removal -> higgsfield_remove_background tool.
- Image expansion/outpaint/uncrop -> higgsfield_outpaint tool.

## Auto-routed models

| Model | Best for | Input image | Aspect ratios |
|---|---|---|---|
| gpt_image_2 (DEFAULT) | Everything; text/typography; design (posters, banners, ads, thumbnails, infographics, carousels, UI/UX, storyboards); editing existing images; product/e-commerce/lifestyle | yes | 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3 |
| nano_banana_2 | Cartoon/illustrated characters (2D and 3D); heavily textured photos | yes | 1:1, 3:2, 2:3, 4:3, 3:4, 4:5, 5:4, 9:16, 16:9, 21:9 |
| text2image_soul_v2 | New original character/person -- UGC creator, influencer, editorial, fashion portraits | yes (1 ref) | 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3 |
| soul_cinematic | Cinematic stills -- epic/film-style frames, mood frames | yes (1 ref) | 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9 |
| seedream_v4_5 | Face edits on real photographs; vector graphics | yes | 1:1, 4:3, 16:9, 3:2, 21:9, 3:4, 9:16, 2:3 |
| soul_cast | Character creation for reuse in video -- saved as character element | no | 16:9 |
| soul_location | Location/environment for video pipelines -- saved as environment element | no | 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9, 9:21 |

## How to generate

Before generating, call higgsfield_generate_models_explore(action=get, model_id=<model>) to load
live parameters. Then call higgsfield_generate_image.

For multiple different images, issue multiple higgsfield_generate_image calls in parallel.

## Input handling
- Plain text: pick model, generate.
- Reference image (attached or URL): download, higgsfield_upload, pass id in medias.
- Product page URL: load skill_view("product-analyzer", "references/url-extract.md").

## Format references
| Format | Reference |
|---|---|
| Avatar / influencer / person portrait | references/soul-v2-avatar.md |
| UGC creator / persona for video pipeline | references/soul-v2-ugc-character.md |
| Posters / banners / launch images | references/poster-design.md |
| YouTube thumbnails | references/youtube-thumbnail.md |

## Polling -- fire-and-forget
Poll only when the agent needs the finished URL in the same turn (chaining, element creation,
or explicit user request).

## Error Handling
| Error | Fix |
|---|---|
| status 401 | Proxy auth error |
| status 429 | Rate limited -- wait and retry |
| failed | Retry same prompt, then simplify, then switch model |
| nsfw | Remove suggestive elements, retry once |
