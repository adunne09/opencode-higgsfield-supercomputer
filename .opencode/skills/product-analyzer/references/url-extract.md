# Product URL Extraction

When user provides a product URL (Amazon, Shopify, AliExpress, any e-commerce), extract product data automatically before generation. **Do NOT pause for user confirmation** -- if the request contains a URL + generation intent, run extraction then proceed immediately.

## Step 1: Extract structured product data + images

Use the native `web_extract` tool with `extract_prompt` to extract product data and gallery images in a single call. This returns structured JSON (Firecrawl backend) -- no separate markdown fetch needed.

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

**CRITICAL: Always use User-Agent when downloading images.** Without it, Amazon and other CDNs return a tiny placeholder (9 bytes) instead of the real image -- causing "Could not process image" error.

Make sure you're inside a project (`higgsfield_project_set` / `higgsfield_project_create`) so the download lands inside the synced project folder. `/tmp` is NOT synced -- never download there.

```bash
curl -sL -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" -o ./assets/product_N.jpg "IMAGE_URL"
# Verify file is valid before reading:
ls -lh ./assets/product_N.jpg  # must be > 10KB, otherwise URL is blocked
# Then Read ./assets/product_N.jpg
```

- DISCARD: human faces visible
- DISCARD: different color/variant than page listing
- DISCARD: file smaller than 10KB (CDN placeholder, not real image)
- KEEP: product only (hands OK, faces not)

## Step 4: Build product description

- 150-300 words, informative not salesy
- Structure: Name + brand -> what it is -> key selling points (3-5) -> specs -> who it's for
- Include: dimensions, weight, materials, compatibility

## Step 5: Proceed to generation

Use extracted images in the `medias` array (upload with `--force-ip-check`). Use extracted description to inform the video prompt.
