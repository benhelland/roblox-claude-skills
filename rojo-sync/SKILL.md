---
name: rojo-sync
description: Manage the Rojo dev server for a Roblox project — start it, stop it, build a place file, or check status. Use when the user asks to sync, serve, build, or launch Rojo.
---

# rojo-sync

Control the Rojo sync server and build pipeline for a Roblox project.

## When to use

- "Start Rojo" / "serve the project" / "sync to Studio"
- "Build a .rbxl" / "make a place file"
- "Is Rojo running?" / "stop Rojo"
- "Regenerate the sourcemap"

## Prerequisites

- `rojo` on PATH (via Aftman/Rokit) — verify with `rojo --version`
- A `default.project.json` at the project root (or pass an explicit `--project` path)
- Roblox Studio with the Rojo plugin installed and running for live-sync

## Core commands

| Action | Command |
| --- | --- |
| Start dev server | `rojo serve default.project.json` |
| Serve on custom port | `rojo serve --port 34872 default.project.json` |
| Build place file | `rojo build default.project.json --output build/place.rbxl` |
| Build place (XML) | `rojo build default.project.json --output build/place.rbxlx` |
| Generate sourcemap | `rojo sourcemap default.project.json --output sourcemap.json --include-non-scripts` |
| Open existing place in Studio | `rojo upload default.project.json --asset_id <id> --cookie <cookie>` (rare) |

## Running `rojo serve` correctly

Rojo serve is a long-running process. Always start it with `run_in_background: true` in the Bash tool so the conversation isn't blocked. After starting, check output once with a brief poll to confirm it bound to the port. Example flow:

1. Run `rojo serve default.project.json` in background.
2. Read output — expect lines like `Rojo server listening on port 34872`.
3. Tell the user Studio needs the plugin's "Connect" button pressed.
4. To stop: kill the background shell, do not send Ctrl-C through stdin.

## Sourcemap hygiene

If luau-lsp is giving wrong types or missing instances, regenerate the sourcemap:

```
rojo sourcemap default.project.json --output sourcemap.json --include-non-scripts
```

The VSCode extension can do this automatically if `luau-lsp.sourcemap.autogenerate` is true.

## Gotchas

- Windows: if `rojo` isn't found, the user likely installed via Aftman but hasn't restarted their shell — tell them to reopen VSCode's terminal.
- Port 34872 is the Rojo default — if it's busy, another Rojo instance is probably already running.
- Do not commit `sourcemap.json` or `build/*.rbxl` — they belong in `.gitignore`.
- If `default.project.json` is missing, ask whether to create one rather than guessing the tree.
