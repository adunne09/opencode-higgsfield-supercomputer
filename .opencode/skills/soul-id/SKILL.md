---
name: soul-id
description: |
  Face identity training and management. Trains a persistent identity model
  (Soul ID) from 1-100 face photos OR bootstraps one from a text description,
  then generates likeness-preserving images via Soul 2.0. Use when the same
  person must appear consistently across multiple generations.
---
# Soul ID Skill

Create trained face identity models (Soul IDs) from photos, then generate images with them.

## What is a Soul ID?

A Soul ID is a trained identity model. Upload 1-100 face photos, the backend trains a model,
and you can generate new images preserving that person's likeness using Soul 2.0.

## Identity Continuity Preflight (run before training)

Soul ID training is slow (up to 30 min) and not always necessary. Before starting, answer:

1. Does the request describe a named or implied persona with a face?
2. Will that persona appear in 2+ separate generations within this session or downstream pipeline?
3. Is there an existing identity source already available (trained soul_id, element_id, reference job)?

Decision matrix:
- (1) AND (2) AND NOT (3) -> identity-continuity path is needed.
  - For image carousel / stills: no-training path via nano_banana_2 with reference is equally valid.
    Present the 50/50 choice to the user before loading this skill.
  - For video / multi-reel: Soul ID is canonical -- proceed directly.
- (3) is true -> reuse the existing soul_id / element directly. Skip training.
- (2) is false (one-shot) -> use Soul Cast or text2image_soul_v2 without training.
- (1) is false (no specific identity) -> do not train; let the model choose freely.

## Workflow

### 0. Bootstrap from Description (fictional persona, no source photos)

Use when the persona does not exist in reality and no photos are available.
Two variants -- pick based on context:

- Standard (sequential): Generate one seed portrait -> poll -> generate 4 augmentations.
  Best when the persona needs no other images generated.
- Parallel (persona-clone / content-plan pipelines): Generate 5+ training images simultaneously
  as distinct scenes/outfits from the same character description. Use when a content plan will
  produce multiple images anyway -- the content images ARE the training set.

#### Standard Steps

1. Seed portrait via text2image_soul_v2 (editorial/fashion) or nano_banana_2 (photorealistic).
   Include: age, skin tone, hair color/texture/length, eye color, face shape, build, outfit,
   neutral background, natural light, authentic skin texture. Aspect ratio 3:4.

2. Poll the seed until completed (mandatory -- augmentation batch needs the seed job id).

3. Augmentation batch -- generate 4 variations using nano_banana_2 with the seed as reference.
   Universal augmentation prompt:
   "Generate an image featuring the exact same person from the input reference,
    maintaining high facial fidelity. The context is completely changed to a new
    realistic setting with the subject wearing a new outfit. Position: [new pose].
    View: [new shot type]. Style must perfectly match the original aesthetic."

   Suggested 4-item variation axis:
   - Item 1 -- Position: standing; View: medium shot, waist-up
   - Item 2 -- Position: sitting or leaning forward; View: close-up portrait
   - Item 3 -- Position: walking or mid-motion; View: full body shot
   - Item 4 -- Position: head turn; View: side profile headshot

4. Poll all 4 augmentations until completed.

5. Download all 5 images (seed + 4 augmentations) to ./assets/soul-bootstrap-<slug>/.

6. Train: higgsfield_soul_id(action="create", dir="./assets/soul-bootstrap-<slug>", name="<persona>", poll=true)
   Capture the returned reference_id.

7. Create a character element (only if persona will be used in seedance_2_0 videos).
   Reuse seed.png from step 5 -- do not generate a separate image.
   higgsfield_upload(files=["./assets/soul-bootstrap-<slug>/seed.png"])
   higgsfield_element(action="create", category="character_ip_verified", name="<persona>",
     medias=[{"id": "<UPLOAD_ID>", "url": "<UPLOAD_URL>", "type": "media_input"}])
   Use returned element id in seedance prompts via <<<ELEMENT_ID>>>.

