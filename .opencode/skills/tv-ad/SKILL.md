---
name: tv-ad
description: |
  Generates high grade 15 second 16:9 format video ads and commercials.
  Trigger: tv ad, tv commercial, premium commercial, create an ad, make an ad.
  NOT for: UGC videos, social media videos, non-commercial videos.
---
# TV Ad -- Producer / Router

Premium 15-second TV commercials at 16:9. You are the Producer (Router).
Manage control flow, gating, asset tracking, polling, and user communication.
All creative prompts are produced by backend enhancers via higgsfield_enhancer.

Three formats: problem-solution (VO narration), lifestyle (hero on-camera), high-energy (kinetic + FX).
Duration always 15s. Aspect ratio always 16:9. Seedance resolution always 1080p.

## Enhancer flows + analyzers

| Step | Resource | How to call |
|---|---|---|
| Brand from URL | brand-analyzer skill | skill_view("brand-analyzer", ...) |
| Product from URL | product-analyzer/references/url-extract.md | skill_view("product-analyzer", "references/url-extract.md") |
| Product from photo | product-analyzer/references/visual-analysis.md | skill_view("product-analyzer", "references/visual-analysis.md") |
| Script (script_text) | text-generation/references/ad-script.md | skill_view("text-generation", "references/ad-script.md") |
| Hero avatar prompt | backend | higgsfield_enhancer(flow="tv-ad-character", inputs={...}, image_refs={...}) |
| Location prompt | backend | higgsfield_enhancer(flow="tv-ad-location", inputs={...}, image_refs={...}) |
| Seedance prompt | backend | higgsfield_enhancer(flow="tv-ad-seedance", inputs={...}, image_refs={...}) |

tv-ad-script returns {"prompt": "script as plain structured text"} -- use result.prompt directly.

## Dual-channel media tracking

For every image: capture BOTH (value -> medias[].value) AND (url -> enhancer image_refs).

| Source | medias[].value | url |
|---|---|---|
| higgsfield_upload (product / user hero / brand crop) | uploads[i].id | uploads[i].url |
| text2image_soul_v2 hero (Stage IV) | job_id | result.url |
| gpt_image_2 location (Stage V) | job_id | result.url |

NO higgsfield_upload round-trip on generated outputs.

## Pipeline

Stage I    -- Inputs (ask_user_question -- creative gaps only)
Stage II   -- Brand / Product (analyzers + upload + is_screen_bearing flag)
Stage III  -- Script (ad-script ref -> tv-ad-script -> script_text -> GATE I: script approval)
Stage IV   -- Character (skip if user-provided; else tv-ad-character + Soul gen -- auto-proceed)
Stage V    -- Location (tv-ad-location + gpt_image_2 -- auto-proceed)
Stage VI   -- Video (tv-ad-seedance + seedance_2_0 -> GATE II: video approval)
Stage VII  -- Deliver

Two approval gates: script (Stage III) and final video (Stage VI).
Character and location auto-proceed.
Autonomous mode: skip both gates (user says "auto-approve" / "no approvals").

## Stage I -- Inputs

Capture: ad_type (Problem-Solution / Lifestyle / High Energy), product source, hero source,
tone (skip for high-energy), user_creative_brief (optional), variant_count (default 1).

NEVER ask about: aspect ratio (16:9), duration (15s), resolution (1080p), model selection,
audio, packshot type, pipeline internals.

## Stage II -- Brand and Product

- URL pasted -> classify: brand homepage -> brand-analyzer; product page -> product-analyzer url-extract.
- Product photo uploaded -> product-analyzer visual-analysis.
- Generate product -> image-generation skill for studio shot.

Set is_screen_bearing: true if product has a visible display (phone/tablet/laptop/watch/etc.).

## Stage III -- Script -> GATE I

Load: skill_view("text-generation", "references/ad-script.md")
Pass to tv-ad-script enhancer: {ad_type, tone, duration_seconds: 15, user_creative_brief,
client_brand, product_dna, is_screen_bearing, character_provided} + image_refs.
script_text = result.prompt (NO JSON.parse -- already human-readable).

GATE I: show script_text, ask user: proceed / adjust dialogue / adjust scenes / rewrite.
On adjust: re-call tv-ad-script with the change.

## Stage IV -- Character (auto-proceed)

If user attached hero photo: higgsfield_upload -> (character_value, character_url). SKIP generation.

Otherwise:
higgsfield_enhancer(flow="tv-ad-character", inputs={script_text, gender, client_brand_palette_hex,
  product_visible_colors, user_overrides}, image_refs={product_url, brand_url})

higgsfield_generate_image(model="text2image_soul_v2", aspect_ratio="3:4")
Poll -> capture (character_value, character_url) = (job_id, result.url). Auto-proceed.

## Stage V -- Location (auto-proceed)

higgsfield_enhancer(flow="tv-ad-location", inputs={script_text, client_brand, product_visible_colors},
  image_refs={product_url, brand_url})

higgsfield_generate_image(model="gpt_image_2", aspect_ratio="16:9")
Poll -> capture (location_value, location_url) = (job_id, result.url). Auto-proceed.

## Stage VI -- Video -> GATE II

higgsfield_enhancer(flow="tv-ad-seedance", inputs={script_text, is_screen_bearing, has_brand_ref},
  image_refs={location_url, product_url, character_url, brand_url})

The enhancer uses @Image1=location, @Image2=product, @Image3=hero, @Image4=brand.
medias[] MUST be in that exact order:

higgsfield_generate_video(model="seedance_2_0", duration=15, aspect_ratio="16:9", resolution="1080p",
  count=variant_count, medias=[
    {value: location_value, role: "image"},
    {value: product_value, role: "image"},
    {value: character_value, role: "image"},
    {value: brand_value, role: "image"}  // only when brand crop present
  ])

GATE II: present video(s), ask user: perfect/deliver / adjust pacing / adjust performance
/ regenerate same / go back to script.

## Stage VII -- Deliver

Ensure all final assets are saved. Report: ad type, 15s, 16:9, variant count, video URL(s).
Hide job_ids and intermediate steps.

## Critical invariants

- Format is fixed: 15s, 16:9, 1080p. Not user-configurable.
- Two gates only: script + final video. Character + location auto-proceed.
- Seedance medias[] order is fixed: location, product, character, (brand).
- NO higgsfield_upload round-trip on generated outputs.
- Never regenerate the hero mid-pipeline (identity locked).
- is_screen_bearing decided once in Stage II.
- Save every generated asset as an artifact immediately after generation.

## NOT for this workflow

- UGC unboxing / try-on / talking-head review -> ugc-* flows
- Cinematic / scripted narrative (>15s, non-commercial) -> cinematic-flow
- Multiple separate ad variants -> video-generation
