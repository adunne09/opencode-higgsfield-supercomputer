# UGC Tutorial Board System Prompt - 4-Slot 21:9 Storyboard

Generates a single horizontal 21:9 storyboard sheet with exactly 4 equal 9:16 slots.
Each slot depicts ONE physical step of the product tutorial with a "Step N -- Heading" caption.

## Key constraints

- Exactly 4 slots, all active, identical 9:16 dimensions, single horizontal row, 21:9 total
- Thin white gutters between slots, clean white background
- Exactly ONE "Step N -- Heading" caption per slot (English, Title Case, en-dash separator)
- Identical typography (font, size, color, position) across ALL 4 slots
- Character identity consistent across all 4 slots (from reference image)
- Product follows Angle Lock (show only front-facing side from reference)
- Product at realistic real-world scale (never enlarged)
- POV may change between slots (every POV change = hard cut)
- Character has exactly 2 hands; selfie POV = 1 hand for phone + 1 for action

## Caption typography selection (per product category)

- Skincare/luxury beauty: editorial serif (Didot/Bodoni vibe), thin strokes
- Fragrance: refined serif or thin elegant sans
- Color cosmetics/makeup: modern sans or italic stylized serif
- Tech/electronics: modern geometric sans (Inter/Helvetica Neue)
- Fitness/supplements: bold condensed sans ALL-CAPS (Oswald/Impact vibe)
- Food/beverage: warm rounded sans or handwritten script
- Default: clean sans (Inter/Helvetica), regular weight

## Default POV cadence for tutorials

TRIPOD -> TRIPOD -> TRIPOD -> SELFIE
(Slots 1-3: both hands free for product mechanics; Slot 4: selfie wrap-up)

## Step captions format

"Step N -- Heading" (en-dash with single spaces, Title Case, English only)
Position: top-center of slot, ~5-7% from top, same across all 4 slots
Shift to bottom-center only if character face crosses top caption zone (and shift ALL 4 slots)

## Image reference order

Product + character + prev board (K>1): @Image1=product, @Image2=character, @Image3=prev-board
Product + character (K=1): @Image1=product, @Image2=character
Character only: @Image1=character

## Output

JSON: { "case": "ugc-tutorial-board-4slot", "input_tier": "...", "board_specs": {...}, "physics_analysis": {...}, "prompt": "..." }
