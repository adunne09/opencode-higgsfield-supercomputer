# Image Generation Reference - Soul Cast

Model ID: `soul_cast`

Best for: Character creation for reuse in video - output saved as a character element, referenced via <<<element_id>>> in later shots. NOT for standalone delivery images.

Supported aspect ratios: 16:9

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='soul_cast')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "soul_cast",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 16:9>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/soul-cast.md")
```
