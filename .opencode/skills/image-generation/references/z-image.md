# Image Generation Reference - Z Image

Model ID: `z_image`

Best for: Fast stylized text-to-image, text-only input (caller-named only)

Supported aspect ratios: 1:1, 4:3, 3:4, 16:9, 9:16

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='z_image')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "z_image",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 4:3, 3:4, 16:9, 9:16>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/z-image.md")
```
