# Image Generation Reference - Nano Banana 2

Model ID: `nano_banana_2`

Best for: Cartoon/illustrated characters (2D/3D), heavily textured photos with strong grain/filter/sharpening

Supported aspect ratios: 1:1, 3:2, 2:3, 4:3, 3:4, 4:5, 5:4, 9:16, 16:9, 21:9

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='nano_banana_2')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "nano_banana_2",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 3:2, 2:3, 4:3, 3:4, 4:5, 5:4, 9:16, 16:9, 21:9>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/nano-banana-2.md")
```
