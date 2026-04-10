---
name: studio-debug
description: Debug a live Roblox place via the MCP server — run Luau code, read output logs, analyze scripts, manage playtests. Use when the user wants to test logic, inspect runtime state, check for errors, or run code without editing a script file.
---

# studio-debug

Execute code and inspect runtime state in a live Roblox place without touching script files.

## When to use

- "Run this Luau snippet in Studio"
- "What's in the output log?"
- "Are there any script errors?"
- "Start a playtest" / "stop the playtest"
- "Check if X is working at runtime"
- "What's the value of X right now?"
- "Analyze this script for warnings"

## Core MCP tools

| Tool | Use |
|---|---|
| `execute_luau` | Run arbitrary Luau on the server in the live place |
| `get_output_log` | Read Studio's output window (print, warn, error) |
| `get_script_analysis` | Static analysis warnings for a script (like luau-lsp) |
| `get_playtest_output` | Read output captured during a playtest session |
| `start_playtest` | Start a playtest (equivalent to pressing Play in Studio) |
| `stop_playtest` | Stop the running playtest |
| `get_script_source` | Read the current source of a script instance |
| `grep_scripts` | Search across all scripts for a pattern |
| `get_instance_properties` | Inspect runtime property values on any instance |
| `get_descendants` | Walk the full tree under a container at runtime |

## Rules

- `execute_luau` runs in the **server** context. It cannot access client-side state (`LocalScript` variables, `PlayerGui`, etc.). For client state, print it via a `RemoteEvent` to server first, or inspect properties via `get_instance_properties`.
- Always capture return values when using `execute_luau` — `return` the value you want to inspect rather than relying solely on `print`.
- `get_output_log` reads the full log including previous messages — look at timestamps to find recent output.
- Do not use `execute_luau` to make permanent structural changes to the game (creating scripts, permanently modifying instances) — use `world-builder` or `script-scaffold` for that. Use it for inspection and temporary testing only.
- Before starting a playtest with `start_playtest`, warn the user that it will reset the Workspace state (respawns characters, resets non-persistent state).

## Useful execute_luau patterns

**Inspect a value at runtime:**
```lua
return game.Workspace.SomePart.Position
```

**Count instances of a class:**
```lua
local parts = game.Workspace:GetDescendants()
local count = 0
for _, v in parts do
    if v:IsA("BasePart") then count += 1 end
end
return count
```

**Check if a service is accessible:**
```lua
local ds = game:GetService("DataStoreService")
return ds ~= nil
```

**Print all children of a container:**
```lua
for _, v in game.ServerScriptService:GetChildren() do
    print(v.Name, v.ClassName)
end
```

**Test a RemoteEvent exists:**
```lua
return game.ReplicatedStorage:FindFirstChild("Remotes") ~= nil
```

## Gotchas

- `execute_luau` does not persist between calls — variables declared in one call are gone in the next. Use `return` to surface values.
- Script errors in `execute_luau` surface as MCP errors — read them carefully, they include the Luau stack trace.
- `get_script_analysis` is static — it won't catch runtime errors, only type/lint issues. Use `get_output_log` for runtime errors.
- HTTP requests must be enabled in the place for MCP tools to work at all. If tools are timing out, check Game Settings → Security → Allow HTTP Requests.
