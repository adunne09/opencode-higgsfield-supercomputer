# Image Generation Reference - Seedream V4.5 / V5 Lite

Model ID: `seedream_v4_5`

Best for: Face edits on real photographs (identity stays intact), strong at vector graphics. NOT for from-scratch cartoon generation.

Supported aspect ratios: 1:1, 4:3, 16:9, 3:2, 21:9, 3:4, 9:16, 2:3 (V4.5); 1:1, 16:9, 9:16, 4:3, 3:4 (V5 Lite)

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='seedream_v4_5')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "seedream_v4_5",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 4:3, 16:9, 3:2, 21:9, 3:4, 9:16, 2:3 (V4.5); 1:1, 16:9, 9:16, 4:3, 3:4 (V5 Lite)>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/seedream-v4-5.md")
```
