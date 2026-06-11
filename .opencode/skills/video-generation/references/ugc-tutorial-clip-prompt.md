# UGC Tutorial Clip Prompt Writer -- Seedance 2.0

UGC tutorial: creator demonstrates step-by-step product use.
Board: 21:9 strip, FOUR slots with rendered "Step N -- Heading" captions baked in.
Each cut = ONE physical step of using the product.

## Key specifics
- Step captions are baked into source frames -- Seedance preserves them as-is
- NEVER animate/replace/add captions; they stay static and sharp throughout each cut
- Final board (): last 0.5-1s of Cut 4 = CTA tail (talking-head selfie + downward gesture + "Link in bio."/"Follow me."/"Subscribe!")
- CTA is audio + gesture only -- NEVER add a text overlay for the CTA
- Default cadence: TRIPOD -> TRIPOD -> TRIPOD -> SELFIE

## Prompt structure

```
Style & Mood: UGC iPhone aesthetic, [light], [MIXED/TRIPOD/SELFIE POV cadence], social media vertical format. Each cut shows the on-screen text caption "Step N -- Heading" baked into the frame, identical typography across cuts.

Narrative Summary: [1 sentence: this clip demonstrates Steps X through Y of the product tutorial].

Dynamic Description:
Cut 1 (0-Xs) -- [framing] [POV]: [step 1 physical mechanic]. The on-screen caption "Step N -- Heading" is visible in its baked position throughout this cut. Hard cut to.
Cut 2 (Xs-Ys) -- [framing] [POV]: [step 2 mechanic]. Caption "Step N+1..." visible. Hard cut to.
Cut 3 (Ys-Zs) -- [framing] [POV]: [step 3 mechanic]. Caption "Step N+2..." visible. Hard cut to.
Cut 4 (Zs-end) -- [framing] [POV]: [step 4 mechanic]. Caption "Step N+3..." visible. [IF is_last_board: CTA tail at final 0.5-1s]

Static Description: [1-2 sentences].

Audio: She speaks to camera, iPhone microphone audio with natural room tone[, K=1 non-verbal sounds]: "[monologue across 4 cuts][, IF is_last_board: CTA phrase appended]"

Facial features clear, consistent clothing. Shot on iPhone, natural lighting. [POV movement language]. Step captions stay sharp, legible, undistorted, unchanged. No additional on-screen text, no subtitles, no extra captions beyond the baked-in Step captions.
```

## CTA tail (is_last_board only)
~1s available: "Link in bio." (3 syllables) or "Follow me." (3 syllables)
<=0.5s: "Subscribe!" (2 syllables)
CTA = audio + downward gesture only. No new text overlay. Never two CTAs.

## Tutorial audio
- Opens mid-thought for K>1 (continuing into next step: "Now I press the pump twice...")
- Describes mechanics and outcomes, not abstract feelings
- English only by default
