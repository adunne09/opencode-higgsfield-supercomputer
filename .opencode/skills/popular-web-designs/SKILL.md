---
name: popular-web-designs
description: >
  54 production-quality design systems extracted from real websites. Load a template
  to match the visual identity of sites like Stripe, Linear, Vercel, Notion, Airbnb,
  and more. Each template includes colors, typography, components, layout rules, and
  ready-to-use CSS values. Apply them in the website builder (TanStack Start + Tailwind)
  or as standalone HTML/CSS.
version: 1.0.0
tags: [design, css, html, ui, web-development, design-systems, templates]
triggers:
  - build a page that looks like
  - make it look like stripe
  - design like linear
  - vercel style
  - create a UI
  - web design
  - landing page
  - dashboard design
  - website styled like
---
# Popular Web Designs

54 real-world design systems ready for use when generating HTML/CSS. Each template captures a
site's complete visual language: color palette, typography hierarchy, component styles, spacing
system, shadows, responsive behavior, and practical agent prompts with exact CSS values.

## How to Use

1. Pick a design from the catalog below.
2. Load it: `skill_view(name="popular-web-designs", file_path="templates/<site>.md")`.
3. Apply the design tokens (colors, typography, components, layout, shadows).
4. Where you apply them depends on whether the website builder is available:

   - If the website builder is available (create_website / deploy_website) ->
     build a real, publishable site with the builder. Apply tokens to the React
     (TanStack Start + Tailwind) project, then deploy_website for a live URL.
     Pairs with landing-page-flow skill.
   - If the website builder is NOT available -> fall back to single-file HTML/CSS.

## Applying a design system in the website builder (preferred)

The builder project is TanStack Start + Tailwind + shadcn/ui (46 components).

1. Scaffold (if not already): `create_website(name="<brand>")` -> note source_path.
2. Fonts -- add the template's Google Fonts link to the document head (src/routes/__root.tsx).
3. Tokens -> CSS variables -- put the palette and shadow system as :root custom properties
   in src/styles.css (e.g. --color-bg, --color-text, --color-accent, --shadow-card).
4. Type scale and layout -- apply the typography hierarchy and spacing/layout rules.
5. Components -- build sections from the existing shadcn/ui components, styled with the tokens.
6. Publish -- deploy_website() -> return the live URL.

Sites are server-rendered per request -- keep components SSR-safe (no window/document/localStorage
at module top level or during render; use them only inside useEffect / handlers).

## Font Substitution Reference

Most sites use proprietary fonts unavailable via CDN. Common mappings:

| Proprietary Font | CDN Substitute | Character |
|---|---|---|
| Geist / Geist Sans | Geist (on Google Fonts) | Geometric, compressed tracking |
| Geist Mono | Geist Mono (on Google Fonts) | Clean monospace, ligatures |
| sohne-var (Stripe) | Source Sans 3 | Light weight elegance |
| Berkeley Mono | JetBrains Mono | Technical monospace |
| Airbnb Cereal VF | DM Sans | Rounded, friendly geometric |
| Circular (Spotify) | DM Sans | Geometric, warm |
| figmaSans | Inter | Clean humanist |
| Pin Sans (Pinterest) | DM Sans | Friendly, rounded |
| NVIDIA-EMEA | Inter (or Arial system) | Industrial, clean |
| CoinbaseDisplay/Sans | DM Sans | Geometric, trustworthy |
| UberMove | DM Sans | Bold, tight |
| HashiCorp Sans | Inter | Enterprise, neutral |
| waldenburgNormal (Sanity) | Space Grotesk | Geometric, slightly condensed |
| IBM Plex Sans/Mono | IBM Plex Sans/Mono | Available on Google Fonts |
| Rubik (Sentry) | Rubik | Available on Google Fonts |

## Design Catalog

### AI and Machine Learning

| Template | Site | Style |
|---|---|---|
| claude.md | Anthropic Claude | Warm terracotta accent, clean editorial layout |
| cohere.md | Cohere | Vibrant gradients, data-rich dashboard aesthetic |
| elevenlabs.md | ElevenLabs | Dark cinematic UI, audio-waveform aesthetics |
| minimax.md | Minimax | Bold dark interface with neon accents |
| mistral.ai.md | Mistral AI | French-engineered minimalism, purple-toned |
| ollama.md | Ollama | Terminal-first, monochrome simplicity |
| opencode.ai.md | OpenCode AI | Developer-centric dark theme, full monospace |
| replicate.md | Replicate | Clean white canvas, code-forward |
| runwayml.md | RunwayML | Cinematic dark UI, media-rich layout |
| together.ai.md | Together AI | Technical, blueprint-style design |
| voltagent.md | VoltAgent | Void-black canvas, emerald accent, terminal-native |
| x.ai.md | xAI | Stark monochrome, futuristic minimalism, full monospace |

