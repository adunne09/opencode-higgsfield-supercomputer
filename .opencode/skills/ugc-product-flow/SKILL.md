---
name: ugc-product-flow
description: >
  AI Employee: Product UGC Reviewer.
  A UGC-style product-only video -- the product is the sole focus,
  voiceover only, no creator on camera.
triggers:
  - only the product
  - product-only video
  - no talking head
  - no creator on camera
  - product is the hero
  - just the product on screen
  - product showcase
  - product review no face
  - voiceover product video
  - hands-only product demo
---

# ugc-product-flow

## Purpose

Generate a UGC-style product-only video where the **product is the sole hero**.
There is NO creator on camera — only voiceover narration accompanies
close-up product shots, demo angles, and result reveals. The viewer sees
the product being held, used, and demonstrated but never sees a face or
talking head. This is the go-to flow when the user wants a product review
video that feels authentic and UGC-native without requiring an on-camera
creator, identity, or Soul ID.

---

## Trigger Conditions

Activate this skill when the request matches ANY of these signals:

- "only the product", "product-only video"
- "no talking head", "no creator on camera", "no face"
- "product is the hero", "just the product on screen"
- "product showcase video", "product review without a person"
- "voiceover product demo", "hands-only demo"
- User explicitly says they do NOT want a person / creator visible

**Do NOT activate for:**

| Scenario | Correct Skill |
|---|---|
| Talking-head UGC (creator on camera speaking) | `ugc-flow` |
| Unboxing video (package reveal sequence) | `ugc-unboxing-flow` |
| Tutorial / how-to (step-by-step instructional) | `ugc-tutorial-flow` |
| Try-on / wearable demo (person modeling product) | `ugc-try-on-flow` |
| Cinematic / brand-film aesthetic | Do NOT use any UGC skill |

If the request is ambiguous between product-only and talking-head,
ask the user to clarify before proceeding.

---

## Architecture Overview

### Board = 21:9 Strip with 4 Vertical 9:16 Slots

Each **board** is a single wide image generated at **21:9 aspect ratio**
using `gpt_image_2`. This strip encodes exactly **4 vertical 9:16 frames**
laid out left-to-right. Each board is then fed to **ONE Seedance call**
(`seedance_2_0`, output **9:16**) which animates the 4-frame strip into
a single continuous video clip.

```
Board layout (21:9):
+----------+----------+----------+----------+
|  Slot 1  |  Slot 2  |  Slot 3  |  Slot 4  |
|  (9:16)  |  (9:16)  |  (9:16)  |  (9:16)  |
|          |          |          |          |
| PRODUCT  | PRODUCT  | PRODUCT  | PRODUCT  |
|  INTRO   | DEMO-A   | DEMO-B   | RESULT   |
+----------+----------+----------+----------+
```

### Canonical Product Arc (4 Slots per Board)

Every board follows the **Product Arc** — a 4-beat narrative structure
designed for product-only videos:

| Slot | Beat | Description |
|------|------|-------------|
| 1 | **PRODUCT-INTRO** | Hero shot of the product — clean, well-lit, centered. Packaging or label visible. Establishes what the product is. |
| 2 | **PRODUCT-DEMO-A** | First interaction — hands pick up, open, squeeze, apply, pour, or activate the product. Demonstrates primary use. |
| 3 | **PRODUCT-DEMO-B** | Second angle or continued demo — close-up texture, swatching, pouring, spreading, or mechanical action. Shows detail. |
| 4 | **PRODUCT-RESULT** | Outcome / payoff — the result of using the product. Before/after, final application, or satisfied reveal shot. |

For multi-board videos, each subsequent board continues the arc with
variation: different angles, zoom levels, lighting shifts, or
progression of use. The product must remain the dominant visual element
in every single slot of every board.

---

## Duration Table

Map the user requested duration to the number of boards:

| Requested Duration | Boards | Effective Duration |
|---|---|---|
| 4 - 15 s | 1 | ~5 s per board |
| 16 - 19 s | 2 | ~10 s total |
| 20 - 30 s | 2 | ~10 s total |
| 31 - 45 s | 3 | ~15 s total |
| 46 - 60 s | 4 | ~20 s total |
| > 60 s | ceil(D / 15) | ~5 s per board |

If the user does not specify a duration, default to **2 boards** (~10 s).
Never ask the user how many boards to generate — compute it silently
from the duration table.

---

## Pipeline — Step by Step

### Step 1: Parse Request

Extract from the user message:
- **Product**: what the product is (name, type, brand if given)
- **Duration**: target length in seconds (default: 10 s -> 2 boards)
- **Tone / angle**: any specific mood, selling point, or audience hint
- **Product image**: attached reference image of the product

If no product image is attached, ask the user to provide one.
If no duration is specified, default to 10 s (2 boards).

### Step 2: Upload Product Image

