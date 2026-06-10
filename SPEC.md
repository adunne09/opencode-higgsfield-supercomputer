# Supercomputer Capabilities Inventory

## Tools

**Browser and Web**
- browser_navigate — load a URL
- browser_snapshot — get accessibility tree of current page
- browser_click — click element by ref
- browser_type — type into input field
- browser_scroll — scroll page
- browser_back — go back in history
- browser_press — press a keyboard key
- browser_screenshot — capture PNG screenshot
- browser_vision — screenshot plus vision analysis in one call
- browser_console — read JS console / evaluate expressions
- browser_get_images — list all images on current page
- web_search — Google-like web search
- web_extract — extract content from URLs (markdown, highlights, summary, structured)
- extract_document — parse PDF, XLSX, CSV, DOCX into markdown

**Higgsfield — Image Generation**
- higgsfield_generate_image — generate images (multiple models)
- higgsfield_generate_models_explore — discover/recommend generation models
- higgsfield_upscale_image — upscale images (Topaz, ByteDance)
- higgsfield_outpaint — expand/outpaint an image
- higgsfield_remove_background — remove background from image or video

**Higgsfield — Video Generation**
- higgsfield_generate_video — generate videos (multiple models)
- higgsfield_upscale_video — upscale/enhance videos
- higgsfield_reframe — change a video aspect ratio
- higgsfield_motion_control — animate a character image using a reference video

**Higgsfield — Identity and Assets**
- higgsfield_soul_id — train/manage face identity models (Soul IDs)
- higgsfield_element — manage reusable character/environment/prop elements
- higgsfield_upload — upload local files to Higgsfield CDN
- higgsfield_attachments_list — list previously generated images/videos/files
- higgsfield_balance — check workspace credit balance
- higgsfield_enhancer — backend prompt enhancer (40+ named flows)
- higgsfield_job_status — poll the status of generation jobs

**Memory and State**
- memory — persistent memory across sessions (user profile and project facts)
- artifacts — store/retrieve per-chat artifacts (job IDs, URLs, etc.)
- artifact_get / artifact_put — key-value store for expensive tool results
- todo — session task list manager
- tool_output_grep — search/read stored tool outputs

**System and Terminal**
- terminal — execute shell commands (foreground or background)
- process — manage background processes (poll, log, kill, write, submit)
- delegate_task — spawn parallel subagents for isolated workstreams

**Utilities**
- ask_user_question — structured questions (text, entity picker, file upload)
- schedule — create/manage cron-scheduled jobs
- skill_manage — create, patch, edit, delete skills
- skill_view — load a skill full content and linked files
- skills_list — list all available skills
- tool_search — discover and load additional toolsets on demand

---

## AI Employees (Specialized Workflows)

| Display Name | Domain |
|---|---|
| AI Influencer | Photoreal UGC creator/persona image |
| Amazon Listing Designer | Amazon marketplace listing image sets |
| Cartoon Animator | 2D/2.5D animated cartoon/anime videos |
| Cinematic Director | Short films, scripted narrative scenes |
| Motion Designer | Animated brand/product motion reels |
| Premium Motion Designer | High-energy kinetic/hyperkinetic brand reels |
| Image Generator | All image generation tasks |
| Infographic Animator | Animated infographic/data reel videos |
| Podcast Producer | AI-generated podcast/interview videos |
| Product Photographer | Studio product photos and brand marketing images |
| Product Animator | Photoreal premium product commercial reels |
| Text Generator | Copywriting, captions, scripts, long-form |
| TV Ad Director | 15-second 16:9 video ads and commercials |
| Typography Animator | Typography-driven kinetic brand reels |
| UGC Creator | UGC talking-head review videos |
| Product UGC Reviewer | Product-only UGC videos (no creator on camera) |
| Try-On UGC Stylist | UGC try-on videos (wearable products) |
| Tutorial UGC Creator | UGC tutorial/how-to step-by-step videos |
| Unboxing UGC Creator | UGC unboxing/reveal videos |
| Video Generator | All video generation tasks |

