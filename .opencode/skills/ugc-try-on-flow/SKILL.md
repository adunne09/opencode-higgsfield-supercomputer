---
name: ugc-try-on-flow
description: |
  A UGC-style try-on video -- a creator wears and poses with a specific wearable product.
  Triggers only with ALL: (a) try-on/wearing/OOTD/fit-check intent, (b) UGC framing, (c) a specific wearable product.
---
# ugc-try-on-flow

UGC-style try-on pipeline. 4-slot boards (21:9). Enhancers: ugc-try-board, ugc-try-clip.
Canonical arc (Board 1): PRE_WEAR -> WEARING -> TEXTURE_CLOSEUP -> STYLE_POSE.
Kraft bag in Slot 1 only (plain brown, no logo). Product outfit worn from Slot 2 onward.
Multi-board arc_roles: K=1 CANONICAL, K=2 HOME_TOUR, K=3 OUTDOOR (light rain), K=4 HOME_REFLECT, K>=5 LOOP.
Audio model: Cuts 1/2/4 = on-camera dialogue (lip-sync). Cut 3 = voiceover only (hand-free macro).
Twirl beat: mandatory in Board 1 / Cut 2 ONLY. One spin only.
Hand-free macro: NO hand contact with product fabric in Slot 3.
Garment Consistency Lock: silhouette/color/print identical across all cuts.
No mirrors or reflections anywhere. No CTA tail. No on-screen costume change.
Default monologue frame: personal-want mini-story (not generic praise).
All other pipeline steps identical to ugc-flow (character, product upload, boards, Seedance, stitch).
NOT for: unboxing (ugc-unboxing-flow), talking-head review (ugc-flow), tutorial (ugc-tutorial-flow).
