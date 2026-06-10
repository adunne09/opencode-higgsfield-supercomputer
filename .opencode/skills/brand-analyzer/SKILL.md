---
name: brand-analyzer
description: |
  Extracts brand assets from a client website URL: logo, color palette (hex), typography, hero imagery, tagline, target audience.
  Output is the typed `client_brand` JSON object plus a local folder with downloaded asset files (logo + hero photos),
  ready to be uploaded as references in image/video generation.

  Run in parallel with `research-agent` when a brief contains a client URL plus downstream
  image/video persona generation. May also be invoked on-demand from `image-generation` / `video-generation` when their input
  contains a client URL but no pre-extracted `client_brand` payload.
---
# Brand Extract Skill

Pulls brand identity assets from a client homepage so downstream image/video generation can use real logo, real palette,
real typography, and real environment photos as references — instead of text-only descriptions that drift on color and
miss the logo entirely.

## When to invoke

- Brief contains a client URL (homepage of the brand) AND downstream task is image/video generation involving the brand
  (persona ads, marketing creatives, lifestyle shots, posters).
- HANDOFF payload is being assembled and `client_brand` is not yet populated.
- The image or video skill is asked to operate on a client URL with no attached `client_brand` payload.

Do NOT invoke for:
- Pure research with no generation downstream — `client_brand` is only useful for visual outputs.
- Briefs that already include a populated `client_brand` JSON in their delegation prompt — assume orchestrator
  pre-extracted it; do not re-fetch.
- Product page URLs (Amazon, Shopify, etc.) — those route to `product-analyzer/references/url-extract.md`.
  This skill is for BRAND homepages, not product pages.

## Output contract

A single JSON object plus a local asset folder.

```json
{
  "brand_slug": "planet-fitness",
  "source_url": "https://www.planetfitness.com/",
  "homepage_crop_local_path": "./assets/brand-pack-planet-fitness/homepage-crop.png",
  "palette_hex": ["#4B2E83", "#FFD700", "#FFFFFF"],
  "typography_font_family": "Montserrat, sans-serif",
  "typography_descriptor": "geometric sans-serif, modern, slightly condensed, medium weight",
  "typography_reference_url": "https://...screenshot.png",
  "hero_reference_urls": ["https://...", "https://..."],
  "hero_local_paths": ["./assets/brand-pack-planet-fitness/hero-1.jpg", "./assets/brand-pack-planet-fitness/hero-2.jpg"],
  "tagline": "The Judgement Free Zone",
  "target_audience": "everyday people, non-fitness-model demographic, judgement-free positioning",
  "key_differentiators": ["10/mo Classic", "no contracts", "no Lunks"]
}
```

Fields:
- `brand_slug` — kebab-case slug derived from domain or brand name.
- `source_url` — the URL that was extracted from.
- `homepage_crop_local_path` — **the primary brand reference**. A PNG screenshot of the top portion (top 1/4) of the brand homepage, taken via `searchcli read --format screenshot` (real headless browser bypasses Cloudflare/Akamai/anti-bot). The crop captures the actual logo (typically upper-left), brand colors as they render, real typography, hero copy, and the brand design language in one image. Downstream image-gen models use this as `medias[0]` / `images[0]` with the directive "use the logo and brand identity from this reference verbatim — do not invent a logo." Replaces the previous `logo_url` / `logo_local_path` approach (which struggled with SVG conversion and bot-protected logo URLs). If the screenshot step fails, this field is omitted and the pipeline silently degrades to palette and hero photos only.
- `palette_hex` — array of 2-6 hex codes representing the brand primary, accent, and supporting colors. ALWAYS lowercase 6-digit hex prefixed with `#`. Extraction is high-priority and never returns empty: prefer CSS variables / inline styles / explicit color tokens; if those are absent, infer dominant colors from hero imagery, header/button backgrounds, brand-name cues, and industry patterns. Last-resort fallback `["#000000", "#ffffff"]` is acceptable when nothing usable is determinable.
- `typography_font_family` — `font-family` value from CSS verbatim (the brand primary font stack). May be an obscure custom name like `A_L_L HOURS_DOORMAT` — kept as-is for traceability.
- `typography_descriptor` — REQUIRED 1-line plain-language description of the typography (weight + style + character). Example: `"bold condensed sans-serif, geometric, modern"`. This field is what downstream image/video prompts actually use as the Typography directive — `typography_font_family` alone is often unrenderable (custom font names mean nothing to a generation model). Always provide a descriptor even when `typography_font_family` is a known font like Helvetica or Inter.
- `typography_reference_url` — optional URL to a screenshot showing typography in use (header, hero text). May be empty string if only CSS font-family was extracted (no rendered screenshot available).
- `hero_reference_urls`, `hero_local_paths` — homepage hero images that show the brand real environment / real members / authentic settings. 2-4 images. Exclude product shots, abstract gradients, navigation icons.
- `tagline` — brand primary tagline if present on the homepage.
- `target_audience` — one-sentence description of who the brand markets to, derived from copy / "about" / hero messaging.
- `key_differentiators` — array of 3-6 short bullets capturing the brand unique selling points (price, philosophy, service-tier, etc.).

