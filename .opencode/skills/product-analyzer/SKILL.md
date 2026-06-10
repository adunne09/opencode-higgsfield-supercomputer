---
name: product-analyzer
description: |
  Product input preprocessing for media workflows. Extracts product data from
  e-commerce URLs (Amazon, Shopify, AliExpress, any product page) via web_extract,
  or analyzes product photos to identify category, usage mechanic, and opening
  mechanic. Output feeds downstream image / video generation workflows that take
  a product as input.
  Use for: "extract product from <URL>", "parse this product page", "analyze
  this product photo", any product URL or product photo arriving as workflow input.
  NOT for: trend / viral / creator research (use research/trend-picker),
  source video analysis (use workflow-generation/video-adapt),
  brand asset extraction from a brand homepage (use analyzer/brand-analyzer),
  generation itself (use image-generation / video-generation / workflow-generation).
---
# Product Analyzer

Utility skill. Two modes — pick the one that matches your input, then load the matching reference via `skill_view`.

## When to use

Called by media workflows (e.g. `workflow-generation/ugc-flow`) at the input-parsing stage, before any generation. You typically do NOT invoke this skill directly from the user — it is a building block.

## Modes

| Input | Reference to load | What you get |
|---|---|---|
| Product URL (Amazon / Shopify / AliExpress / any e-commerce page) | `references/url-extract.md` | Structured product data (name, brand, price, variant, description, specs) + 3-5 curated product images filtered visually |
| Product photo only (no URL, no description) | `references/visual-analysis.md` | Product category + usage mechanic + opening mechanic + key visual details, ready for prompt building |

Load only the reference matching your case — do not pull both.

```
URL   -> skill_view("product-analyzer", "references/url-extract.md")
Photo -> skill_view("product-analyzer", "references/visual-analysis.md")
```

## NOT for this skill

- Trend / viral / creator research -> `research/trend-picker`.
- Source video analysis for adaptation -> `workflow-generation/video-adapt`.
- Brand asset extraction (logo, color palette from a brand homepage) -> `analyzer/brand-analyzer`.
- Image / video generation -> `media/image-generation`, `media/video-generation`, or a `workflow-generation/*` flow.

---

## references/url-extract.md

# Product URL Extraction

When user provides a product URL (Amazon, Shopify, AliExpress, any e-commerce), extract product data automatically before generation. **Do NOT pause for user confirmation** — if the request contains a URL + generation intent, run extraction then proceed immediately.

## Step 1: Extract structured product data + images

Use the native `web_extract` tool with `extract_prompt` to extract product data and gallery images in a single call. This returns structured JSON (Firecrawl backend) — no separate markdown fetch needed.

**CRITICAL: Do NOT also request markdown.** Marketplace pages produce 100KB+ of markdown that will truncate the output. `extract_prompt` alone returns product info AND image URLs in ~2-3KB.

```
web_extract(
    urls=["USER_URL"],
    extract_prompt="Extract product name, brand, price, currency, selected color/variant, full description, all feature bullet points, technical specifications, and ALL product image URLs from the main gallery (front, side, back, close-up, packaging). Classify each image by section: main_gallery, description, review, other. Return 3-8 unique product gallery images. EXCLUDE user review photos, related products, navigation, banners, color swatch icons, size charts, logos."
)
```

The response `content` field contains the stringified JSON with the structured product data including curated image URLs.

## Step 2: Filter images

1. DISCARD: `section: "review"` (user UGC)
2. DISCARD: `section: "other"` (navigation, banners)
3. KEEP: `section: "main_gallery"` (priority) + `section: "description"`
4. Keep up to **5** best images

## Step 3: Visual filter (Claude reads each image)

**CRITICAL: Always use User-Agent when downloading images.** Without it, Amazon and other CDNs return a tiny placeholder (9 bytes) instead of the real image — causing "Could not process image" error.

Make sure you are inside a project (`higgsfield_project_set` / `higgsfield_project_create`) so the download lands inside the synced project folder. `/tmp` is NOT synced — never download there.

```bash
curl -sL -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" -o ./assets/product_N.jpg "IMAGE_URL"
# Verify file is valid before reading:
ls -lh ./assets/product_N.jpg  # must be > 10KB, otherwise URL is blocked
# Then read ./assets/product_N.jpg
```

- DISCARD: human faces visible
- DISCARD: different color/variant than page listing
- DISCARD: file smaller than 10KB (CDN placeholder, not real image)
- KEEP: product only (hands OK, faces not)

## Step 4: Build product description

- 150-300 words, informative not salesy
- Structure: Name + brand -> what it is -> key selling points (3-5) -> specs -> who it is for
- Include: dimensions, weight, materials, compatibility

## Step 5: Proceed to generation

Use extracted images in the `medias` array. Use extracted description to inform the video prompt.

---

## references/visual-analysis.md

# Product Visual Analysis (no URL case)

**When the user provides only a product photo (no URL, no description) — MANDATORY: visually analyze the image before building any prompt.**

Read the product image and identify:

1. **Product category** — what is it exactly? (perfume, face cream, lip balm, shampoo, body lotion, foundation, mascara, sunscreen, serum, hair oil, supplement capsules, protein powder, etc.)
2. **Usage mechanic** — how is it physically used?
   - Perfume / spray bottle -> **spray** (press nozzle -> mist comes out)
   - Tube (cream, gel, toothpaste) -> **squeeze + apply** with fingers
   - Pump bottle (serum, lotion, soap) -> **press pump** -> dispense onto fingers -> apply
   - Lipstick / lip gloss -> **swipe** directly on lips
   - Mascara -> **brush** applied to lashes
   - Powder / compact -> **brush or sponge** pressed on skin
   - Dropper / serum with pipette -> **drop** onto fingertips -> press into skin
   - Jar (cream, mask) -> **scoop** with fingers -> apply
   - Capsule / pill / gummy -> **swallow** or **chew**
   - Powder sachet / scoop -> **mix** into liquid
3. **Opening mechanic** — how does it open? (uncap, unscrew, pull tab, flip top, press pump — this must be shown BEFORE contents exit the container)
4. **Key visual details** — color, shape, material, label, any distinctive features to preserve in the prompt

**Use this analysis to write the Dynamic Description accurately.** Never default to "applies cream" or generic skincare interaction — describe the exact mechanic that matches the identified product type.
