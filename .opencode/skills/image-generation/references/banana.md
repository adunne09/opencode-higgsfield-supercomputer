# Nano Banana 2 (nano_banana_2)

## Prompting

Text prompts with optional reference images. Flexible, instruction-following.
Reference images are addressed in the prompt as **"image_0.png"**, **"image_1.png"**,
… matching their order in the `medias` array.

Structure:
1. **Subject and scene** — what to generate.
2. **Style** — medium, lighting, color, visual direction.
3. **Instructions** — direct commands: "make the background darker", "remove the
   text", "change jacket to red".
4. **Constraints** — negatives: "no text", "no watermark", "avoid oversaturation".

Tips:
- Negative instructions are well-understood and respected.
- One clean prompt — no JSON wrapping.

## Generate

1. `higgsfield_generate_models_explore(action='get', model_id='nano_banana_2')`
2. `higgsfield_generate_image`

For image-to-image, add the reference in `medias`:
`{"value":"<id>","role":"image"}` (for a prior in-session generation, pass its job_id as `value`).
