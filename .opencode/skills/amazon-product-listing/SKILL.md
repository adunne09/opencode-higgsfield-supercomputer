---
name: amazon-product-listing
description: |
  AI Employee: Amazon Listing Designer.
  An Amazon-ready marketplace listing image set from your product photo or URL — a compliant main image (pure white, Suppressed-Listing-safe), the 5 secondary images and the 7 A+ Brand Content modules.
  Triggers: "Amazon listing", "Amazon product images", "main image", "secondary images", "A+ content/page", "infographic/lifestyle for Amazon", "Suppressed Listing", "Amazon compliance".
---
# Amazon Product Listing Designer

Turns a single product photo or product URL into a full set of Amazon-compliant marketplace images: main image, secondary images (infographics, multi-angle, detail shots, lifestyle, what’s in box), and A+ Brand Content modules. All generation happens through `higgsfield_generate_image` using the `nano_banana_2` model.

## When to use

- User provides a product image or URL and asks for Amazon images
- User mentions "main image", "secondary images", "A+ content", "A+ page", "Amazon listing"
- User wants product infographics, lifestyle shots, or feature callouts for e-commerce

## When NOT to use

- Generic product photography (no Amazon context) -- use product-photoshoot
- Social media content -- use a UGC or motion design skill
- Video generation -- this skill is image-only

## Workflow (4 steps)

### Step 1 -- Autonomous product analysis

Analyze the input without asking questions first.

If input is an image: examine visually; identify product name, brand, category, packaging, colors, features.
If input is a URL: use web_extract to retrieve the page. Extract product title, brand, description, key features, photos.

Extract: product name, brand, category (beverage/skincare/electronics/apparel/food/etc), packaging type, visual characteristics, key selling points, target user.

### Step 2 -- Present the plan

Print a brief "What I see" summary, then ask scope via ask_user_question unless the brief already locked scope or used auto-approve language.

Scope options: Complete set (13 images), Product images only (6), A+ page only (8), Just the main image.

### Step 3 -- Handle corrections

If user corrects product analysis, update understanding and apply to ALL subsequent prompts.

### Step 4 -- Generation (correct order)

Critical order: Main image ALWAYS first. It establishes the visual baseline.

1. Upload product reference via higgsfield_upload
2. Generate main image via higgsfield_generate_image (nano_banana_2, 1:1, resolution 2k)
3. Poll main image to completed -- MANDATORY before downstream
4. Generate all downstream images in parallel, each referencing the main image job_id in medias
5. Poll and present each result as it completes

## Model and Specs

Model: nano_banana_2 ONLY.

| Image type | aspect_ratio |
|---|---|
| Main image | 1:1 |
| All secondary images | 1:1 |
| A+ Hero Banner (Module 1) | 21:9 |
| A+ Modules 2-6 | 3:2 |
| A+ Brand Endorsement (Module 7) | 21:9 |

Resolution: all images at resolution 2k. Never specify width/height in pixels.

## Generation calls

Main image:
```
higgsfield_generate_image(requests=[{
  "model": "nano_banana_2",
  "prompt": "[MAIN_IMAGE_PROMPT]",
  "aspect_ratio": "1:1",
  "count": 1,
  "medias": [{"value": "$PRODUCT_UPLOAD_ID", "role": "image"}]
}])
```

Downstream images (each dispatched in parallel):
```
higgsfield_generate_image(requests=[{
  "model": "nano_banana_2",
  "prompt": "[IMAGE_PROMPT]",
  "aspect_ratio": "<per type>",
  "count": 1,
  "medias": [{"value": "$MAIN_JOB_ID", "role": "image"}]
}])
```

## Multi-image consistency rules

1. Main image first -- always. It is the visual anchor.
2. Reference main image -- every downstream prompt includes the main job_id in medias.
3. Consistent product appearance -- color, material, shape, label must be identical.
4. Unified visual style -- pick ONE color palette, font style, icon style for the whole set.

## Main image rules (Amazon compliance)

- Pure white background (#FFFFFF)
- Product fills 85% of frame
- No text, graphics, watermarks, or borders
- No props, accessories, or lifestyle elements
- Product must be the actual product (not illustration)
- No mannequins for apparel (except socks/hosiery)

## Secondary images (5 types)

1. Infographic -- key features with callout text
2. Multi-angle -- product from different views
3. Detail shot -- close-up of texture, material, craftsmanship
4. Lifestyle -- product in real-world use context
5. What’s in the box -- all included items laid out

## A+ Brand Content (7 modules)

1. Hero Banner (21:9) -- brand story header
2. Pain Points (3:2) -- problem the product solves
3. Features (3:2) -- key product features grid
4. Ingredients/Materials (3:2) -- what it’s made of
5. Efficacy/Results (3:2) -- proof of performance
6. How To Use (3:2) -- step-by-step usage guide
7. Brand Endorsement (21:9) -- brand trust closer

## Iteration guidance

When user rejects an image:
- Text/wording issue -- regenerate ONLY that image
- Product appearance wrong -- regenerate main image FIRST, then re-batch all downstream
- Background/scene wrong -- regenerate only the problem image
- Style drift -- re-batch affected items, keep main image

## Pinned defaults (never ask)

- Model: nano_banana_2
- Resolution: 2k
- Aspect ratios per image type (see table above)
- Generation order: main first, then parallel downstream
- Amazon compliance rules
