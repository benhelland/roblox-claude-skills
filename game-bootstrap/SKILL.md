---
name: game-bootstrap
description: Scaffold a complete Roblox game from a plain-English concept — scripts, folder structure, RemoteEvents, core systems, and a starter scene. Use when the user describes a game idea and wants to go from zero to a working project skeleton.
---

# game-bootstrap

Turn a game concept into a working project skeleton — scripts, services, remotes, and a starter world.

## Approach

1. **Understand the concept** — core loop (what does the player DO?), key systems, multiplayer model (PvP / co-op / solo).
2. **Ask only if ambiguous** — proceed with reasonable defaults. Only ask for critical unknowns (e.g. "PvP or co-op?").
3. **Scaffold in order**: read `default.project.json` → shared types/config → Remotes → server services → client controllers → GUI → starter scene.
4. **Minimum viable skeleton** — every script wired up, business logic stubbed with `-- TODO:`. Goal: runs without errors.

## Project structure

```
src/
  server/
    GameManager.server.luau      — round loop, game state, win conditions
    PlayerService.server.luau    — PlayerAdded/Removing, leaderstats, per-player data
    <Feature>Service.server.luau — one file per major server system
  player/
    Main.client.luau             — client entry, requires controllers
    <Feature>Controller.client.luau
  character/
    <Feature>.client.luau        — character-scoped scripts
  gui/
    <Screen>Controller.client.luau
  replicatedStorage/
    Remotes.luau                 — all RemoteEvents/Functions declared here
    GameConfig.luau              — all tunable constants
    Types.luau                   — shared type definitions
  workspace/
    Baseplate.rbxm               — static geometry saved from Studio
    <Scene>.rbxm
```

## Systems by game type

| Game type | Server scripts | Client scripts | Key remotes | Leaderstats |
|---|---|---|---|---|
| **Combat / PvP** | `WeaponService`, `RespawnService` | `WeaponController`, `HUDController` | `DealDamage`, `PlayerDied`, `Respawn` | Kills, Deaths |
| **Parkour / Obby** | `CheckpointService`, `TimerService` | `TimerController`, `HUDController` | `ReachedCheckpoint`, `FinishedCourse` | BestTime, Completions |
| **Collection / Tycoon** | `CollectionService`, `CurrencyService` | `CollectionController`, `HUDController` | `CollectItem`, `UpdateCurrency` | Coins, Score |
| **Survival / Waves** | `WaveService`, `EnemyService` | `HUDController`, `SpectatorController` | `WaveStarted`, `EnemySpawned`, `PlayerDamaged` | Survived, Score |
| **Racing** | `RaceService`, `LapService` | `TimerController`, `HUDController` | `LapComplete`, `RaceFinished` | BestLap, Wins |
| **Tower Defense** | `WaveService`, `TowerService` | `TowerController`, `HUDController` | `PlaceTower`, `EnemyReachedEnd` | Rounds, Gold |
| **Simulator** | `ResourceService`, `UpgradeService` | `AutoController`, `HUDController` | `CollectResource`, `PurchaseUpgrade` | Cash, Rebirths |
| **Roleplay / Social** | `JobService`, `InteractionService` | `InteractionController`, `HUDController` | `TakeJob`, `Interact` | Level, Cash |

## Always include

**PlayerService** — `PlayerAdded` / `PlayerRemoving`, leaderstats init, per-player data table. Server only.

**GameConfig** — all magic numbers. Speed, damage, timer durations, spawn counts, prices. Makes tuning trivial.

**Remotes** — single module, all events declared once. Never scatter remote names across files.

**Types** — shared type definitions for player data structs, game state enums, etc.

## Script templates

### PlayerService (server)
```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Remotes = require(ReplicatedStorage.Remotes)
local GameConfig = require(ReplicatedStorage.GameConfig)

local playerData: { [Player]: { score: number } } = {}

local function initLeaderstats(player: Player)
    local ls = Instance.new("Folder")
    ls.Name = "leaderstats"
    ls.Parent = player
    local score = Instance.new("IntValue")
    score.Name = "Score"
    score.Value = 0
    score.Parent = ls
end

Players.PlayerAdded:Connect(function(player)
    playerData[player] = { score = 0 }
    initLeaderstats(player)
end)

Players.PlayerRemoving:Connect(function(player)
    playerData[player] = nil
end)
```

