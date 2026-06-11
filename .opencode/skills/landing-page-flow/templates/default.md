# Template - Default (custom site)

Fallback when no structural template fits. Mock up 3 distinct homepage designs,
let user pick one, then build a custom responsive landing page.

## Quick path

1. Generate 3 distinct homepage mockups with GPT Image 2 (vary layout + palette + type)
   -> show all 3 -> wait for user to pick one
2. Study chosen mockup: extract palette hexes, fonts, section order, component style
   -> write it as a design spec
3. `create_website(name="<brand>", template="frontend")` -> note source_path
4. Build responsive landing page in src/routes/index.tsx + src/styles.css:
   - Set design tokens from sampled palette
   - Build sections in spec order using shadcn/ui components
   - Real on-brand copy (NEVER transcribe mockup text - it's fake)
   - Generate actual imagery with GPT Image 2 in the chosen style -> place in public/
   - Keep SSR-safe (no window/document at module top level)
5. `deploy_website()` -> return live URL

## Two critical rules

1. Mockup is a DIRECTION not a pixel-spec. Extract structure/palette/type, build responsively.
2. IGNORE text in mockup - it's gibberish. Write real copy from brand/purpose/intake.
