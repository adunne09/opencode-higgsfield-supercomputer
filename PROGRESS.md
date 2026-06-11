# Supercomputer Parity Progress

Last updated: 2026-06-11

This file tracks parity between the Higgsfield Supercomputer capability inventory in `SUPERCOMPUTER_SPEC.md` and this OpenCode harness.

The current harness has two configured MCP servers in `.opencode/opencode.jsonc`:

- Playwright MCP: `playwright`, via `npx @playwright/mcp@latest`.
- Higgsfield MCP: `higgsfield`, via `https://mcp.higgsfield.ai/mcp`.

## Done

- [x] Added `.opencode/opencode.jsonc` with Playwright MCP enabled.
- [x] Added `.opencode/opencode.jsonc` with Higgsfield MCP enabled.
- [x] Created `SUPERCOMPUTER_SPEC.md` as the Supercomputer capabilities inventory.
- [x] Implemented all AI Employees from `SUPERCOMPUTER_SPEC.md` as OpenCode skills.
- [x] Implemented all plain skills from `SUPERCOMPUTER_SPEC.md` as OpenCode skills.
- [x] Verified expected skill count equals actual skill count: 41 expected, 41 present.
- [x] Verified every skill folder has a matching `name:` frontmatter value.
- [x] Added referenced skill support files under `references/` and `templates/`.
- [x] Removed stale uppercase MD-flow skill names from active skill references.
- [x] Normalized generic `higgsfield_generate` wording to specific image/video/audio tool names in skills.
- [x] Represented `video-adapt` as a disabled skill, matching its current `SUPERCOMPUTER_SPEC.md` status.

## Current Coverage Summary

- Playwright/browser automation: mostly covered by Playwright MCP, with some Supercomputer tool names requiring aliases or wrapper documentation.
- Higgsfield generation/assets: broadly covered by Higgsfield MCP, but several `SUPERCOMPUTER_SPEC.md` names are conceptual names that map to differently named MCP tools.
- AI Employees: covered by `.opencode/skills`.
- Plain skills: covered by `.opencode/skills`.
- Supercomputer-only utilities: partially covered by OpenCode native tools, not all exposed through this repo configuration.
- Research/social/web toolsets: not yet represented in `.opencode/opencode.jsonc` beyond what current OpenCode provides natively.

## Playwright MCP Mapping

Browser tools in `SUPERCOMPUTER_SPEC.md` map to the configured Playwright MCP server, not to Higgsfield.

| `SUPERCOMPUTER_SPEC.md` capability | OpenCode / MCP mapping | Status | Notes |
|---|---|---:|---|
| `browser_navigate` | `playwright_browser_navigate` | Done | Direct Playwright MCP equivalent. |
| `browser_snapshot` | `playwright_browser_snapshot` | Done | Direct Playwright MCP equivalent. |
| `browser_click` | `playwright_browser_click` | Done | Direct Playwright MCP equivalent. |
| `browser_type` | `playwright_browser_type` / `playwright_browser_fill_form` | Done | Direct coverage through Playwright MCP. |
| `browser_scroll` | Playwright key/evaluate interaction | Partial | No exact exposed `browser_scroll` tool is currently visible; scrolling can be done through Playwright keyboard/evaluate flows. Add alias/wrapper if exact parity is required. |
| `browser_back` | `playwright_browser_navigate_back` | Done | Direct Playwright MCP equivalent. |
| `browser_press` | `playwright_browser_press_key` | Done | Direct Playwright MCP equivalent. |
| `browser_screenshot` | `playwright_browser_take_screenshot` | Done | Direct Playwright MCP equivalent. |
| `browser_vision` | Screenshot plus model analysis | Missing | Playwright exposes screenshots, but no single screenshot-plus-vision tool is configured here. |
| `browser_console` | `playwright_browser_console_messages` / `playwright_browser_evaluate` | Done | Covered by Playwright MCP. |
| `browser_get_images` | Playwright evaluate | Partial | Images can be collected with page evaluation, but no exact `browser_get_images` tool is exposed. Add wrapper if exact parity is required. |
| `web_search` | None in repo config | Missing | Needs search/web MCP or another configured toolset. |
| `web_extract` | None in repo config | Missing | Needs web extraction MCP/toolset. Current local session may have `webfetch`, but repo config does not define Supercomputer-style `web_extract`. |
| `extract_document` | None in repo config | Missing | Needs document extraction/data ingestion toolset. |

