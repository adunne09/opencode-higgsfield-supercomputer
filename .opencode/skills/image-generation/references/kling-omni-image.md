# Image Generation Reference - Kling Omni Image

Model ID: `kling_omni_image`

Best for: Photorealistic generation with strong input-image support (caller-named only)

Supported aspect ratios: auto, 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 21:9

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='kling_omni_image')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "kling_omni_image",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: auto, 1:1, 16:9, 9:16, 4:3, 3:4, 3:2, 2:3, 21:9>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/kling-omni-image.md")
```
