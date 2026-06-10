---
name: ugc-flow
description: |
  A UGC-style talking-head review video -- a real creator appears on camera and speaks while reviewing or showcasing a specific product (a creator is on camera). The default for a UGC brief about a product.
  Triggers only with ALL: (a) review/talking-head/creator-ad intent, (b) UGC framing (UGC/creator/tiktok/"video of me"), (c) a specific product (image/URL/"this/our/my X").
---
# ugc-flow

UGC-style talking-head pipeline. Creative prompt-writing is delegated to backend enhancers.
The skill itself owns control flow, gating, sequencing, polling, and stitching.
All prompt-construction rules live inside the backend system prompts behind higgsfield_enhancer.

## Reference Resolution

Only product-analyzer references are loaded via skill_view. Character / board / clip prompt logic
lives in the backend enhancer.

| Step | Resource | How to call |
|---|---|---|
| Product URL extraction | analyzer/product-analyzer/references/url-extract.md | skill_view("product-analyzer", "references/url-extract.md") |
| Product visual analysis | analyzer/product-analyzer/references/visual-analysis.md | skill_view("product-analyzer", "references/visual-analysis.md") |
| Character generation | backend system prompt | higgsfield_enhancer(flow="ugc-character", inputs={...}) |
| Storyboard generation | backend system prompt | higgsfield_enhancer(flow="ugc-board", inputs={...}, image_refs={...}) |
| Seedance prompt writing | backend system prompt | higgsfield_enhancer(flow="ugc-clip", inputs={...}, image_refs={...}) |

## Reference IDs and URLs (two-channel input -- no re-upload for generated outputs)

For every image created or uploaded, capture BOTH:
- ref id -- fed into the next higgsfield_generate_image / higgsfield_generate_video call inside medias[].value.
  For uploaded files: media_input UUID. For generate outputs: the source job_id (chain-reference).
  NO higgsfield_upload round-trip on generated outputs -- they chain forward via job-id.
- url -- public HTTPS URL, fed into higgsfield_enhancer via image_refs.

| Source | id field | url field |
|---|---|---|
| higgsfield_upload (uploaded product / user-attached character) | uploads[i].id | uploads[i].url |
| higgsfield_generate_image Soul character (Step 2) | job_id returned by the call | result.url on terminal status |
| higgsfield_generate_image board K (Step 5) | job_id returned by the call | result.url on terminal status |

## Variety rolls are backend-owned

Do NOT generate variety_rolls for the ugc-character flow. higgsfield_enhancer injects 8 fresh
integers in [0, 99] server-side. Never pass variety_rolls inside inputs.

## Architecture

- Each board is a 16:9 strip with 3 vertical 9:16 slots -- three sequential narrative moments inside ONE Seedance clip.
- Each board -> ONE Seedance call. Inputs: board image + character + product, all as medias with role image.
- Within the clip, the prompt describes hard cuts between the 3 slots.
- For >15s: N boards generated sequentially (each conditioned on the previous for consistency),
  N Seedance clips run in parallel, ffmpeg concat with hard cuts.

## Duration -> N boards -> per-clip durations

| Total D | N | Clip durations |
|---|---|---|
| 4-15 | 1 | D |
| 16-19 | 2 | balance to >=4s each (D=18 -> 14+4) |
| 20-30 | 2 | 15, D-15 |
| 31-45 | 3 | 15, 15, D-30 |
| 46-60 | 4 | 15, 15, 15, D-45 |
| >60 | ceil(D/15) | 15 each, last = remainder >=4s |

## Story arc by N

| N | Per-board roles |
|---|---|
| 1 | Single arc HOOK->MAIN->CLOSER across the 3 slots (arc_role = FULL_ARC) |
| 2 | Board 1 = HOOK+SETUP, Board 2 = APPLY+CLOSER |
| 3 | Board 1 = HOOK, Board 2 = MAIN, Board 3 = CLOSER |
| 4 | Board 1 = HOOK, Board 2 = REVEAL, Board 3 = APPLY, Board 4 = CLOSER |
| >4 | Board 1 = HOOK, last = CLOSER, middle = REVEAL/APPLY/etc. |

## Pipeline

### 1. Parse request

