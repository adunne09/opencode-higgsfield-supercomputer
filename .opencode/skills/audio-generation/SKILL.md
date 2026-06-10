---
name: audio-generation
description: Always read this SKILL.md before any audio task -- text-to-speech voiceover, swapping the voice in an existing video, or dubbing/translating a video into another language. Covers mode selection, voice picker usage, TTS model picks, async/sync polling, and ask_user_question rules.
---
# Audio Generation

Pick the mode, pick a voice, submit via `higgsfield_audio_generate`. The tool is batch-shaped -- always pass `requests: [...]` even for one job.

## User-facing style

Keep chat text relaxed and non-technical. Do not narrate routing decisions, tool names, job IDs, payload fields, or JSON values to the user.

For clear requests with a selected voice, submit directly with no preamble. Say one short plain-language line:
- Voiceover: "Started the voiceover."
- Change voice: "Started changing the voice."
- Translate / dub: "Started dubbing this into Chinese."

## When to use this skill

Trigger when the user's intent is one of:

- Voiceover (text-to-speech): "make audio of X saying Y", "voiceover this script", "read this aloud".
- Change voice: "swap the voice in this video", "replace the narrator", "use Mark's voice for this clip".
- Translate / dub: "translate this video to Spanish", "dub this clip into French".

Do NOT trigger for:
- Music or sound-effect generation (voice-only tool).
- Speech-to-text / transcription.

## Mode selection

| User intent | type |
|---|---|
| "Make audio of X saying Y" / "voiceover for ..." | voiceover |
| "Change the voice in this video" / "swap narrator" | change_voice |
| "Translate this video to <language>" / "dub into ..." | translate |

## Voiceover workflow

Required params:
- type: "voiceover"
- prompt: the exact text the voice should speak -- verbatim, no narration framing.
- model: TTS engine (see Model picks below).
- voice_id: UUID returned by the voice picker.

Defaults to async -- the tool returns job_ids immediately. Poll higgsfield_job_status(job_ids=[...], poll=true) only when the user asks for the result.

### Example (single job)
```json
{
  "requests": [
    {
      "type": "voiceover",
      "prompt": "Welcome to our daily roundup.",
      "model": "elevenlabs",
      "voice_id": "<selected_voice_uuid>"
    }
  ]
}
```

## Change voice workflow

Required params:
- type: "change_voice"
- voice_id: UUID returned by the voice picker.
- input_video: {"id": "<uuid>", "type": "<source-type>"}
  - Prior video-generation result: pass originating job_id and type matching the source job kind (e.g. seedance_job, veo3_job, kling_job).
  - User-uploaded video: use type: "video_input".

### Example
```json
{
  "requests": [
    {
      "type": "change_voice",
      "voice_id": "<selected_voice_uuid>",
      "input_video": {"id": "<job_or_upload_uuid>", "type": "seedance_job"}
    }
  ]
}
```

## Translate workflow

Required params:
- type: "translate"
- target_language: ISO 639-3 code (e.g. "spa", "fra", "cmn"). English names like "Spanish" are also accepted.
- input_video: same shape as change_voice.

Do not pass voice_id for translate.

### Example
```json
{
  "requests": [
    {
      "type": "translate",
      "target_language": "spa",
      "input_video": {"id": "<job_or_upload_uuid>", "type": "seedance_job"}
    }
  ]
}
```

### Supported languages

ISO 639-3 codes: afr Afrikaans, ara Arabic, hye Armenian, asm Assamese, aze Azerbaijani, bel Belarusian, ben Bengali, bos Bosnian, bul Bulgarian, cat Catalan, ceb Cebuano, nya Chichewa, hrv Croatian, ces Czech, dan Danish, nld Dutch, eng English, est Estonian, fil Filipino, fin Finnish, fra French, glg Galician, kat Georgian, deu German, ell Greek, guj Gujarati, hau Hausa, heb Hebrew, hin Hindi, hun Hungarian, isl Icelandic, ind Indonesian, gle Irish, ita Italian, jpn Japanese, jav Javanese, kan Kannada, kaz Kazakh, kir Kyrgyz, kor Korean, lav Latvian, lin Lingala, lit Lithuanian, ltz Luxembourgish, mkd Macedonian, msa Malay, mal Malayalam, cmn Mandarin Chinese, mar Marathi, nep Nepali, nor Norwegian, pus Pashto, fas Persian, pol Polish, por Portuguese, pan Punjabi, ron Romanian, rus Russian, srp Serbian, snd Sindhi, slk Slovak, slv Slovenian, som Somali, spa Spanish, swa Swahili, swe Swedish, tam Tamil, tel Telugu, tha Thai, tur Turkish, ukr Ukrainian, urd Urdu, vie Vietnamese, cym Welsh.

## Voice selection

For voiceover and change_voice, if voice_id is missing, ask with the voice entity picker:
- kind: "entity", entity: {type: "voice"}
- The frontend renders preset and custom voice cards; returns UUID.

Do not pass voice names. Do not fetch or resolve the voice catalog in Hermes.

## Model picks (voiceover)

| Model | When to use |
|---|---|
| elevenlabs (default) | High-quality general TTS. Pick this when in doubt. |
| minimax | High-quality, strong voice-cloning support. |
| seed_speech | ByteDance Seed Speech -- good for multilingual. |
| vibe_voice | Multi-speaker / dialog styles. |

## Async vs sync

- Async (default): returns job_ids immediately; chat stays responsive.
- Sync (async=false): blocks up to timeout_seconds and adds results. Use only when caller explicitly wants the URL inline.

## Polling -- Fire-and-Forget

Default: fire-and-forget. Submit, hold job IDs internally, announce only after at least one job starts.

Poll only when:
- The user explicitly asks for the audio file / URL.
- A downstream skill needs the audio result inside the same turn.

Poll call: higgsfield_job_status(job_ids=["JOB_ID"], poll=true). The finished job's result.url is the audio file link.

## Error Handling

| Error | Fix |
|---|---|
| voice_id is required | Ask the user to choose a voice from the picker. |
| prompt is required (voiceover) | Ask the user for the text to speak. |
| Job failed | Explain in plain language; offer to retry. |
| Sync timeout | Switch to async and poll later. |

## ask_user_question rules

DO ask:
- Voice -- when voice_id is missing for voiceover or change_voice, use the voice entity picker (kind: "entity", entity: {type: "voice"}).
- Language (translate only) -- when user asks to translate but hasn't named a target language, use the language entity picker (kind: "entity", entity: {type: "language"}).
- Type -- only when the request is genuinely ambiguous between modes.

NEVER ask:
- For a model when the user did not mention one -- default to elevenlabs.
- For sync vs async -- default to async.
- For confirmation before submitting.
