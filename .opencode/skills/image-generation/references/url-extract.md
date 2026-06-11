# Product URL Extraction (image-generation context)

See canonical reference: skill_view("product-analyzer", "references/url-extract.md")

When a product URL is provided in an image generation request:
1. Load product-analyzer/references/url-extract.md
2. Extract product data + images via web_extract
3. Download and visually verify product images
4. For designed pieces (ads, posters) use gpt_image_2 with references/gpt-image-2.md