Extract: product (URL or photo), duration, gender, any user-specified overrides.
Compute N + per-clip durations + arc_role per board from the tables above.

### 1.1 Product analysis (mandatory -- always via product-analyzer)

Product MUST always be normalized through product-analyzer regardless of input form.

- URL provided -> skill_view("product-analyzer", "references/url-extract.md")
- Photo only -> skill_view("product-analyzer", "references/visual-analysis.md")

After analysis, determine:
- tier: "luxury" | "premium" | "drugstore" from packaging visual cues + brand identity.
- category: e.g. "skincare" | "cosmetics" | "fragrance" | "food" | "fitness" | "cars" | ...

tier and category are decided here ONCE and passed into both the character and board enhancers.

### 1.2 Input tier classification

| input_tier | Trigger |
|---|---|
| auto | 1-5 words, no scenario, "make video", or empty brief |
| guided | 1-3 sentences with general idea, tone, mood, or rough flow |
| director | 4+ sentences with specific scenario, dialogue intent, shot list, location, props |

### 1.5 Persona source -- detect character_provided

Set character_provided = true if the user attached one or more person photos alongside the product brief.
Otherwise character_provided = false.

If character_provided == true:
- Upload the user-provided photo via higgsfield_upload -> capture (character_media_id, character_url).
- SKIP Step 2 entirely. The user's photo IS the character.
- Proceed directly to Step 3.

If character_provided == false: proceed to Step 2.

This is a hard gate, not a question to the user.

### 2. Character generation -- only when character_provided == false

MANDATORY when character_provided == false. NOT optional, NOT skippable.
Without a real character_media_id captured here, Step 5 (board generation) cannot run.