### GameManager round loop (server)
```lua
local ROUND_LENGTH = GameConfig.ROUND_LENGTH  -- e.g. 120 seconds
local INTERMISSION = GameConfig.INTERMISSION  -- e.g. 15 seconds

local function runRound()
    -- TODO: teleport players, spawn enemies, start timers
    task.wait(ROUND_LENGTH)
    -- TODO: determine winner, award points
end

while true do
    task.wait(INTERMISSION)
    runRound()
end
```

### Remotes module (replicatedStorage)
```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")
local Remotes = {}

if RunService:IsServer() then
    local folder = Instance.new("Folder")
    folder.Name = "Remotes"
    folder.Parent = ReplicatedStorage

    local function ensure(name, class)
        local obj = Instance.new(class)
        obj.Name = name
        obj.Parent = folder
        return obj
    end

    -- declare all remotes here:
    -- Remotes.MyEvent = ensure("MyEvent", "RemoteEvent")
else
    local folder = ReplicatedStorage:WaitForChild("Remotes")
    -- mirror all remotes here:
    -- Remotes.MyEvent = folder:WaitForChild("MyEvent")
end

return Remotes
```

### Touch / Proximity trigger (server)
```lua
-- Touch-based trigger
local part = workspace.KillBrick
part.Touched:Connect(function(hit)
    local player = game.Players:GetPlayerFromCharacter(hit.Parent)
    if not player then return end
    -- TODO: handle player touch
end)

-- ProximityPrompt-based trigger
local prompt = Instance.new("ProximityPrompt")
prompt.ActionText = "Collect"
prompt.Parent = workspace.Collectible
prompt.Triggered:Connect(function(player)
    -- TODO: handle collect
end)
```

### DataStore save/load (server)
```lua
local DataStoreService = game:GetService("DataStoreService")
local store = DataStoreService:GetDataStore("PlayerData_v1")

local function saveData(player: Player, data: table)
    local ok, err = pcall(store.SetAsync, store, tostring(player.UserId), data)
    if not ok then warn("Save failed:", err) end
end

local function loadData(player: Player): table
    local ok, data = pcall(store.GetAsync, store, tostring(player.UserId))
    return (ok and data) or { score = 0 }  -- default if no data
end
```

### HUD Controller (client)
```lua
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Remotes = require(ReplicatedStorage.Remotes)

local player = Players.LocalPlayer
local gui = player.PlayerGui

-- TODO: get ScreenGui reference, update labels
-- Remotes.UpdateHUD.OnClientEvent:Connect(function(data)
--     gui.HUD.ScoreLabel.Text = "Score: " .. data.score
-- end)
```

## Starter scene by type

Use `scene-architect` after scripting:

| Game type | Scene |
|---|---|
| Combat | Small arena (40–60 studs), walls, 2–4 spawn pads |
| Parkour | Linear start zone + first 3–5 platforms |
| Collection | Open area (100+ studs), marked spawn regions |
| Survival | Enclosed arena, clear sightlines, single entry |
| Racing | Short looping track section (can be extended) |
| Simulator | Small area with interactable objects |

## Rules

- Read `default.project.json` before writing any files — confirm src/ paths.
- Every script `require`s dependencies explicitly. No globals beyond Roblox services.
- Server validates all `RemoteEvent` arguments. Never trust client input.
- Stub with `-- TODO: implement X`, never leave empty functions or broken `require` chains.
- After scripting, build starter scene with `scene-architect`, then instruct the user to right-click each top-level Model in Studio Explorer → **Save to File** → `src/workspace/ModelName.rbxm`.
- Run `execute_luau` via `studio-debug` to verify no script errors after bootstrap.
- Print a final summary: files created, systems wired, what to implement next.
