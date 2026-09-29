# AGENTS.md — pod-chrome-devtools-mcp

Standalone candy repo for the `chrome-devtools-mcp` candy — the Chrome DevTools
MCP server (Streamable HTTP on `9224`, bridged from stdio by `mcp-proxy`). The
candy lives in `charly.yml` at the repo root plus its build inputs (`package.json`
for the npm server, `pixi.toml`/`pixi.lock` for the `mcp-proxy` bridge).

Canonical files:

- `charly.yml` — the `chrome-devtools-mcp:` candy entity (description, `require`,
  port, `mcp_provide`, service, plan) and its `skill:` entity.
- `package.json` — the npm `chrome-devtools-mcp` dependency.
- `pixi.toml` / `pixi.lock` — the pixi environment providing `mcp-proxy`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-selkies:chrome-devtools-mcp` — the owning skill: the MCP tool catalog,
  the stdio-to-HTTP bridge architecture, the hermes reconfiguration note, and the
  port-publishing gotcha. Load before editing, building, deploying, or
  troubleshooting this candy.
- `/charly-build:charly-mcp-cmd` — the `mcp:` check verb and the URL rewriter.
- `/charly-selkies:chrome` — the parent Chrome layer that provides CDP on `9222`.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is a composing box's `check` bed; the candy's own `check:`
  steps assert the npm/pixi binaries, the running service, and the live `mcp:`
  ping + `list-tools` catalog (`navigate_page`, `take_screenshot`).

## Modify this repo

- Edit the `chrome-devtools-mcp:` candy entity in `charly.yml`; the `skill:`
  entity in the same file is the owning skill's source — a candy change and its
  skill change land together.
- The `mcp_provide` name `chrome-devtools` is the service contract consumed by
  hermes and other MCP clients; a rename is an explicit hard cutover.
- Dependency bumps belong in `package.json` / `pixi.toml`; keep the service exec
  paths (`~/.npm-global/bin/chrome-devtools-mcp`,
  `~/.pixi/envs/default/bin/mcp-proxy`) in step with the plan's `check:` steps.
- The `skill:` entity is the source for
  `/charly-selkies:chrome-devtools-mcp`; never edit the generated `SKILL.md`.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
