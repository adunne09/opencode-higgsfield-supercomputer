# Image Generation Reference - Soul Cinematic

Model ID: `soul_cinematic`

Best for: Cinematic stills - epic/film-style frames, mood frames. NEVER for text/typography.

Supported aspect ratios: 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='soul_cinematic')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "soul_cinematic",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3, 21:9>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/soul-cinematic.md")
```