If a field cannot be reliably extracted, omit it from the JSON (do not fabricate). Required minimum: `brand_slug`, `source_url`, `palette_hex`, and at least one of {`homepage_crop_local_path`, `hero_reference_urls[0]`}.

## Pipeline

### 1. Derive slug + create local folder

Make sure you are inside a project (call `higgsfield_project_set` or `higgsfield_project_create` first — only the project folder is synced; `/tmp` is NOT). All brand-pack assets must live inside the project folder.

```bash
SLUG=$(echo "<brand_url>" | sed -E 's|https?://(www\.)?||;s|/.*||;s|\.[a-z]+$||' | tr '.' '-')
ASSET_DIR="./assets/brand-pack-${SLUG}"
mkdir -p "$ASSET_DIR"
```

The skill runs **three independent phases (Phase 1-3) followed by an end-state check (Phase 4)**. Each phase has its own retry/cascade. **Phases do NOT abort each other** — Phase 1 failing does not kill Phase 2, and vice versa. Phase 3 depends on Phase 1 hero URLs; if Phase 1 produces nothing, Phase 3 just runs empty. Phase 4 makes the final decision: success (emit normal JSON) vs. hard failure (emit `error: no_visual_brand_assets` envelope so the orchestrator can stop and ask the user).

### 2. Phase 1 — Text data extraction (cascade)

Goal: produce `palette_hex`, `typography_*`, `hero_reference_urls`, `tagline`, `target_audience`, `key_differentiators`. Try three sources in order. **First successful source wins.** A 401 / 403 / empty / timeout / anti-bot response from any tier means: continue to the NEXT tier within Phase 1, do NOT abort the whole pipeline.

```bash
# Tier A — fetchcli (server-side LLM extraction, fast, ~3-4KB output)
TEXT_JSON=$(./bin/fetchcli fetch   --url "<brand_url>"   --formats json   --prompt "Extract brand identity from this homepage. Return strict JSON with these fields: palette_hex (array of 3-6 hex color codes for primary brand colors, lowercase 6-digit format like '#aabbcc'. EXTRACTION ORDER: (1) try CSS variables, inline styles, or page color tokens first; (2) if no CSS hex codes are present, INFER the dominant brand colors from hero imagery dominant tones, header/button background colors, brand-name color cues, and industry patterns; provide your best-guess hex codes. ALWAYS return at least 2 hex codes — empty array is not acceptable; if completely unclear, return ['#000000', '#ffffff'] as last resort. Color extraction is high-priority — do not omit this field), typography_font_family (CSS font-family value verbatim, even if it is a custom or obscure font name), typography_descriptor (1-line plain-language description of the typography style: weight + style + character — e.g. 'bold condensed sans-serif, geometric, modern'. ALWAYS provide this), hero_reference_urls (2-4 URLs of large hero/marketing/lifestyle photos showing real people in real brand environments — EXCLUDE product shots, abstract gradients, icons, partner logos, footer images), tagline (brand primary tagline or hero headline if present), target_audience (one sentence), key_differentiators (array of 3-6 short bullets). Be strict on hero_reference_urls — omit if not clearly determinable. Be permissive on palette_hex and typography_descriptor — infer when CSS data is missing."   2>/dev/null) || TEXT_JSON=""

# Detect failure: empty output, anti-bot HTML challenge, or 401/403/4xx error envelope
if [ -z "$TEXT_JSON" ] || echo "$TEXT_JSON" | grep -qE '("error"|"unauthorized"|"Just a moment"|"status":4[0-9][0-9])'; then
  TEXT_JSON=""
fi

# Tier B — searchcli read (rendered markdown), then second LLM extraction pass
if [ -z "$TEXT_JSON" ]; then
  RENDERED_MD=$(./bin/searchcli read --url "<brand_url>" --output markdown -t 60 2>/dev/null) || RENDERED_MD=""
  TEXT_JSON="<extracted JSON from rendered markdown via second-pass extraction; empty if both options fail>"
fi

# Tier C — WebFetch (last resort)
if [ -z "$TEXT_JSON" ]; then
  TEXT_JSON="<WebFetch extracted JSON; empty if blocked/failed>"
fi
```

### 3. Phase 2 — Homepage screenshot (the brand-identity anchor)

