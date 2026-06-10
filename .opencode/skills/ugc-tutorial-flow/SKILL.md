---
name: ugc-tutorial-flow
description: |
  A UGC-style tutorial video -- a creator demonstrates step-by-step use of a specific product.
  Triggers only with ALL: (a) tutorial/how-to/step-by-step intent, (b) UGC framing, (c) a specific product.
---
# ugc-tutorial-flow

UGC-style tutorial pipeline. 4-slot boards (21:9). Each slot = one physical product usage step.
Step captions: format "Step N -- Heading", English Title Case, en-dash separator.
Total tutorial steps = 4 x N_boards. Global step numbering across the whole video.

References (MANDATORY -- use only these, no substitutes):
- Character: skill_view("image-generation", "references/soul-v2-ugc-character.md")
- Board: skill_view("image-generation", "references/ugc-tutorial-boards.md")
- Clip prompt: skill_view("video-generation", "references/ugc-tutorial-clip-prompt.md")
- Product URL: skill_view("product-analyzer", "references/url-extract.md")
- Product photo: skill_view("product-analyzer", "references/visual-analysis.md")

Product Usage Analysis (mandatory before any board generation):
Build total_step_count = 4 x N realistic chronological usage steps.
Output: step_captions[1..total_step_count] array. Pass per-board group of 4 to each board call.

Board generation: gpt_image_2, aspect_ratio "21:9".
Clip enhancer: OpenRouter LLM with ugc-tutorial-clip-prompt.md system prompt.
Pass arc_role="BOARD_TUTORIAL_STEPS" for every board.
Pass is_last_board=true ONLY when K == N.

CTA tail (mandatory, video-level only): final ~0.5-1s of final cut = talking-head selfie + downward
hand gesture + short English CTA ("Link in bio" / "Follow me" / "Subscribe!").
NOT a separate slot. NOT rendered on board image. Only added when is_last_board=true.

Weight/grip rules: heavy items = two hands + facial strain; light = one hand relaxed; tiny = pinched.
English-only output everywhere (captions, monologue, CTA, all prompts).

All other pipeline steps (persona source, character gen, product upload, board sequencing,
Seedance parallel jobs, poll/download/stitch) identical to ugc-flow.

NOT for: talking-head review (ugc-flow), unboxing (ugc-unboxing-flow), try-on (ugc-try-on-flow), cinematic.
