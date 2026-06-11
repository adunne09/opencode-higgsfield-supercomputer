# Soul 2.0 - UGC Character Prompt Rules

Generate a clean creator/persona (no products) for UGC video pipelines.
Model: text2image_soul_v2, aspect_ratio: 3:4, enhance_prompt: false.

## CRITICAL: No Products in Character Image

Describe ONLY the person (appearance, face, expression, clothing, pose, style).
NEVER include products, objects, or props. Location/background is OK.
Product enters the video LATER via medias + @ImageN / <<<element_id>>>.

## Beauty Floor (mandatory in every prompt)

Every prompt MUST contain: "with high model facial features", "symmetrical features",
"well-proportioned figure", "natural skin texture".

## Variety Roll Protocol (mandatory before generation)

Run: python3 -c "import secrets; print(' '.join(str(secrets.randbelow(100)) for _ in range(8)))"
Map 8 rolls to these axes (roll % pool_size):
- Age band (4): early 20s, mid 20s, late 20s, early 30s
- Hair color (14): warm honey blonde, cool ash blonde, chestnut, espresso, soft brown, jet black, deep auburn, copper, platinum, honey balayage, money-piece highlights, vivid green, vivid pink, pastel lavender
- Hair length & style (14): shoulder-length wavy, long sleek straight, long with soft waves, medium with curtain bangs, sleek bob, pixie cut, wolf cut, claw-clip slicked back, messy low bun, half-up half-down, boxer braids, high ponytail, dreadlocks, shaved buzz cut
- Build / vibe (5): athletic toned, soft natural, average proportional, petite, tall everyday
- Distinctive feature (9): clean, natural freckles, delicate nose stud, small lip ring, eyebrow piercing, septum ring, dimples, gap teeth, subtle beauty mole
- Makeup register (9): bare-skin, casual natural, glowy with mascara only, soft brown smoky, playful winged liner, bold colored eyeliner, inner-corner glitter, graphic blush, glossy lip
- Ethnicity / face read (8): fair European, warm Mediterranean, East Asian, South Asian, Latina, mixed, Middle Eastern, Slavic
- Outfit aesthetic register (10): streetwear-oversized, preppy/equestrian, Y2K-coded, quiet-luxury, coastal-minimal, sporty-jersey, editorial-blazer-cool, boho-soft, leather-grit, clean-minimalist

## Style

- Natural, lifestyle, UGC-feel - NOT editorial, studio, or fashion
- Default light: neutral cool daylight ONLY - NEVER golden hour, warm sunset, orange/amber cast
- Approachable, authentic

## Location x Tier x Wardrobe Matrix (abbreviated)

Match (product category, tier: luxury/premium/drugstore) to location + wardrobe:
- Cosmetics/fragrance luxury: stylish bedroom/vanity, silk robe tightly tied
- Cosmetics/fragrance premium: bright modern bathroom/bedroom, premium cotton robe
- Cosmetics/fragrance drugstore: everyday bathroom/bedroom, cozy oversized robe
- Skincare/haircare luxury: spa-like bathroom, silk robe
- Skincare/haircare premium: bright modern bathroom, cream cotton robe
- Food/beverages: modern kitchen, casual chic top + jeans
- Supplements/fitness: home gym or kitchen, athleisure
- Clothing/accessories luxury: dressing room, curated outfit
- Tech: home desk/living room, smart casual
- Cars: outdoor/driveway, casual outerwear

## Universal Outfit Modesty (mandatory)

Append to every outfit: "top fully closed at the front, fabric meeting at the collarbone, classic high-coverage fit"
Robes/kimonos: "tightly tied at the waist with sash visible, both lapels overlapping fully across the chest"
FORBIDDEN as standalone: tank top, spaghetti strap (only under blazer/cardigan with modesty rules applied)

## iPhone UGC Camera Rules

MANDATORY phrasing in every prompt:
- "Self-portrait selfie shot on iPhone front-facing camera held by the subject at arm's length"
- "Slightly off-center, slightly imperfect framing - not posed, not studio-centered"
- "Phone-sensor grain and realistic skin pores and texture preserved - no retouch"

HARD BAN: "centered composition at eye-level", "minimal depth of field", "editorial portrait",
"aspirational lifestyle atmosphere", "glowing skin", "poses", "striking a pose"

## Closing block (append verbatim)

"Self-portrait selfie shot on iPhone front-facing camera held by the subject at arm's length --
head and shoulders fill the frame, casual handheld framing, slight natural tilt, slightly
off-center, slightly imperfect, not posed. Phone-sensor grain and realistic skin texture
preserved, no retouch, no smooth-skin filter. Authentic UGC creator phone selfie,
NOT editorial portrait, NOT fashion magazine."

## Tool call

higgsfield_generate_image(requests=[{
  "model": "text2image_soul_v2",
  "prompt": "<built per structure above>",
  "aspect_ratio": "3:4",
  "quality": "2k",
  "enhance_prompt": false
}])

After completion: register as character element for video reuse via higgsfield_element(action="create").
