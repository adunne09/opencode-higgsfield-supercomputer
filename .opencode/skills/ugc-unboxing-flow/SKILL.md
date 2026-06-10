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
