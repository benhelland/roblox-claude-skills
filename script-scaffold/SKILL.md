---
name: script-scaffold
description: Scaffold new Roblox Luau scripts (ModuleScript, Script, LocalScript), services, singletons, or RemoteEvent wiring with Rojo-friendly naming and idiomatic patterns. Use when the user asks to create a new script, module, service, controller, or remote.
---

# script-scaffold

Generate new Luau files that follow Rojo file-name conventions and Roblox best practices.

## When to use

- "Create a new ModuleScript for X"
- "Scaffold a PlayerService" / "scaffold a controller"
- "Wire up a RemoteEvent for Y"
- "Add a new singleton module"

## Rojo file naming (critical)

Rojo maps filenames to instance classes by suffix. Get this wrong and the script becomes the wrong type in Studio.

| Filename suffix | Instance class | Runs on |
| --- | --- | --- |
| `Foo.luau` | `ModuleScript` | Required by other scripts |
| `Foo.server.luau` | `Script` (Server) | Server |
| `Foo.client.luau` | `LocalScript` | Client |
| `init.luau` | ModuleScript folder | — |
| `init.server.luau` | Script folder | Server |
| `init.client.luau` | LocalScript folder | Client |

An `init.*.luau` inside a folder turns the folder into that script type, and siblings become children of it. Use this when a module grows beyond one file.

## Standard module template (singleton pattern)

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local MyService = {}
MyService.__index = MyService

function MyService.new()
	local self = setmetatable({}, MyService)
	self._connections = {}
	return self
end

function MyService:Start()
	-- wire up events, start loops
end

function MyService:Stop()
	for _, conn in self._connections do
		conn:Disconnect()
	end
	table.clear(self._connections)
end

return MyService
```

## RemoteEvent wiring template

**Shared (`src/shared/Remotes.luau`)** — single source of truth for remote names:

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local Remotes = {}

local folder = ReplicatedStorage:FindFirstChild("Remotes")
	or Instance.new("Folder")
folder.Name = "Remotes"
folder.Parent = ReplicatedStorage

local function ensure(name: string, className: string): Instance
	local existing = folder:FindFirstChild(name)
	if existing then return existing end
	local new = Instance.new(className)
	new.Name = name
	new.Parent = folder
	return new
end

Remotes.DoThing = ensure("DoThing", "RemoteEvent")

return Remotes
```

**Server handler** — validate every argument from the client. Never trust client input.

**Client sender** — fire and forget, or use `RemoteFunction:InvokeServer` if you need a return value (and handle the yield).

## Rules

- Always place shared types/modules in `ReplicatedStorage` (via Rojo's `src/shared`).
- Place server-only logic in `ServerScriptService` (via `src/server`).
- Place client logic in `StarterPlayerScripts` (via `src/client`).
- Use `task.spawn`/`task.wait`, not the deprecated `spawn`/`wait`.
- Add the `.luau` extension, not `.lua` (luau-lsp and Studio both support it, and it signals Luau-specific features like string interpolation and type annotations).
- When scaffolding inside an existing project, read `default.project.json` first to see how `src/shared`, `src/server`, `src/client` actually map — don't assume.

## Gotchas

- Forgetting the `.server` or `.client` suffix on a script that needs it will sync it as a ModuleScript that never runs.
- `init.luau` must be inside a folder — a top-level `init.luau` doesn't do what you think.
- RemoteEvent names should live in one shared module, not be typed as magic strings across the codebase.
