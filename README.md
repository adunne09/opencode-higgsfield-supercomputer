# OpenCode Higgsfield Supercomputer

An OpenCode harness for running Higgsfield Supercomputer-style creative workflows from a local project.

This repo contains:

- `.opencode/opencode.jsonc` MCP configuration for Playwright and Higgsfield.
- `.opencode/skills/` workflow skills for Higgsfield AI Employees and supporting plain skills.
- `SUPERCOMPUTER_SPEC.md`, the capability inventory this harness is tracking against.
- `PROGRESS.md`, the parity tracker for what is covered and what still needs work.

## Prerequisite

Install OpenCode first:

- OpenCode: https://opencode.ai
- OpenCode docs: https://opencode.ai/docs

You will also need access to the configured MCP services:

- Higgsfield MCP: `https://mcp.higgsfield.ai/mcp`
- Playwright MCP: installed on demand through `npx @playwright/mcp@latest`

## Use This Harness

Clone the repo:

```sh
git clone git@github.com:adunne09/opencode-higgsfield-supercomputer.git
cd opencode-higgsfield-supercomputer
```

Start OpenCode from the cloned project directory:

```sh
opencode
```

From there, scaffold the content or project you want to create inside this cloned workspace. The included OpenCode skills and MCP configuration are meant to give the agent access to the Higgsfield creative workflows, browser automation, and the local project files in one place.

Example prompts to run from this project:

```text
Create a UGC product review video workflow for this product URL.
```

```text
Build a landing page for this brand using Higgsfield-generated visuals.
```

```text
Generate a premium product commercial concept and scaffold the assets here.
```

## How It Is Organized

- `.opencode/opencode.jsonc` configures the MCP servers OpenCode should load for this project.
- `.opencode/skills/` contains the reusable workflows OpenCode can invoke.
- `SUPERCOMPUTER_SPEC.md` documents the target Supercomputer capability set.
- `PROGRESS.md` tracks parity between that target capability set and this OpenCode harness.

## Notes

- Run OpenCode from the root of this cloned repo so it picks up `.opencode/opencode.jsonc` and the local skills.
- Treat this repo as the workspace where you create or scaffold new content unless you intentionally copy the `.opencode/` harness into another project.
- `video-adapt` is included as a disabled workflow until the backend capability is available.
