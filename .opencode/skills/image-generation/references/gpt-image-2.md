# Image Generation Reference - GPT Image 2.0

Model ID: `gpt_image_2`

Best for: Default for design, typography, posters, banners, ads, thumbnails, infographics, editing existing images, product/e-commerce/lifestyle

Supported aspect ratios: 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3

## Before generating

Call `higgsfield_generate_models_explore(action='get', model_id='gpt_image_2')` to load
live parameters (quality tiers, resolution options, sub_model, medias roles, etc.).

## Tool call

```
higgsfield_generate_image(requests=[{
  "model": "gpt_image_2",
  "prompt": "<prompt>",
  "aspect_ratio": "<one of: 1:1, 4:3, 3:4, 16:9, 9:16, 3:2, 2:3>",
  // add other params from models_explore
}])
```

For full prompt-craft guidance, call:
```
skill_view(name="image-generation", file_path="references/gpt-image-2.md")
```