Goal: produce `$ASSET_DIR/homepage-crop.png` — a top-1/4 PNG screenshot of the brand homepage. This is the **most critical** asset because it carries logo + brand colors + typography + hero copy + design language in one image. Phase 2 runs **independent of Phase 1**.

```bash
SCREENSHOT_OK=0
for attempt in 1 2; do
  ./bin/searchcli read     --url "<brand_url>"     --format screenshot     -o markdown     -t 120     --output-file "$ASSET_DIR/_homepage-raw.bin"     2>/dev/null && SCREENSHOT_OK=1 && break
  sleep 2
done

if [ "$SCREENSHOT_OK" = "1" ] && [ -s "$ASSET_DIR/_homepage-raw.bin" ]; then
  python3 -c "
from io import BytesIO
from PIL import Image
raw = open('\/_homepage-raw.bin', 'rb').read()
sig = b'\x89PNG\r\n\x1a\n'
idx = raw.find(sig)
if idx >= 0:
    img = Image.open(BytesIO(raw[idx:]))
    w, h = img.size
    img.crop((0, 0, w, h // 4)).save('\/homepage-crop.png', optimize=True)
  "
fi
rm -f "$ASSET_DIR/_homepage-raw.bin" 2>/dev/null || true
```

### 4. Phase 3 — Hero image downloads

```bash
for i in 1 2 3 4; do
  HERO_URL="<hero_url_i_from_TEXT_JSON; skip if absent>"
  [ -z "$HERO_URL" ] && continue
  curl -sL     -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36"     --max-time 30     -o "$ASSET_DIR/hero-${i}.jpg" "$HERO_URL" 2>/dev/null || true
done
```

### 5. Phase 4 — End-state check + emit final JSON

```bash
HAS_HOMEPAGE_CROP=0; [ -s "$ASSET_DIR/homepage-crop.png" ] && HAS_HOMEPAGE_CROP=1
HAS_HERO=0
for f in "$ASSET_DIR"/hero-*.jpg; do
  [ -s "$f" ] && HAS_HERO=1 && break
done
HAS_REAL_PALETTE=0
# parse TEXT_JSON.palette_hex in the agent — set HAS_REAL_PALETTE accordingly
```

**Hard failure condition:** `HAS_HOMEPAGE_CROP == 0` AND `HAS_HERO == 0` AND `HAS_REAL_PALETTE == 0`. Emit error envelope:

```json
{
  "error": "no_visual_brand_assets",
  "brand_slug": "<slug>",
  "source_url": "<url>",
  "tiers_attempted": { "text_extraction": {...}, "screenshot": "...", "hero_downloads": "..." },
  "messages": ["fetchcli returned 401 (auth)", "searchcli timed out after 120s on both attempts", "WebFetch returned anti-bot challenge"]
}
```

Otherwise emit the normal `client_brand` JSON.

## Failure handling

| Failure | Phase | Behavior |
|---------|-------|----------|
| fetchcli returned 401 / 403 / 4xx / empty / anti-bot HTML | Phase 1 (Tier A) | Fall through to Tier B. Do NOT abort. |
| searchcli read empty / timeout | Phase 1 (Tier B) | Fall through to Tier C. Do NOT abort. |
| WebFetch blocked / empty | Phase 1 (Tier C) | Phase 1 ends with empty TEXT_JSON. Phase 2 + Phase 3 still run. |
| searchcli screenshot timeout (1st attempt) | Phase 2 | Retry once with 2s gap. |
| searchcli screenshot timeout after retry | Phase 2 | homepage-crop.png not produced. Phase 3 + Phase 4 still run. |
| PIL not installed / malformed PNG / missing signature | Phase 2 | homepage-crop.png not produced. Phase 3 + Phase 4 still run. |
| Hero download failed (404 / network / anti-bot) | Phase 3 | Drop hero_local_paths entry for that URL; keep hero_reference_urls entry so downstream agents can retry. |
| All three signals are 0 | Phase 4 | Hard failure. Emit error envelope. Orchestrator stops pipeline and asks the user. |
| At least one signal is 1 | Phase 4 | Normal success path. Emit client_brand JSON. |
| URL is a product page, not brand homepage | preflight | Refuse — instruct caller to use product-analyzer/references/url-extract.md instead. |
| URL invalid / unreachable | preflight | Return {"error": "url_unreachable"}. |

## Notes

- This skill does NOT generate creative output. It only extracts and downloads. Generation is downstream.
- This skill does NOT verify legal rights to use the logo/imagery — that is the caller responsibility.
- One brand URL = one extraction call. Cache the result in $ASSET_DIR/brand-pack.json so repeated invocations within the same conversation reuse the same pack instead of re-fetching.
