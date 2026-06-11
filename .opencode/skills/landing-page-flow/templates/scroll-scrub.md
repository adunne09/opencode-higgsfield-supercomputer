# Template - Scroll Scrub

Scroll-driven cinematic landing page: an AI-generated ~15-second film is extracted to frames
and scrubbed frame-by-frame on a canvas as the user scrolls.

## Quick path

1. `create_website(name="<brand>", template="scroll-scrub")` -> note source_path
2. GPT Image 2 storyboard (6 keyframes, ONE continuous move, dark background, center subject)
   -> show user -> wait for approval
3. Seedance 2.0 film (1080p, `role: "image"` for storyboard, NOT start_image)
   -> optional 4K upscale with bytedance if user opts in
4. `ffmpeg -y -i film.mp4 -qscale:v 2 -start_number 1 public/frames/frame_%04d.jpg`
   -> count N frames
5. Fill template: set FRAME_COUNT=N, replace `<...>` placeholders in src/routes/index.tsx,
   set --accent in scroll-scrub.css, set data-range for each overlay
6. `deploy_website()` -> return live URL

## Footage rules

- ONE continuous camera move (orbit/push-in/rise), NO hard cuts
- One hero subject, centered, dark seamless background
- Slow steady motion, locked exposure, minimal motion blur
- No on-screen text or logos (those are HTML overlays)

## Optional ambient audio (Step 5)

`higgsfield_audio_generate` -> save under public/audio/ -> start muted, unmute on interaction

## Key files

- src/routes/index.tsx: FRAME_COUNT, placeholders, data-range attributes, BrandMark
- src/routes/scroll-scrub.css: --accent + surface/ink tokens, .ss-video-spacer height
- public/frames/: frame_0001.jpg ... (4-digit pad, from 1)
