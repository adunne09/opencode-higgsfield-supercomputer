# UGC Clip Prompt Writer -- Seedance 2.0

Generates ONE 9:16 Seedance prompt for a UGC talking-head review clip.
Board: 16:9 strip, THREE vertical 9:16 slots -> THREE hard cuts.

## Prompt structure

```
Style & Mood: UGC iPhone aesthetic, [light], [SELFIE/TRIPOD/MIXED POV cadence], social media vertical format.

Narrative Summary: [1 sentence, arc_role, throughline of 3 cuts].

Dynamic Description:
Cut 1 (0-Xs) -- [framing] [POV]: [action, 3+ micro-behaviors, expression, product placement]. Hard cut to.
Cut 2 (Xs-Ys) -- [framing] [POV]: [action, 3+ micro-behaviors, expression, product placement]. Hard cut to.
Cut 3 (Ys-end) -- [framing] [POV]: [action, 3+ micro-behaviors, expression, product placement].

Static Description: [1-2 sentences: setting, ambient details].

Audio: She speaks to camera, iPhone microphone audio with natural room tone[, non-verbal sounds if K==1]: "[monologue verbatim distributed across 3 cuts]"

Facial features clear and undistorted, consistent clothing. Shot on iPhone, natural lighting, social media aesthetic. [POV movement language]. No on-screen text, no subtitles, no captions, no watermarks.
```

## POV rules
- SELFIE: phone is camera (never visible), 1 hand free, natural handheld micro-shake
- TRIPOD: absolutely frozen, zero camera movement, 2 hands free
- MIXED: use MIXED phrasing for POV-alternating boards

## Cut timing (3-cut distribution)
| duration | Cut 1 | Cut 2 | Cut 3 |
|---|---|---|---|
| 6s | 2s | 2s | 2s |
| 10s | 3s | 3.5s | 3.5s |
| 15s | 4.5s | 5s | 5.5s |

## Audio rules
- K=1: include 1-3 bracketed non-verbal sounds at start ([*explosive gasp*] etc) -- skip for calm tone
- K>1: open mid-thought, NO greetings, NO re-introductions
- Never repeat same phrase across cuts
- Forbidden openers: OK/Okay/So/Yeah/Um/Like/Wait as FIRST word

## Action rules
- Product Angle Lock: only front-facing side, never rotate/spin
- ONE product instance only
- Max 1 product interaction per cut
- Cap/lid removed BEFORE contents exit; cap disappears, never re-closes
- Body-part target lock: perfume->wrist/neck, lipstick->lips, cream->fingertip->face, drink->mouth
- Forbidden phrases: sprays again, presses repeatedly, back and forth

## Micro-behaviors (5+ per cut, evolving across cuts)
Default (hyped): WILD open-mouth scream-gasp, mouth blown open, dramatic head jerk, cheek puff, eyebrows shoot skyward, full-body shudder
Calm (goth/clinical/cold): weight shift, hair touch, glance break, head tilt, eyebrow flash, posture shift

## Anti-patterns (NEVER write)
- smiles at the camera / looks at the camera / holds the product and talks
- Identical expression across all 3 cuts
- Phone visible in SELFIE cuts
- Mirror/reflection shots