## Higgsfield MCP Mapping

Higgsfield tools in `SUPERCOMPUTER_SPEC.md` map to the configured Higgsfield MCP server. Some names in `SUPERCOMPUTER_SPEC.md` are higher-level capability names; the exposed MCP tool names are more specific.

| `SUPERCOMPUTER_SPEC.md` capability | Higgsfield MCP mapping | Status | Notes |
|---|---|---:|---|
| `higgsfield_generate_image` | `higgsfield_generate_image` | Done | Direct MCP tool. |
| `higgsfield_generate_models_explore` | `higgsfield_models_explore` | Done, rename | MCP exposes `higgsfield_models_explore`; `SUPERCOMPUTER_SPEC.md` uses the longer conceptual name. |
| `higgsfield_upscale_image` | `higgsfield_upscale_image` | Done | Direct MCP tool. |
| `higgsfield_outpaint` | `higgsfield_outpaint_image` | Done, rename | MCP exposes image-specific outpaint tool. |
| `higgsfield_remove_background` | `higgsfield_remove_background` | Done | Direct MCP tool. |
| `higgsfield_generate_video` | `higgsfield_generate_video` | Done | Direct MCP tool. |
| `higgsfield_upscale_video` | `higgsfield_upscale_video` | Done | Direct MCP tool. |
| `higgsfield_reframe` | `higgsfield_reframe` | Done | Direct MCP tool. |
| `higgsfield_motion_control` | `higgsfield_motion_control` | Done | Direct MCP tool. |
| `higgsfield_soul_id` | `higgsfield_show_characters` plus `soul-id` skill | Done, adapter | MCP exposes Soul character management through `higgsfield_show_characters`; workflow is documented in `soul-id`. |
| `higgsfield_element` | `higgsfield_show_reference_elements` | Done, rename | MCP exposes reference element management through `higgsfield_show_reference_elements`. |
| `higgsfield_upload` | `higgsfield_media_upload`, `higgsfield_media_upload_widget`, `higgsfield_media_confirm`, `higgsfield_media_import_url` | Done, split | Upload/import is split into multiple MCP tools. |
| `higgsfield_attachments_list` | `higgsfield_show_generations`, `higgsfield_show_medias` | Done, split | Prior generations and uploaded media are separate MCP tools. |
| `higgsfield_balance` | `higgsfield_balance` | Done | Direct MCP tool. |
| `higgsfield_enhancer` | Not visible as an exposed MCP tool in current harness | Missing / needs verification | Skills reference enhancer flows from `SUPERCOMPUTER_SPEC.md`, but the current visible MCP tool list does not include a direct `higgsfield_enhancer` tool. Confirm whether enhancer behavior is embedded in another Higgsfield tool or must be exposed. |
| `higgsfield_job_status` | `higgsfield_job_status`, `higgsfield_job_display` | Done | Status and display are exposed separately. |
| `higgsfield_audio_generate` | Not visible as an exposed MCP tool in current harness | Missing / needs verification | `SUPERCOMPUTER_SPEC.md` and `audio-generation` expect this tool. Confirm whether Higgsfield MCP exposes audio generation in this OpenCode config, or add/load the required audio MCP/toolset. |

## Additional Higgsfield MCP Tools Present

These Higgsfield MCP tools are visible in the current harness and extend beyond the short `SUPERCOMPUTER_SPEC.md` inventory. They can be mapped into `SUPERCOMPUTER_SPEC.md` later if the inventory should be exhaustive.

