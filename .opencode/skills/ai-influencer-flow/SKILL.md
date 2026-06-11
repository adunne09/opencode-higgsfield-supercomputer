---
name: ai-influencer-flow
description: |
  Generate a standalone AI influencer: one clean photoreal UGC creator/persona
  image (no products) via Soul 2.0, reusable as a character reference. USE when
  the user directly asks to create a person/creator/influencer/model to reuse.
  DO NOT use when another workflow makes its own character (ugc-flow,
  cinematic-flow, short-drama), for products/posters (image-generation),
  trained face IDs from photos (soul-id), or a full video (ugc-flow).
---
# ai-influencer

Standalone generator for a single AI influencer -- one clean UGC-feel person
image (the creator/persona, no products in frame) the user can reuse downstream
as a <<<element_id>>> / @ImageN character reference. Output is ONE image.

This skill owns control flow only. All creative prompt rules (variety roll,
beauty floor, wardrobe matrix, modesty, iPhone-UGC framing, forbidden phrases)
live inside the backend system prompt behind higgsfield_enhancer -- NOT here.
The agent passes structured inputs; it never writes the Soul 2.0 prompt itself.

## Pipeline

### 1. Parse the request

Extract any user-specified traits: gender (default woman), age, hair, ethnicity,
location, outfit register, mood, time-of-day. If the user grounds the influencer
in a product/brand context, infer tier (luxury | premium | drugstore) and
category (skincare | cosmetics | fragrance | food | fitness | ...) for the
wardrobe/location matrix. With no product context, pass tier/category as null
-- the enhancer falls back to a cozy-home default.

### 2. Character enhancer (text-only)

Call the same backend flow ugc-flow uses for its character step. No image_refs.
Never pass variety_rolls -- the wrapper injects 8 fresh integers server-side
and overwrites anything you supply.

```json
higgsfield_enhancer({
  "flow": "ugc-character",
  "inputs": {
    "tier": "<luxury | premium | drugstore | null>",
    "category": "<skincare | cosmetics | fragrance | food | fitness | ... | null>",
    "gender": "<woman | man>",
    "user_request": "<original brief verbatim -- backup parsing source + tone>",
    "user_overrides": {
      "location": "<optional>",
      "hair_color": "<optional>",
      "hair_style": "<optional>",
      "ethnicity": "<optional>",
      "outfit_register": "<optional>",
      "age_band": "<optional>",
      "mood": "<optional>"
    },
    "previous_character_traits": null
  }
})
```

Returns {"prompt": "<full Soul 2.0 prompt string>"}.

### 3. Generate

Submit the enhancer output as-is. enhance_prompt is pinned false -- the prompt
is already final; re-enhancing degrades it.

```json
higgsfield_generate_image({
  "requests": [{
    "type": "generation",
    "model": "text2image_soul_v2",
    "params": {
      "prompt": "<enhancer output>",
      "aspect_ratio": "3:4",
      "quality": "1080p",
      "enhance_prompt": false
    }
  }]
})
```

### 4. Poll, register, report

Poll to terminal status. Re-upload the result to obtain a fresh character_media_id,
and register it as a character element so it can be reused across downstream
generations via <<<element_id>>>.

Report to the user: the final image URL + the reusable character_media_id /
element_id (this is the deliverable). Hide job_ids and intermediate steps.

### NSFW retry

If text2image_soul_v2 returns status=nsfw, do NOT retry the identical prompt --
re-call the enhancer with a more conservative outfit_register (and pass the
rejection back via user_request) so it rebuilds a filter-safe wardrobe, then
regenerate once.

## ask_user_question -- when

Only for genuine creative gaps the brief left blank (e.g. gender if truly
ambiguous). Never ask to "confirm" model, aspect ratio, quality, or
enhance_prompt -- those are pinned.

## Pinned invariants (never offer as forks)

- Model: text2image_soul_v2. Aspect ratio: 3:4. Quality: 1080p.
- enhance_prompt: false always.
- No products / objects / props in the character image -- clean person only.
- All prompt construction is the enhancer job; the agent passes structured inputs.

## NOT for this skill

- Full UGC talking-head video -> ugc-flow (it owns its own character step internally).
- Product / object / scene / poster image -> image-generation.
- Persistent trained face identity from real photos -> soul-id.
- Cinematic / cartoon / short-drama cast -> those workflows own their character generation.
