---
description: Photoreal UGC creator/persona image generation via Soul 2.0. Reusable character reference.
mode: agent
---
# ai-influencer-flow

Generate a standalone AI influencer -- one clean photoreal UGC creator/persona image (no products)
via Soul 2.0, reusable as a character reference. USE when user directly asks to create a
person/creator/influencer/model to reuse.

## Pipeline

1. Parse traits: gender (default woman), age, hair, ethnicity, location, outfit, mood.
   Infer tier (luxury/premium/drugstore) and category from product context.

2. Call character enhancer:
   higgsfield_enhancer(flow="ugc-character", inputs={tier, category, gender, user_request, user_overrides})
   NEVER pass variety_rolls -- the wrapper injects 8 fresh integers server-side.

3. Generate:
   higgsfield_generate(model="text2image_soul_v2", aspect_ratio="3:4", quality="1080p", enhance_prompt=false)

4. Poll to terminal. Re-upload, register as character element for reuse via <<<element_id>>>.

## Pinned invariants
- Model: text2image_soul_v2. Aspect ratio: 3:4. Quality: 1080p. enhance_prompt: false.
- No products or props in the character image -- clean person only.
- All prompt construction is the enhancer job.

## NOT for this skill
- Full UGC talking-head video -> ugc-flow owns its character step.
- Product/object/scene/poster -> image-generation.
- Persistent trained face identity from real photos -> soul-id.
- Cinematic/cartoon/short-drama cast -> those workflows own character generation.
