# Soul 2.0 (text2image_soul_v2)

Text-to-image for high-quality aesthetic images — portraits, landscapes, abstract
art, product shots, editorial, fashion, stylized illustration. Image-to-image:
pass a reference in `medias`; the prompt is **ignored** when `medias` is present
(the model transforms the reference directly).

## Prompting

Write as a **single dense paragraph** in this order:
1. **Opening** — subject + shot type ("A high-angle close-up of…").
2. **Details/textures** — objects, clothing, materials ("glossy", "weathered",
   "knitted"). Transcribe desired text verbatim.
3. **Setting** — background, arrangement, negative space.
4. **Lighting** — sources + quality ("specular highlights", "diffuse shadows",
   "volumetric rays").
5. **Color palette** — dominant + accent ("muted olive and slate gray").
6. **Composition/camera** — angle, framing, depth of field, lens.
7. **Medium** — "35mm film", "digital editorial"; grain/noise if relevant.
8. **Mood** — "nostalgic and melancholic", "cozy and intimate".

Tips:
- Objective, technical, photographic tone — material reality, not poetry.
- Min age 20 for humans; age up minors to "young woman/man, 20 years old".
- Model adds opaque clothing internally; "sheer" / "see-through" get replaced.
- **No text/lettering** — does not render readable text. For cinematic framing use Soul Cinematic.

## Generate

1. `higgsfield_generate_models_explore(action='get', model_id='text2image_soul_v2')`
2. `higgsfield_generate_image`

Image-to-image: pass `medias: [{"value":"<id>","role":"image"}]` — the prompt is ignored.
