# Soul 2.0 - UGC Character Prompt Rules

Canonical location: skill_view("image-generation", "references/soul-v2-ugc-character.md")

Generates a clean creator/persona (no products) for UGC video pipelines.
Model: text2image_soul_v2, aspect_ratio: 3:4, enhance_prompt: false.

Key rules:
- No products in character image (product enters via medias at video step)
- Beauty floor mandatory: "with high model facial features, symmetrical features, well-proportioned figure, natural skin texture"
- Run variety roll protocol (python3 -c "import secrets; print(' '.join(str(secrets.randbelow(100)) for _ in range(8)))") before generating
- Neutral cool daylight ONLY - NEVER golden hour / warm sunset / orange/amber cast
- iPhone selfie aesthetic: "Self-portrait selfie shot on iPhone front-facing camera... slightly off-center, phone-sensor grain..."
- Universal outfit modesty triplet: "top fully closed at the front, fabric meeting at the collarbone, classic high-coverage fit"

See full spec: skill_view("image-generation", "references/soul-v2-ugc-character.md")
