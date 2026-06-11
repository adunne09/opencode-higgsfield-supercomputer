# Seedream (seedream_v4_5 / seedream_v5_lite)

Excels at **face identity preservation** from reference photos, compositing from
multiple references, product placement, and text transfer between images. Prompt
is always required, even in edit mode with references.

## Prompting

Reference images are addressed as **"Image0"**, **"Image1"**, … matching their
order in the `medias` array (up to 10). Instruction-based — tell the model what
to do with the inputs:
1. **Action** — "Replace the product in Image1 with that from Image2", "Place the
   person from Image1 into the scene from Image2".
2. **Subject** — for face preservation: which figure has the face, how the person
   should appear.
3. **Typography** — for text transfer: "Copy the text from Image3 to the top",
   with contrast and placement.
4. **Style** — lighting, atmosphere, palette.
5. **Constraints** — "do not change the background", "keep original lighting".

Tips:
- Multi-image composition: reference elements from different images freely.
- Prompt always required — even with `medias`, describe what to do.
- Best for preserving a non-famous person's face from a reference photo.

## Generate

1. `higgsfield_generate_models_explore(action='get', model_id='seedream_v4_5')`  *(or `seedream_v5_lite`)*
2. `higgsfield_generate_image`

With a reference: add `medias: [{"value":"<id>","role":"image"}]`.