Upload the user-provided product image via `higgsfield_upload`.
Store the returned `id` as `product_id`. This single upload is reused
across every board and Seedance call — never re-upload the same asset.

### Step 3: Draft Voiceover Script

Write a voiceover script covering the full video duration.
Rules for the voiceover:

- **Voiceover ONLY** — no on-camera dialogue, no lip-sync, no greetings
- No "Hey guys", "Hi everyone", or any direct-address openers
- Start with the product or its benefit immediately
- Match voice gender to product category:
  - **Female voice**: skincare, beauty, makeup, haircare, nails, fragrance,
    jewelry, fashion accessories, home decor, candles, wellness supplements
  - **Male voice**: tech, gadgets, tools, automotive, sports equipment,
    grooming, outdoors gear, gaming peripherals
  - **Random / neutral**: food, beverages, stationery, pet products,
    household cleaning, generic lifestyle
- Keep it natural, conversational, UGC-authentic — not polished ad-copy
- Pace: ~2.5 words per second target
- Structure script beats to align with board transitions

### Step 4: Generate Boards Sequentially (gpt_image_2)

Generate each board image one at a time using `gpt_image_2` at **21:9**
aspect ratio. Each board prompt must describe all 4 slots left-to-right
in a single image.

**Board prompt structure:**
```
A 21:9 horizontal strip divided into 4 equal vertical 9:16 panels,
left to right, showing a product-only UGC sequence:

Panel 1 (PRODUCT-INTRO): [description — hero shot of {product}]
Panel 2 (PRODUCT-DEMO-A): [description — first interaction with {product}]
Panel 3 (PRODUCT-DEMO-B): [description — detail/close-up of {product}]
Panel 4 (PRODUCT-RESULT): [description — result/payoff of {product}]

Lighting: natural, soft, lifestyle setting.
Style: iPhone-shot UGC aesthetic, authentic, not overly produced.
The product is the dominant element in every panel.
Any person shown is auxiliary — hands only, POV perspective, cropped
below the shoulders. No face visible. No talking head.
```

**Board medias (input images):**
- **Board 1**: `[product_id]` — only the uploaded product reference
- **Board K (K > 1)**: `[product_id, previous_board_id]` — the product
  reference PLUS the previous board output for visual continuity

**Two-channel input rule:** When a board generation returns a result,
capture the `(job_id, result.url)` chain-ref. Use this for downstream
references. Do NOT re-upload outputs — reference them by their returned
ID directly.

### Step 5: Write Seedance Prompt per Board

For each generated board, write a Seedance animation prompt that
describes the motion, camera movement, and product interaction to
animate across the 4-frame strip.

**Seedance prompt rules:**
- Describe continuous product-focused motion across the 4 panels
- Include specific physical actions: hands picking up, rotating,
  squeezing, opening, applying, pouring, swiping
- Specify camera movement: slow push-in, pan across product, orbit,
  top-down to eye-level tilt
- Mention lighting shifts if relevant (morning light, studio ring-light)
- Keep motion smooth and purposeful — no jarring cuts within a board
- The product must remain in frame and dominant throughout the animation

### Step 6: Submit Seedance Jobs in Parallel

Submit ALL Seedance jobs at the same time — do not wait for one board
video before submitting the next. Use `seedance_2_0` with output
aspect ratio **9:16**.

**Seedance medias (input references per job):**
- `[board_K_id, product_id]` — the board image first, then the product
  reference image. Board image is primary (what to animate), product
  image is secondary (reference for product fidelity).

**Seedance parameters:**
- Model: `seedance_2_0`
- Aspect ratio: `9:16`
- Each job animates one board strip into one video clip

### Step 7: Poll, Download, and Stitch

- Poll all Seedance jobs until completion
- Download each resulting video clip
- Stitch clips in board order using montage/ffmpeg concatenation
- The final output is a single continuous 9:16 vertical video
- Upload the final stitched video via `higgsfield_upload`

---

## Product as Hero — Visual Rules

The product MUST be the dominant visual element in every frame.
Any human presence is strictly auxiliary and restricted:

### Allowed Human Elements
- **Hands only**: holding, gripping, applying, opening the product
- **POV perspective**: camera-as-person looking down at product in hands
- **Cropped torso**: below shoulders, showing hands interacting with product
- **Forearms**: visible when demonstrating product use

### Forbidden Human Elements
- NO face visible — not even partially
- NO talking head — not even in background
- NO full body shots
- NO creator walking, posing, or gesturing to camera
- NO eye contact with camera
- NO lip movement or speaking on camera

### Weight and Grip Physics Rules

When hands interact with the product, the physics must be realistic:

- **Light products** (lipstick, pen, phone): held between fingertips,
  one-handed, casual grip. Wrist angle relaxed.
- **Medium products** (bottle, jar, book, small appliance): held with
  full hand wrap, possibly two hands for display. Slight wrist effort.
