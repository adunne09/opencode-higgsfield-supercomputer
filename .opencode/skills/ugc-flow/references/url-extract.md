# Product URL Extraction

Canonical location: skill_view("product-analyzer", "references/url-extract.md")

This reference describes how to extract product data from e-commerce URLs (Amazon, Shopify, AliExpress)
using web_extract with extract_prompt. Returns structured JSON with product name, brand, price,
description, specs, and curated gallery images.

Key steps:
1. web_extract(urls=["USER_URL"], extract_prompt="Extract product name, brand, price, description, specs, and ALL product image URLs...")
2. Filter: keep main_gallery + description images, discard review/other, keep up to 5
3. Download each with User-Agent header (required for Amazon CDN): curl -sL -A "Mozilla/5.0..." -o ./assets/product_N.jpg "URL"
4. Verify file size > 10KB (CDN placeholder = blocked). Discard faces; keep product-only images.
5. Build 150-300 word product description (name -> what it is -> selling points -> specs -> audience)

See full spec: skill_view("product-analyzer", "references/url-extract.md")