8. (Optional) Verify by generating one test image via the new Soul ID.

#### Parallel Bootstrap Variant

1. Generate all content images in one parallel batch via text2image_soul_v2.
   Vary scene, outfit, setting, and aspect ratio. Aim for 4-6 images minimum.
   All run concurrently -- no dependencies between them.

2. Simultaneously submit video (reel) generation calls.

3. Poll all image jobs until completed.

4. Download the 3:4 portrait images (face-clear ones):
   mkdir -p ./assets/soul-bootstrap-<slug>
   curl -sL -o ./assets/soul-bootstrap-<slug>/img1.png "<URL_1>"

5. Train: higgsfield_soul_id(action="create", dir="./assets/soul-bootstrap-<slug>", name="<persona>", poll=true)

6. Create character element -- upload one downloaded portrait and create element.

### 1. Collect Images (from Instagram)

```
# Get user posts with image URLs
instagram_research(action="user_posts", username="USERNAME", depth=2)
# Each post has display_url; carousel posts also have carousel: [{display_url, is_video}, ...]
```

Image selection tips:
- Prefer clear face shots (front-facing, good lighting)
- Include variety: different angles, expressions, lighting
- Avoid group photos where the target face is small
- Skip heavily filtered/edited images
- 10-30 high-quality face photos is the sweet spot

### 2. Download Images

```bash
mkdir -p ./assets/soul-id-USERNAME
curl -L -o ./assets/soul-id-USERNAME/01.jpg "URL_1"
curl -L -o ./assets/soul-id-USERNAME/02.jpg "URL_2"
```

### 3. Create Soul ID

```
higgsfield_soul_id(
  action="create",
  dir="./assets/soul-id-USERNAME",
  name="Person Name",
  poll=true
)
```

This call discovers all images (jpg, png, webp, heic, heif) in the directory,
batch-uploads them, creates a Soul 2.0 custom reference, and polls until training
completes (up to 30 minutes when poll=true).

Output includes reference_id -- save this for generation.

### 4. Generate with Soul ID

```
higgsfield_generate_image(requests=[
  {
    "type": "generation",
    "model": "text2image_soul_v2",
    "params": {
      "prompt": "a portrait photo in a garden, soft natural lighting",
      "soul_id": "REFERENCE_ID",
      "soul_strength": 1.0,
      "aspect_ratio": "3:4",
      "batch_size": 4
    }
  }
])
```

### 5. Management Calls

```
# List Soul IDs
higgsfield_soul_id(action="list")
# List only completed
higgsfield_soul_id(action="list", status="completed")
# Check status
higgsfield_soul_id(action="status", reference_id="REFERENCE_ID")
# Delete
higgsfield_soul_id(action="delete", reference_id="REFERENCE_ID")
```

## Parameters Reference

### soul-id create
| Flag | Required | Default | Description |
|---|---|---|---|
| action="create" | Yes | -- | Create Soul ID |
| dir | Yes | -- | Directory containing images |
| name | No | "Soul ID" | Name for the Soul ID |
| poll | No | false | Poll until training completes |

### text2image_soul_v2 params
| Field | Required | Default | Description |
|---|---|---|---|
| model | Yes | -- | Always "text2image_soul_v2" |
| prompt | Yes | -- | Text prompt |
| soul_id | No | -- | Custom reference ID |
| soul_strength | No | 1.0 | Likeness strength (0.0-1.0) |
| aspect_ratio | No | "3:4" | "3:4", "1:1", "9:16", "16:9" |
| batch_size | No | 1 | Number of images (1-4) |
| quality | No | "1080p" | Quality level |

## Error Handling

| Error | Fix |
|---|---|
| no image files found | Directory is empty or has no supported image formats |
| too many images (N) | Maximum 100 images -- remove some |
| soul id training failed | Bad input images -- retry with better face photos |
| training timed out | Use higgsfield_soul_id(action="status", reference_id=X, poll=true) |
| status 422 | Invalid request -- check image formats and count |
