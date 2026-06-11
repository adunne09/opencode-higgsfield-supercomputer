# Image Generation Reference - Flux 2

Model ID: `flux_2`

Best for: Versatile general-purpose flagship (caller-named only)

Supported aspect ratios: 1:1, 4:3, 3:4, 16:9, 9:16

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='flux_2')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "flux_2",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 4:3, 3:4, 16:9, 9:16>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/flux-2.md")
```
