# UGC Try-On Clip Prompt Writer -- Seedance 2.0

UGC try-on: creator wears and poses with a wearable product.
Board: 21:9 strip, FOUR slots.
Board 1 canonical arc: PRE_WEAR (kraft bag, on-camera dialogue) -> WEARING (voiceover + twirl) -> TEXTURE_CLOSEUP (voiceover, hand-free macro) -> STYLE_POSE (voiceover, different room).
Boards 2+N: 4 pose variations in product outfit, no kraft bag, no twirl.

## Audio split (unique to try-on)
- Cut 1 (SELFIE POV): on-camera dialogue, mouth moves, lip-syncing audio_lines[0]
- Cut 1 (TRIPOD POV): voiceover layered audio_lines[0], silent on camera
- Cuts 2/3/4: voiceover layered, character SILENT on camera (NOT lip-syncing)

## Board 1 rules
- Cut 1 PRE_WEAR: character in PRE-WEAR outfit (NOT product), kraft paper bag at her side.
  NO opening bag, NO peeking inside, NO product visible.
- Cut 2 WEARING: character now in product outfit. NO description of changing clothes.
  Hard cut from Cut 1 handles the implicit transition.
  MANDATORY TWIRL (Board 1 only): one brief natural twirl revealing back of outfit, settles back facing camera.
- Cut 3 TEXTURE_CLOSEUP: tight macro on fabric/cut/detail. **NO HAND CONTACT WITH FABRIC**.
  Texture shown via framing, drape, light, and natural body micro-movement only.
- Cut 4 STYLE_POSE: character in DIFFERENT room, styled pose. Voiceover layered, character silent.
  NO CTA tail.

## Garment Consistency Lock
Silhouette / primary color / print / recognizable design details identical across all cuts where product is worn.
Realistic fit, natural drape -- not exaggerated.

## Prompt structure (Board 1)

```
Style & Mood: UGC iPhone aesthetic, [light, tier-matched], [MIXED: SELFIE -> TRIPOD -> TRIPOD -> TRIPOD], social media vertical format. Tone: [excited/confident/cold/playful/posh/amazed].

Narrative Summary: She picks up the new outfit, tries it on, shows the texture, and lands in a styled pose.

Dynamic Description:
Cut 1 (0-Xs) -- WAIST-UP SELFIE: [PRE_WEAR, kraft bag, speaking visibly to camera lip-syncing "audio_lines[0]"]. Hard cut to.
Cut 2 (Xs-Ys) -- FULL-BODY TRIPOD: [WEARING, now in product, twirl beat in middle, voiceover audio_lines[1], silent on camera]. Hard cut to.
Cut 3 (Ys-Zs) -- MACRO TRIPOD-CLOSE: [TEXTURE_CLOSEUP, hand-free macro on fabric, voiceover audio_lines[2], silent on camera]. Hard cut to.
Cut 4 (Zs-end) -- MEDIUM-WIDE TRIPOD: [STYLE_POSE, different room, settled pose, voiceover audio_lines[3], silent on camera].

Static Description: Slots 1-3 in [primary room]. Slot 4 in [different room, tier-matched].

Audio: Cut 1 -- on-camera dialogue, mouth moves lip-syncing: "[audio_lines[0]]". Cut 2 -- layered voiceover, silent on camera: "[audio_lines[1]]". Plus ambient fabric rustle on twirl. Cut 3 -- layered voiceover: "[audio_lines[2]]". Cut 4 -- layered voiceover: "[audio_lines[3]]". iPhone mic, natural room tone, tone-matched delivery.

Facial features clear, hairstyle consistent. Garment consistent (silhouette/color/print/details identical). Realistic fit, natural drape. Shot on iPhone, natural lighting. [POV movement language]. No mirror or reflection shots. No on-screen text. No CTA tail.
```

## Tone-matched audio examples
excited: Cut1 "look what just arrived" | Cut2 "the fit is so good" | Cut3 "this material though" | Cut4 "I'm gonna live in this"
confident: Cut1 "new piece, just arrived" | Cut2 "yeah, this works" | Cut3 "the material's quality" | Cut4 "that's the one"
cold: Cut1 "new arrival" | Cut2 "clean." | Cut3 "the fabric reads well" | Cut4 "good"
playful: Cut1 "okay so this just came in" | Cut2 "how is this fitting me this well" | Cut3 "the texture though" | Cut4 "can't deal with how cute this is"
posh: Cut1 "the new piece is here" | Cut2 "the silhouette is correct" | Cut3 "quality fabric" | Cut4 "perfect drape"
amazed: Cut1 "wait it's actually here" | Cut2 "wait this looks unreal" | Cut3 "the texture is incredible" | Cut4 "didn't expect this"

## Forbidden
- On-screen costume change depicted
- Hand contact with fabric in Cut 3
- CTA tail (try-on NEVER has CTA)
- Mirror / reflection shots
- Kraft bag in Cuts 2/3/4 or Boards 2+N
