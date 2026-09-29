# pod-chrome-devtools-mcp

The `chrome-devtools-mcp` candy of the OpenCharly candy library, as a standalone
repo (the candy de-submodule cutover, kind-prefixed naming). It exposes
browser-automation tools over Streamable HTTP on port `9224`.

## What it provides

Installs the `chrome-devtools-mcp` Node.js server (npm) and the Python
`mcp-proxy` bridge (pixi). A supervisord service runs `mcp-proxy`, which bridges
the stdio MCP server to Streamable HTTP on `9224` and points it at Chrome's
DevTools Protocol on `127.0.0.1:9222`.

| Property | Value |
|---|---|
| Port | `9224` (Streamable HTTP MCP endpoint) |
| Service | `chrome-devtools-mcp` (`restart: always`) |
| Requires | `layer-nodejs`, `layer-supervisord`, `plugin-mcp` |
| Packages | pixi (PyPI) `mcp-proxy`; npm `chrome-devtools-mcp` |
| `mcp_provide` | `chrome-devtools` at `http://{{.ContainerName}}:9224/mcp` |

The advertised tool catalog covers input, navigation, inspection, network,
performance, and emulation (`navigate_page`, `take_screenshot`, `click`,
`fill_form`, `evaluate_script`, `performance_start_trace`, `emulate`, …). It is
auto-included by the `chrome` base layer, so any box including `chrome`,
`chrome-sway`, or a desktop metalayer gets it.

## How to use it

Compose the candy (or a box that includes `chrome`, which pulls it in):

```yaml
my-browser-box:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-chrome-devtools-mcp:<tag>'
```

Consumers — hermes (`mcp_accept: chrome-devtools`), Claude Code, or the
declarative `mcp:` check verb — connect to `http://<host>:9224/mcp`.

## Layout

- `charly.yml` — the `chrome-devtools-mcp` candy entity plus its `skill:` entity.
- `package.json` — the npm `chrome-devtools-mcp` dependency.
- `pixi.toml` / `pixi.lock` — the pixi environment providing `mcp-proxy`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-selkies:chrome-devtools-mcp` — the MCP tool catalog, the
  bridge architecture, and the hermes reconfiguration note.
- `/charly-build:charly-mcp-cmd` — the `mcp:` check verb used by the plan.
- `/charly-selkies:chrome` — the parent Chrome layer.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
