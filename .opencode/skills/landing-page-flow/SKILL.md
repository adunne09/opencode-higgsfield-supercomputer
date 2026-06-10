---
name: landing-page-flow
version: 1.0
description: |
  Build a complete, content-rich landing page from a short prompt and publish
  it live (TanStack Start + React, server-rendered per request as its own
  Cloudflare Worker). Generates its own visual content (images, video, audio)
  with the Higgsfield tools instead of using stock/placeholder media, picks a
  matching page template, and deploys via the website builder.
  Use for: "build me a website", "make a landing page",
  "scroll site / scroll-scrub site", "AI-generated website", "brand site",
  "product launch page", "portfolio site", "gallery / photo gallery",
  "lookbook", "merch shop / online store", "showcase my work / products",
  "make a site about X".
  Triggers: build a website, landing page, scroll site, scroll scrub,
  brand reveal, product launch page, portfolio, gallery, lookbook, merch,
  shop, store, collection, showcase, website from scratch.
allowed-tools:
  - create_website
  - deploy_website
  - higgsfield_generate_image
  - higgsfield_generate_video
  - higgsfield_generate_models_explore
  - higgsfield_audio_generate
  - higgsfield_job_status
  - higgsfield_upload
  - read_file
  - write_file
  - terminal
  - ask_user_question
  - skill_view
---
# Landing Page Flow

Turn a short prompt into a live, content-rich landing page. Content (hero imagery, video, audio)
is generated, not stock. Pick a page template that fits the request, build it into the website
builder, and deploy. Optimize for a great result with minimal questions.

## Principles

- Content is the product. Every site ships with generated visuals -- use
  higgsfield_generate_image / higgsfield_generate_video / higgsfield_audio_generate.
  Never leave lorem-ipsum, gray boxes, or hot-linked stock photos.
- Few questions. Infer as much as you can; ask at most TWO questions in one message.
- SSR-safe rendering. Sites are server-rendered (published as a per-tenant Worker).
  NEVER touch browser-only globals (window, document, localStorage, navigator) at module
  top level or during render. Use them only inside useEffect / event handlers, or guard
  with typeof window !== "undefined".
- No verify loop. Build it, deploy it, hand back the URL.

## Step 1: Intake (2 questions max)

Extract from the user's request: brand/name, style/vibe, purpose (product launch, portfolio,
event, app, etc.), and any reference images/URLs.

If brand AND style are both clear from the prompt, ask nothing -- proceed.
Otherwise ask at most two short questions in a single message.

Parse into: brand, style, purpose, refs[] (may be empty). If the user gave refs as local files
or URLs, upload them with higgsfield_upload so they can be passed as generation reference media.

## Step 2: Pick a template and load its recipe

Choose the template whose fit best matches the request, then load its recipe:

skill_view(name="landing-page-flow", file_path="templates/<file>")

### Template catalog

| Template | file | Best for |
|---|---|---|
| Scroll Scrub | templates/scroll-scrub.md | Immersive scroll-driven brand reveals and product launches -- an AI-generated film is scrubbed frame-by-frame as the user scrolls. Dark, centered, high-impact cinematic scroll sites. |
| Atelier | templates/atelier.md | A warm editorial scroll site for a boutique / small brand (florist, cafe, perfumer, fashion atelier, hotel). Light, full-frame lifestyle footage with frosted card captions. |
| Gallery Wall | templates/gallery-wall.md | A collection of images/videos/assets -- an infinite draggable wall of media tiles with magnetic hover, tilt, and lightbox. For portfolio, gallery, lookbook, or merch shop. |
| Default | templates/default.md | The fallback when none of the structural templates above fit. Mock up 3 distinct homepage designs, let the user pick one, then build a custom responsive landing page. |

## Step 3: Build and deploy (shared contract for every template)

1. create_website(name="<brand>", template="<template>") -- forks the chosen website template
   into source_path and returns a key_files map. Pass the template the recipe calls for.
2. Follow the chosen template recipe: generate content, write the project files, place
   generated media under source_path/public/...
3. deploy_website() -- builds the SSR app, publishes the Worker, returns the live URL.

Generated media goes in public/. Reference runtime media with plain root-absolute paths
(e.g. /frames/frame_0001.jpg, /hero.png). Keep large source files the page does NOT load
OUT of public/ so they aren't shipped.

## Step 4: Report back

Give the user the live URL, a one-line summary (brand + palette + sections + what media was
generated), and offer one quick follow-up ("want a different vibe / swap the hero video / add a section?").
