---
name: video-adapt
description: |
  Video adaptation pipeline — recreate or adapt a source video with new characters, products, or styling.
  Status: DISABLED. This workflow is not yet available. Do not use or load this skill.
---
# Video Adapt

**Status: DISABLED — this workflow is not yet available.**

Video adaptation allows recreating or adapting a source video URL with new characters, products, or visual styling. The pipeline analyzes the source video, segments it into chunks, and regenerates each chunk with the requested modifications while preserving the original structure, pacing, and narrative.

This skill is currently disabled and will return an error if invoked. When it becomes available, it will support:

- Avatar swap — replace the person in a video with a different character
- Product swap — adapt a video to feature a different product
- Style transfer — preserve the video structure while changing the visual style
- Voice adaptation — re-dub or translate the audio track

Do not attempt to use the `vadapt-adapt-avatar`, `vadapt-adapt-product`, or `vadapt-adapt-preserve` enhancer flows until this skill is enabled.
