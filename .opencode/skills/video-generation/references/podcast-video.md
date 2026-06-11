# Podcast video -- Seedance chunk authoring

Authoring `seedance_2_0` prompts for sit-down conversational podcast video.
A chunk is one `seedance_2_0` generation: `aspect_ratio: "16:9"`, `resolution: "1080p"`,
`duration: 8-15s`, native audio, optional `medias` (up to 4 reference image jobs).

## Core rules

- Hosts speak themselves -- every line is a quoted literal reply tagged `on-camera, mouth synced to speech` OR `voice-over from off-camera -- do not sync any visible mouth`, plus `silent -- mouth stays closed` for a visible non-speaker.
- Attribute lines to a **concrete visible descriptor** (wardrobe color, hair, glasses, position) -- never opaque tokens like Host1/Host2/LEFT HOST.
- No eyes-to-camera -- hosts film a private conversation with each other. Forbidden: "speaking to camera", "looking at the viewer", "addressing the audience".
- Camera stays on the same side of the 180 line as the composite (@Image2).
- Shot algebra (no-repeat invariant): for adjacent shots Sn, Sn+1, the pair (kind, character) must change. WIDE->WIDE forbidden. CU(X)->CU(X) forbidden. WIDE->CU(X), CU(X)->WIDE, CU(X)->CU(Y) with X != Y all allowed.

## Tool call

```
higgsfield_generate_video(params={
  "model": "seedance_2_0",
  "prompt": "<see templates below>",
  "aspect_ratio": "16:9",
  "resolution": "1080p",
  "duration": 15,
  "medias": [
    {"value": "STORYBOARD_JOB_ID", "role": "image"},
    {"value": "COMPOSITE_JOB_ID", "role": "image"},
    {"value": "SPEAKER_HERO_JOB_ID", "role": "image"},
    {"value": "LISTENER_HERO_JOB_ID", "role": "image"}
  ]
})
```

## Shot patterns (4 shots per 12-15s chunk)

Pattern A: [WIDE, CU(speaker), WIDE shifted, CU(listener)] -- default dialog turn
Pattern B: [CU(speaker), WIDE, CU(speaker diff angle), CU(listener)] -- monologue
Pattern C: [WIDE, CU(speaker), WIDE both react, CU(listener)] -- comedic/surprise
Pattern D: [CU(A), WIDE handoff, CU(B), CU(A)] -- speaker swap
Pattern E: [WIDE sustained] -- atmosphere/contemplation only (use sparingly)

## Dialog labeling (per line)

- `(on-camera, mouth synced to speech)` -- speaker visible in frame
- `(continuing from the previous shot, on-camera, mouth synced)` -- carry-over same speaker
- `(continuing from the previous shot, voice-over from off-camera -- do not sync any visible mouth)` -- speaker off-camera while listener is visible
- `(silent -- mouth stays closed, not speaking this line, only micro-reaction)` -- visible non-speaker

**Critical:** any dialog on a close-up of the listener must use voice-over labeling for the speaker and silent labeling for the visible listener.

## Episode continuity

Multi-chunk = one continuous episode sliced in time. Plan arc (logline + per-chunk beats) outside the Seedance prompt. Each chunk's first line resumes the previous chunk's open thought. No cold opens in chunks 2+. Only the final chunk has a ~1.5s silent tail. Intermediate chunks run dialog to full duration.

## Audio

Native dialog with phoneme-level lipsync on shots where speaker is visible. Voice-over only (no mouth to sync) on shots where speaker is off-camera. Ambient SFX: room tone, distant city ambience, soft chair creak. No music in prompt -- music added in post. AUDIO CONTINUITY: no unintended silence > ~2s.

## Tone modes

Mode 1 Conversational: 2.2-2.8 wps, relaxed natural pacing
Mode 2 Investigative: 1.8-2.2 wps, thoughtful pauses, genuine curiosity
Mode 3 Documentary gravitas: 1.5-1.8 wps, authoritative (tone for existing host, NOT a separate narrator)
Mode 4 Comedic/banter: 2.8-3.5 wps, faster, reactive

## Quality suffix (every chunk)

NO autosubs. NO captions. FINAL OUTPUT: one single continuous full-frame 16:9 cinematic video, not a panel grid.
