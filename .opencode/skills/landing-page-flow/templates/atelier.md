# Template - Atelier (editorial brand site)

Warm editorial scroll site for boutique / atelier / small-brand (florist, cafe, perfumer, hotel).
Same scroll-scrubbed canvas as Scroll Scrub, but tuned for warm full-frame lifestyle footage.

## Quick path

1. `create_website(name="<brand>", template="atelier")` -> note source_path
2. GPT Image 2 brandkit frame (warm, full-frame lifestyle, subject centered in environment)
   -> show user -> wait for approval -> sample palette from this frame
3. Seedance 2.0 film (1080p, `role: "image"` NOT start_image, gentle continuous drift)
4. `ffmpeg -y -i film.mp4 -qscale:v 2 -start_number 1 public/frames/frame_%04d.jpg`
5. Fill template: set FRAME_COUNT, replace all <...> placeholders in src/routes/index.tsx,
   set --rose and tokens in atelier.css, set data-range (film scenes 0->0.49, panels 0.52->1.0)
6. `deploy_website()` -> return live URL

## Footage rules

- ONE continuous shot (gentle drift, no cuts)
- Warm full-frame lifestyle - subject IN an environment (maker at work, sunlit room)
- Frosted cards carry legibility - busy background OK
- Locked exposure, no baked-in text

## Key files

- src/routes/index.tsx: FRAME_COUNT, hero, 5 film scenes, 5 content panels (catalog, quote, principles, pricing, contact)
- src/routes/atelier.css: --rose accent, surface/ink tokens, font stacks
- public/frames/: frame_0001.jpg ... (4-digit pad, from 1)
