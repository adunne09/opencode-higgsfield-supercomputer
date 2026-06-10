---
name: podcast-flow
description: |
  AI podcast / interview video. 5 mandatory steps: (1) Hero plates soul_cinematic 3:4, (2) Empty location soul_cinematic 16:9, (3) Seated composite gpt_image_2 16:9 medias=[location,host1..hostN], (4) Storyboard gpt_image_2 16:9 strict B/W medias=[composite,host1..hostN], (5) Seedance chunks seedance_2_0 16:9 1080p 8-15s medias=[storyboard,composite,speaker_hero,listener_hero]. Read references/podcast-pipeline.md first. NEVER skip any step. NEVER use text-only Seedance for multi-chunk or >15s.
---

# podcast-flow

AI podcast / interview video. 5 mandatory steps: (1) Hero plates soul_cinematic 3:4, (2) Empty location soul_cinematic 16:9, (3) Seated composite gpt_image_2 16:9 medias=[location,host1..hostN], (4) Storyboard gpt_image_2 16:9 strict B/W medias=[composite,host1..hostN], (5) Seedance chunks seedance_2_0 16:9 1080p 8-15s medias=[storyboard,composite,speaker_hero,listener_hero]. Read references/podcast-pipeline.md first. NEVER skip any step. NEVER use text-only Seedance for multi-chunk or >15s.