Call the character enhancer:
```json
higgsfield_enhancer({
  "flow": "ugc-character",
  "inputs": {
    "tier": "<luxury | premium | drugstore>",
    "category": "<skincare | cosmetics | fragrance | food | fitness | cars | ...>",
    "gender": "<woman | man>",
    "user_request": "<original brief verbatim>",
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

Submit the enhancer output to:
```json
higgsfield_generate_image({
  "params": {
    "model": "text2image_soul_v2",
    "prompt": "<enhancer output>",
    "aspect_ratio": "3:4",
    "quality": "2k",
    "enhance_prompt": false
  }
})
```

Poll job_status to terminal state. Capture (character_media_id, character_url):
- character_media_id = job_id returned by higgsfield_generate_image
- character_url = result.url on terminal status
NO download, NO higgsfield_upload round-trip.

### 3. Upload product

higgsfield_upload(files=[product.png]) -> capture uploads[0].id (as product_media_id) AND uploads[0].url (as product_url).
Skip if no product.

### 4. Draft full monologue

Word density: <=10s ~12-20 words, 11-12s ~20-28, 13-15s ~28-35.
First-word constraint: the FIRST WORD of every board's monologue segment must be hook content directly.
NEVER start with: OK / Okay / Okay so / Alright / So / Yeah so / Right so / Um / Well / Like / Wait / Hold on.
Greetings + product introduction allowed ONLY in Board 1. Other boards continue mid-thought.

### 5. Generate boards sequentially

For each board K in 1..N:

```json
higgsfield_enhancer({
  "flow": "ugc-board",
  "inputs": {
    "K": <board index>,
    "N": <total boards>,
    "arc_role": "<HOOK | HOOK+SETUP | MAIN | REVEAL | APPLY | APPLY+CLOSER | CLOSER | FULL_ARC>",
    "clip_duration": <4-15>,
    "tier": "<from Step 1.1>",
    "input_tier": "<from Step 1.2>",
    "user_request": "<original brief verbatim>",
    "product_description": "<from product-analyzer, or null>",
    "character_media_id": "<from Step 1.5 or Step 2>",
    "product_media_id": "<from Step 3, or null>",
    "previous_board_media_id": "<board K-1 media_id, or null when K==1>"
  },
  "image_refs": {
    "product_url": "<from Step 3, or omit when no product>",
    "character_url": "<from Step 1.5 or Step 2>",
    "previous_board_url": "<board K-1 url, or omit when K==1>"
  }
})
```

Submit to gpt_image_2:
```json
higgsfield_generate_image({
  "params": {
    "model": "gpt_image_2",
    "prompt": "<enhancer output>",
    "aspect_ratio": "16:9",
    "quality": "high",
    "resolution": "2k",
    "medias": [
      {"value": "<PRODUCT_MEDIA_ID>", "role": "image"},
      {"value": "<CHARACTER_MEDIA_ID>", "role": "image"}
    ]
  }
})
```

Poll to terminal. Capture (board_K_media_id, board_K_url) = (job_id, result.url).
Pass board_K_media_id into next K's previous_board_media_id and board_K_url into previous_board_url.

### 6. Write Seedance prompts in parallel -- one batch, N enhancer calls

Issue ALL N higgsfield_enhancer(flow="ugc-clip", ...) calls in a single tool-call batch.
Each board's clip prompt is independent. For N=3 this drops wall-clock from ~30-60s to ~10-20s.

Per-board call shape:
```json
higgsfield_enhancer({
  "flow": "ugc-clip",
  "inputs": {
    "K": <board index>,
    "N": <total boards>,
    "clip_duration": <4-15>,
    "arc_role": "<same as Step 5>",
    "monologue_segment": "<board K's spoken text, verbatim>",
    "input_tier": "<from Step 1.2>",
    "user_request": "<original brief verbatim>",
    "board_media_id": "<from Step 5>",
    "character_media_id": "<...>",
    "product_media_id": "<... or null>"
  },
  "image_refs": {
    "board_url": "<board K url from Step 5>",
    "character_url": "<from Step 1.5 or Step 2>",
    "product_url": "<from Step 3, or omit when no product>"
  }
})
```

Collect all N prompts before moving to Step 7.

### 7. Submit Seedance jobs in parallel

One higgsfield_generate_video call per board, all issued in parallel:
```json
higgsfield_generate_video({
  "params": {
    "model": "seedance_2_0",
    "prompt": "<board K prompt>",
    "duration": <per-clip>,
    "aspect_ratio": "9:16",
    "resolution": "1080p",
    "medias": [
      {"value": "<BOARD_K_MEDIA_ID>", "role": "image"},
      {"value": "<CHARACTER_MEDIA_ID>", "role": "image"},
      {"value": "<PRODUCT_MEDIA_ID>", "role": "image"}
    ]
  }
})
```

### 8. Poll, download, stitch (MANDATORY)

Poll each job. Download each clip to output/clip_<K>.mp4 (numeric suffix = board index).

Stitch:
- N == 1: copy output/clip_1.mp4 -> output/final.mp4. Done.
- N >= 2: invoke the montage skill. Pass clip paths in board order. Hard cuts only. No transitions.
  Pipeline is NOT complete until output/final.mp4 exists on disk.

### 9. Report

Only after output/final.mp4 exists on disk:
Show: output/final.mp4 path + total duration. Hide: job_ids, element_id, intermediate steps.

## ask_user_question rules

NEVER ask about: aspect ratio, resolution/quality, model selection, audio generation,
identity/Soul ID training, N boards/split, stitch/transitions.

DO ask (bundle into ONE call when multiple are missing):
- Duration: when brief did not specify. Offer 10s / 15s / 30s / 45s + Other.
- Product: when brief did not provide a product URL or photo.

## Critical rules

- Character identity locked via single character_media_id. No mid-pipeline regeneration.
- Product analysis happens ONCE in Step 1.1.
- Boards generated SEQUENTIALLY. Seedance clips submitted in ONE parallel batch.
- The agent passes structured inputs to higgsfield_enhancer -- never prose.
- For every image created or uploaded, capture BOTH (media_id, url).
- NO higgsfield_upload round-trip on generated outputs.
- Never compute or pass variety_rolls yourself.
- Cut markers appear only in Seedance prompts (enhancer output), never in board prompts.
- No greetings / re-introductions in clips 2..N.

## NOT for this workflow

- Product UGC variants -> ugc-product-flow, ugc-try-on-flow, ugc-tutorial-flow, ugc-unboxing-flow
- Cinematic / scripted long-form -> cinematic-flow
- Multiple separate UGC clips (3 x 10s ad variants) -> video-generation
