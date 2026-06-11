# Template - Gallery Wall

Infinite draggable wall of media tiles with magnetic hover, 3D tilt, intro burst.
Click a tile for a lightbox. For portfolio, gallery, art collection, lookbook, merch shop.

## Quick path

1. `create_website(name="<brand>", template="gallery-wall")` -> note source_path
2. Assemble 12-30 images:
   - User supplied? Download/copy each to public/media/
   - Otherwise: generate ONE brandkit frame first (style reference), then generate
     each tile as its OWN distinct subject in that style (pass brandkit as role:"image" NOT start_image)
3. Fill src/collection.ts: items[] array + branding object
4. Set --wall-* palette in src/styles.css (sample from brandkit frame)
5. `deploy_website()` -> return live URL

## Content types

- Portfolio: tile title = project title
- Gallery: tile title = piece name  
- Merch shop: tile title = product name; add cornerLink href to external store

## Key files

- src/collection.ts: items[] and branding (DO NOT edit GalleryWall.tsx / Magnet.tsx)
- src/styles.css: --wall-bg, --wall-ink, --wall-accent, --wall-focus palette
- public/media/: your tile images (referenced as /media/<file> root-absolute)
