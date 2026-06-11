---
name: ugc-unboxing-flow
description: |
  A UGC-style unboxing video -- a creator opens a package on camera with the product reveal as the climax.
  Triggers only with ALL: (a) unboxing/reveal/first-reaction intent, (b) UGC framing, (c) a specific product.
---
# ugc-unboxing-flow

UGC-style unboxing pipeline. 4-slot boards (21:9). Enhancers: ugc-unboxing-board, ugc-unboxing-clip.
Canonical arc: PACKED -> REVEAL -> PRODUCT-FOCUS -> SATISFACTION.
Box disappears after Slot 2. Character does NOT open bag on camera.
Optional real package photo: upload it and pass package_media_id to board enhancer.
ask_user_question: also ask for real package photo (Yes/No) if not attached.
If Yes and no photo yet: fire follow-up asking user to attach, then HALT until received.
All other pipeline steps identical to ugc-flow (character, product upload, boards, Seedance, stitch).
NOT for: talking-head review (ugc-flow), try-on (ugc-try-on-flow), tutorial (ugc-tutorial-flow).
## Clip prompt reference
Clip prompt writer: skill_view("video-generation", "references/ugc-unboxing-clip-prompt.md")

## Shared pipeline steps (from ugc-flow)
1. Character: skill_view("image-generation", "references/soul-v2-ugc-character.md")
2. Product: URL -> skill_view("product-analyzer", "references/url-extract.md") | Photo -> skill_view("product-analyzer", "references/visual-analysis.md")
3. Package photo: ask_user_question if not provided (Yes/No). If Yes and missing: HALT until received.
4. Boards: higgsfield_enhancer(flow="ugc-unboxing-board", inputs={...}, image_refs={product_url, character_url, package_url})
5. Clips: higgsfield_enhancer(flow="ugc-unboxing-clip") -> higgsfield_generate_video(model="seedance_2_0")
6. Stitch: montage skill (concatenate boards + voiceover via ffmpeg)