- [ ] `higgsfield_show_marketing_studio` -> Marketing Studio products, avatars, presets, hooks, settings, brand kits, ad references.
- [ ] `higgsfield_show_plans_and_credits` -> plans, upgrades, and credit purchases.
- [ ] `higgsfield_transactions` -> credit transaction history.
- [ ] `higgsfield_list_workspaces` / `higgsfield_select_workspace` -> workspace management.
- [ ] `higgsfield_personal_clipper_create`, `higgsfield_personal_clipper_jobs`, `higgsfield_personal_clipper_status` -> YouTube clipping workflow.
- [ ] `higgsfield_video_analysis_create`, `higgsfield_video_analysis_jobs`, `higgsfield_video_analysis_status` -> scene-by-scene video analysis.
- [ ] `higgsfield_virality_predictor` -> virality and creative-performance analysis.
- [ ] `higgsfield_presets_show` -> image-to-video preset browsing.
- [ ] `higgsfield_reveal_generation` -> reveal IP-detected generation after rights confirmation.
- [ ] `higgsfield_sync_agents` -> sync user-authored skills/personality into Higgsfield.

## OpenCode / Supercomputer Utility Mapping

These `SUPERCOMPUTER_SPEC.md` capabilities are not provided by the Higgsfield or Playwright MCPs themselves. Some are native to OpenCode or available in this chat runtime, but they are not fully represented by this repo's `.opencode/opencode.jsonc` yet.

| `SUPERCOMPUTER_SPEC.md` capability | Current mapping | Status | Notes |
|---|---|---:|---|
| `memory` | Not configured in repo | Missing / external | Needs memory toolset if this harness must match Supercomputer memory behavior. |
| `artifacts` | Not configured in repo | Missing / external | Needs artifacts toolset or explicit OpenCode storage pattern. |
| `artifact_get` / `artifact_put` | Not configured in repo | Missing / external | Needs artifacts/key-value toolset. |
| `todo` | OpenCode session todo exists in this runtime | Partial | Available to the agent runtime, but not represented as repo MCP config. |
| `tool_output_grep` | Not configured in repo | Missing / external | Needs equivalent stored-output search facility. |
| `terminal` | OpenCode native shell tool | Partial | Available in the runtime, not from `.opencode/opencode.jsonc`. |
| `process` | Not configured in repo | Missing / external | Needs process/background-process toolset if exact parity is required. |
| `delegate_task` | OpenCode task/subagent runtime | Partial | Available in this runtime, not from `.opencode/opencode.jsonc`. |
| `ask_user_question` | OpenCode question tool in this runtime | Partial | Available in this runtime, not from `.opencode/opencode.jsonc`. |
| `schedule` | Not configured in repo | Missing / external | Needs scheduling toolset. |
| `skill_manage` | File-backed skill edits in repo | Partial | Skills can be edited as files; no explicit `skill_manage` MCP tool is configured. |
| `skill_view` | OpenCode skill loading in runtime | Partial | Available in this runtime, not from `.opencode/opencode.jsonc`. |
| `skills_list` | OpenCode available skills list | Partial | Available from runtime context, not as repo MCP config. |
| `tool_search` | Not configured in repo | Missing / external | Needs Supercomputer-style dynamic toolset loader if required. |

## AI Employee Skill Mapping

All AI Employees from `SUPERCOMPUTER_SPEC.md` map to skill files under `.opencode/skills`.

| AI Employee | Skill | Status |
|---|---|---:|
| AI Influencer | `ai-influencer-flow` | Done |
| Amazon Listing Designer | `amazon-product-listing` | Done |
| Cartoon Animator | `cartoon-flow` | Done |
| Cinematic Director | `cinematic-flow` | Done |
| Motion Designer | `classicmd-flow` | Done |
| Premium Motion Designer | `highmd-flow` | Done |
| Image Generator | `image-generation` | Done |
| Infographic Animator | `infographicmd-flow` | Done |
| Podcast Producer | `podcast-flow` | Done |
| Product Photographer | `product-photoshoot` | Done |
| Product Animator | `productmd-flow` | Done |
| Text Generator | `text-generation` | Done |
| TV Ad Director | `tv-ad` | Done |
| Typography Animator | `typographymd-flow` | Done |
| UGC Creator | `ugc-flow` | Done |
| Product UGC Reviewer | `ugc-product-flow` | Done |
| Try-On UGC Stylist | `ugc-try-on-flow` | Done |
| Tutorial UGC Creator | `ugc-tutorial-flow` | Done |
| Unboxing UGC Creator | `ugc-unboxing-flow` | Done |
| Video Generator | `video-generation` | Done |