### Developer Tools and Platforms

| Template | Site | Style |
|---|---|---|
| cursor.md | Cursor | Sleek dark interface, gradient accents |
| expo.md | Expo | Dark theme, tight letter-spacing, code-centric |
| linear.app.md | Linear | Ultra-minimal dark-mode, precise, purple accent |
| lovable.md | Lovable | Playful gradients, friendly dev aesthetic |
| mintlify.md | Mintlify | Clean, green-accented, reading-optimized |
| posthog.md | PostHog | Playful branding, developer-friendly dark UI |
| raycast.md | Raycast | Sleek dark chrome, vibrant gradient accents |
| resend.md | Resend | Minimal dark theme, monospace accents |
| sentry.md | Sentry | Dark dashboard, data-dense, pink-purple accent |
| supabase.md | Supabase | Dark emerald theme, code-first developer tool |
| superhuman.md | Superhuman | Premium dark UI, keyboard-first, purple glow |
| vercel.md | Vercel | Black and white precision, Geist font system |
| warp.md | Warp | Dark IDE-like interface, block-based command UI |
| zapier.md | Zapier | Warm orange, friendly illustration-driven |

### Infrastructure and Cloud

| Template | Site | Style |
|---|---|---|
| clickhouse.md | ClickHouse | Yellow-accented, technical documentation style |
| composio.md | Composio | Modern dark with colorful integration icons |
| hashicorp.md | HashiCorp | Enterprise-clean, black and white |
| mongodb.md | MongoDB | Green leaf branding, developer documentation focus |
| sanity.md | Sanity | Red accent, content-first editorial layout |
| stripe.md | Stripe | Signature purple gradients, weight-300 elegance |

### Design and Productivity

| Template | Site | Style |
|---|---|---|
| airtable.md | Airtable | Colorful, friendly, structured data aesthetic |
| cal.md | Cal.com | Clean neutral UI, developer-oriented simplicity |
| clay.md | Clay | Organic shapes, soft gradients, art-directed layout |
| figma.md | Figma | Vibrant multi-color, playful yet professional |
| framer.md | Framer | Bold black and blue, motion-first, design-forward |
| intercom.md | Intercom | Friendly blue palette, conversational UI patterns |
| miro.md | Miro | Bright yellow accent, infinite canvas aesthetic |
| notion.md | Notion | Warm minimalism, serif headings, soft surfaces |
| pinterest.md | Pinterest | Red accent, masonry grid, image-first layout |
| webflow.md | Webflow | Blue-accented, polished marketing site aesthetic |

### Fintech and Crypto

| Template | Site | Style |
|---|---|---|
| coinbase.md | Coinbase | Clean blue identity, trust-focused, institutional feel |
| kraken.md | Kraken | Purple-accented dark UI, data-dense dashboards |
| revolut.md | Revolut | Sleek dark interface, gradient cards, fintech precision |
| wise.md | Wise | Bright green accent, friendly and clear |

### Enterprise and Consumer

| Template | Site | Style |
|---|---|---|
| airbnb.md | Airbnb | Warm coral accent, photography-driven, rounded UI |
| apple.md | Apple | Premium white space, SF Pro, cinematic imagery |
| bmw.md | BMW | Dark premium surfaces, precise engineering aesthetic |
| ibm.md | IBM | Carbon design system, structured blue palette |
| nvidia.md | NVIDIA | Green-black energy, technical power aesthetic |
| spacex.md | SpaceX | Stark black and white, full-bleed imagery, futuristic |
| spotify.md | Spotify | Vibrant green on dark, bold type, album-art-driven |
| uber.md | Uber | Bold black and white, tight type, urban energy |

## Choosing a Design

- Developer tools / dashboards: Linear, Vercel, Supabase, Raycast, Sentry
- Documentation / content sites: Mintlify, Notion, Sanity, MongoDB
- Marketing / landing pages: Stripe, Framer, Apple, SpaceX
- Dark mode UIs: Linear, Cursor, ElevenLabs, Warp, Superhuman
- Light / clean UIs: Vercel, Stripe, Notion, Cal.com, Replicate
- Playful / friendly: PostHog, Figma, Lovable, Zapier, Miro
- Premium / luxury: Apple, BMW, Stripe, Superhuman, Revolut
- Data-dense / dashboards: Sentry, Kraken, Cohere, ClickHouse
- Monospace / terminal aesthetic: Ollama, OpenCode, x.ai, VoltAgent
