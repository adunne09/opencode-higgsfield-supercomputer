# Image Generation Reference - Soul 2.0

Model ID: `text2image_soul_v2`

Best for: New original character/person - UGC creator, influencer, editorial, fashion, vibe-driven portraits. NEVER for text/typography.

Supported aspect ratios: 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='text2image_soul_v2')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "text2image_soul_v2",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/text2image-soul-v2.md")
```