## Plain Skill Mapping

All plain skills from `SUPERCOMPUTER_SPEC.md` map to skill files under `.opencode/skills`.

| Category | Skill | Status |
|---|---|---:|
| Analyzer | `brand-analyzer` | Done |
| Analyzer | `product-analyzer` | Done |
| Creative | `creative-ideation` | Done |
| Creative | `design-md` | Done |
| Creative | `excalidraw` | Done |
| Creative | `infographic` | Done |
| Creative | `popular-web-designs` | Done |
| Creative | `songwriting-and-ai-music` | Done |
| Media | `audio-generation` | Done, blocked on exposed audio tool verification |
| Media | `montage` | Done |
| Media | `soul-id` | Done |
| Media | `youtube-content` | Done |
| Media | `youtube-research` | Done |
| Productivity | `create-skill` | Done |
| Productivity | `maps` | Done |
| Productivity | `pdf` | Done |
| Productivity | `powerpoint` | Done |
| Research | `trend-picker` | Done |
| Social Media | `organic-marketing` | Done |
| Workflow Generation | `landing-page-flow` | Done |
| Workflow Generation | `video-adapt` | Done, intentionally disabled |

## Prompt Enhancer Flow Mapping

The skills reference these `SUPERCOMPUTER_SPEC.md` prompt enhancer flow groups. End-to-end parity depends on exposing or confirming the `higgsfield_enhancer` capability in the Higgsfield MCP server.

| Enhancer group | Skill mapping | Status |
|---|---|---:|
| UGC flows | `ugc-flow`, `ugc-product-flow`, `ugc-try-on-flow`, `ugc-tutorial-flow`, `ugc-unboxing-flow` | Skill mapping done; enhancer tool verification needed |
| TV Ad flows | `tv-ad` | Skill mapping done; enhancer tool verification needed |
| Cinematic flows | `cinematic-flow`, `text-generation` references | Skill mapping done; enhancer tool verification needed |
| Cartoon flows | `cartoon-flow` | Skill mapping done; enhancer tool verification needed |
| Motion Design flows | `classicmd-flow`, `highmd-flow`, `infographicmd-flow`, `productmd-flow`, `typographymd-flow` | Skill mapping done; enhancer tool verification needed |
| Video Adapt flows | `video-adapt` | Skill present, workflow intentionally disabled |

## Remaining Parity Work

- [ ] Confirm whether Higgsfield MCP exposes `higgsfield_enhancer`; if not, add the missing tool or update skills to use the correct exposed MCP mechanism.
- [ ] Confirm whether Higgsfield MCP exposes `higgsfield_audio_generate`; if not, add/load the Higgsfield audio toolset or update `audio-generation` and `landing-page-flow` to the correct exposed mechanism.
- [ ] Decide whether `SUPERCOMPUTER_SPEC.md` should use exact MCP tool names or conceptual Supercomputer names for renamed/split Higgsfield tools.
- [ ] Add exact browser aliases/wrappers for `browser_scroll`, `browser_vision`, and `browser_get_images` if exact Supercomputer browser parity is required.
- [ ] Add or configure web/search/document extraction coverage for `web_search`, `web_extract`, and `extract_document`.
- [ ] Add or configure state/utility parity for memory, artifacts, process management, scheduling, and dynamic tool loading if this harness must reproduce the full Supercomputer runtime.
- [ ] Decide whether additional visible Higgsfield MCP tools should be added to `SUPERCOMPUTER_SPEC.md` so the inventory is exhaustive for this harness.

## Notes

- AI Employees are intentionally represented as OpenCode skills, not `.opencode/agents` files.
- `.opencode/opencode.jsonc` is the source of truth for MCP server wiring in this repo.
- The Playwright MCP server provides the browser automation surface.
- The Higgsfield MCP server provides the Higgsfield generation, asset, billing, workspace, analysis, and related surfaces currently visible to OpenCode.
- Skill parity and tool parity are tracked separately: a skill can exist and still depend on a missing MCP tool.