- **Heavy products** (blender, cast iron pan, large electronics):
  two-handed grip, visible forearm tension, supported from below.
  Product rests on surface when not actively lifted.
- **Liquid products**: show pour weight, tilting momentum, viscosity
  in motion. Drips and flow must match liquid type.
- **Squeezable products**: show compression, product deformation,
  dispensing action matching container material (soft tube vs rigid pump).

Always match grip style and hand position to the product real-world
weight, size, and interaction pattern. Unrealistic floating or
weightless handling breaks UGC authenticity.

---

## ask_user_question Policy

### NEVER Ask About (Decide Silently)
- Aspect ratio — always 9:16, non-negotiable
- Resolution — default Seedance output
- Model selection — always gpt_image_2 for boards, seedance_2_0 for video
- Audio / music — voiceover is generated separately, not part of this pipeline
- Identity / Soul ID — this is a product-only flow, no creator identity needed
- Number of boards — computed from duration table, never exposed
- Stitch method — always montage/ffmpeg, no user choice

### DO Ask About (When Missing)
- **Product image**: if no product reference image is attached, ask for one
- **Duration**: if not specified, confirm or default to 10 s
- **Product name / type**: if unclear from context, ask what the product is
- **Specific angles or features**: if user hints at a preference, clarify
- **Tone**: only if the request is ambiguous between playful / serious / luxury

### ask_user_question Format
When asking, use `ask_user_question` with clear options. Batch all
missing information into a single question call — never ask one thing
at a time. Example:

```
questions:
  - header: "Product details needed"
    options: []
    freeform: true
    text: "Please share: (1) a product image, (2) desired video length
           in seconds, (3) the product name/type"
```

---

## Voiceover Generation Details

### Script Structure by Board Count

**1 board (4-15 s):**
- 10-35 words total
- Single punchy statement about the product + result

**2 boards (16-30 s):**
- 35-70 words total
- Board 1: introduce product + primary benefit
- Board 2: demonstrate + reveal result

**3 boards (31-45 s):**
- 70-105 words total
- Board 1: hook + product introduction
- Board 2: features + demonstration
- Board 3: result + soft CTA

**4 boards (46-60 s):**
- 105-140 words total
- Board 1: hook + product context
- Board 2: primary feature demo
- Board 3: secondary feature or texture detail
- Board 4: result + recommendation

### Voice Style
- Casual, enthusiastic but not over-the-top
- First person singular: "I", "my", "I have been using"
- Present tense for demo, past tense for discovery story
- Include sensory language: texture, scent, sound, feel
- End with soft recommendation, never hard sell

---

## Multi-Board Visual Continuity

When generating boards K > 1, ensure visual continuity:

1. **Same product instance**: identical packaging, label, color throughout
2. **Consistent lighting**: if Board 1 is warm natural light, maintain it
3. **Progressive interaction**: each board advances the product story
4. **Background continuity**: same surface/environment unless motivated shift
5. **Hand consistency**: same skin tone, nail style, jewelry if visible
6. **Scale consistency**: product size relative to hands stays constant

Feed `previous_board_id` into Board K generation to enforce these
constraints visually. The model uses it as style and content reference.

---

## Output Specification

| Property | Value |
|---|---|
| Final format | MP4 video |
| Aspect ratio | 9:16 (vertical) |
| Composition | Product-only, voiceover narration |
| Clip count | Equal to number of boards |
| Audio | Voiceover track (generated separately) |
| Creator visible | NO — never |
| Product visible | YES — every frame |

---

## Error Handling

- If `gpt_image_2` fails on a board, retry once with simplified prompt
- If `seedance_2_0` fails on a clip, retry once; if still failing,
  regenerate the board image and retry Seedance
- If product image upload fails, ask user to re-attach
- If all retries exhausted, report which board/clip failed and deliver
  partial results (completed clips) with explanation
- Never silently drop a board — the user expects the full requested duration

---

## Example Request and Response

**User**: "Make me a product video of this serum, about 20 seconds,
focusing on the texture and glow"

**Parsed**:
- Product: facial serum (attached image)
- Duration: 20 s -> 2 boards
- Tone: texture + glow focus
- Voice: female (skincare category)

**Board 1 prompt**: 21:9 strip, 4 panels — serum bottle hero shot,
hand picking up dropper, close-up of serum dropping onto fingertip,
serum spread on skin showing golden glow.

**Board 2 prompt**: 21:9 strip, 4 panels — serum applied to cheek
in natural light, blending motion with fingertips, dewy skin close-up,
final glow result with bottle in background.

**Voiceover**: "This serum changed my entire morning routine. The texture
is so lightweight — it just melts right in. Look at that glow. Two drops
and my skin looks like I actually slept eight hours. Honestly obsessed."

**Output**: 2-clip stitched 9:16 vertical video, ~10 s, voiceover track,
product dominant in every frame, no face visible.