---

## Plain Skills (by category)

**Analyzer**
- brand-analyzer — extract brand assets from a website URL
- product-analyzer — extract product data from e-commerce URLs

**Creative**
- creative-ideation — generate project ideas through creative constraints
- design-md — author/validate/export DESIGN.md files
- excalidraw — create hand-drawn style diagrams
- infographic — render data visualizations as live React components
- popular-web-designs — 54 production-quality design system templates
- songwriting-and-ai-music — songwriting craft and AI music generation prompts

**Media**
- audio-generation — TTS voiceover, voice swap, video dubbing/translation
- montage — video assembly, stitch clips, overlay music
- soul-id — face identity training from photos or text description
- youtube-content — fetch YouTube transcripts and convert to text content
- youtube-research — research YouTube channels, videos, comments

**Productivity**
- create-skill — save current workflow as a reusable skill
- maps — geocoding, nearby places, routing/distance
- pdf — generate styled PDF documents from markdown
- powerpoint — create/edit .pptx slide decks

**Research**
- trend-picker — social media research, ad intelligence, trend discovery

**Social Media**
- organic-marketing — competitor discovery, trends, content brief generation

**Workflow Generation**
- landing-page-flow — build and deploy a full landing page (React + Cloudflare Worker)
- video-adapt — (coming soon, currently disabled)

---

## Loadable Toolsets (not yet active, load on demand)

ads, artifacts, ask_user_question, browser, browser-cdp, clarify, code_execution, connectors, data_ingestion, debugging, delegation, discord, file, higgsfield, higgsfield_assets, higgsfield_audio, higgsfield_generate, higgsfield_identity, image_gen, instagram, memory, messaging, safe, scheduling, search, session_search, skills, terminal, tiktok, tiktok_publish, todo, trends, tts, vision, web, website_builder, youtube

---

## Higgsfield Prompt Enhancer Flows (40+)

These are named pipelines inside higgsfield_enhancer:

**UGC flows:** ugc-character, ugc-board, ugc-clip, ugc-unboxing-board, ugc-unboxing-clip, ugc-try-board, ugc-try-clip

**TV Ad flows:** tv-ad-script, tv-ad-character, tv-ad-location, tv-ad-seedance

**Cinematic flows:** cinematic-dramaturg, cinematic-director, cinematic-style-architect, cinematic-shot-planner, cinematic-prompt-writer

**Cartoon flows:** cartoon-style-formula, cartoon-character-base, cartoon-character-stylize, cartoon-location-base, cartoon-location-stylize, cartoon-prop-base, cartoon-prop-stylize, cartoon-seedance-clip, cartoon-scene-parse, cartoon-shot-plan, cartoon-clip-plan

**Motion Design flows:** classicMD-board, classicMD-clip, motion-design-board, motion-design-clip, productMD-board, productMD-clip, highMD-board, highMD-clip, typographyMD-board, typographyMD-clip, infographicMD-board, infographicMD-clip

**Video Adapt flows:** vadapt-adapt-avatar, vadapt-adapt-product, vadapt-adapt-preserve

---

## Other Capabilities

- Scheduling: Cron jobs via schedule (create, pause, resume, stop, trigger, patch, delete)
- Subagent delegation: Parallel workstreams via delegate_task (leaf or orchestrator roles; research category available)
- Vision analysis: Screenshot plus AI vision via browser_vision or vision_analyze (multi-image comparison)
- Website building: Full React plus TanStack Start plus Cloudflare Worker deployment via website_builder toolset
- Code execution: Shell via terminal; structured code via code_execution toolset (loadable)
- Connected services: OAuth connectors (Google Drive, Gmail, Slack, Notion, etc.) via connectors toolset (loadable)
- Social media research: Instagram, TikTok, YouTube via dedicated toolsets (loadable)
- Audio: TTS, voice swap, dubbing via audio-generation skill plus higgsfield_audio toolset
