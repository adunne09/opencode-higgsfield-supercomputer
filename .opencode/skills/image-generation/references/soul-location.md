# Image Generation Reference - Soul Location

Model ID: `soul_location`

Best for: Location/environment for video pipelines - saved as an environment element. Best for cinematic-location requests.

Supported aspect ratios: 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9, 9:21

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='soul_location')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "soul_location",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9, 9:21>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/soul-location.md")
```
