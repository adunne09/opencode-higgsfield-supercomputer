# Image Generation Reference - Grok Image

Model ID: `grok_image`

Best for: Expressive, high-contrast generation; best for NSFW (caller-named only)

Supported aspect ratios: 1:1, 4:3, 3:4, 16:9, 9:16

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='grok_image')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "grok_image",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 4:3, 3:4, 16:9, 9:16>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/grok-image.md")
```
