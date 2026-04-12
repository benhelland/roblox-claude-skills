---
name: game-bootstrap
description: Scaffold a complete Roblox game from a plain-English concept — scripts, folder structure, RemoteEvents, core systems, and a starter scene. Use when the user describes a game idea and wants to go from zero to a working project skeleton.
---

# game-bootstrap

Turn a game concept into a working project skeleton in one shot — scripts, services, remotes, and a starter world.

## When to use

- "I want to make a sword fighting game"
- "Bootstrap a tycoon game"
- "Set up a parkour game with coins and a timer"
- "Create a zombie survival game structure"
- Any time the user describes a game concept from scratch

## How to approach a bootstrap

1. **Understand the concept** — identify the core loop (what does the player DO?), key systems (combat, collecting, building, racing, etc.), and multiplayer model (cooperative, PvP, solo).
2. **Ask only what you need** — if the concept is clear enough, proceed with reasonable defaults. Only ask if a critical decision is ambiguous (e.g. "is this PvP or co-op?").
3. **Scaffold in order**: folder structure → shared types/constants → services → remotes → client controllers → starter scene.
4. **Build the minimum viable skeleton** — every script should exist and be wired up, but business logic can be stubbed with `-- TODO:` comments. The goal is a project that runs without errors, not a finished game.

## Standard project structure

```
src/
  server/
    GameManager.server.luau       — game state, round loop, win conditions
    PlayerService.server.luau     — player join/leave, data init
    <Feature>Service.server.luau  — one file per major server system
  player/
    Main.client.luau              — client entry point
    <Feature>Controller.client.luau — one file per major client system
  character/
    <Feature>.client.luau         — character-level client scripts
  gui/
    <Screen>Controller.client.luau — UI scripts
  replicatedStorage/
    Remotes.luau                  — single source of truth for all RemoteEvents
    GameConfig.luau               — tunable constants (speed, damage, timers)
    Types.luau                    — shared type definitions
  workspace/
    Baseplate.rbxm                — static world geometry saved from Studio
    <Scene>.rbxm
```

## Core systems by game type

**Combat/PvP**
- `WeaponService.server.luau` — hit validation, damage application
- `WeaponController.client.luau` — input handling, swing animation
- Remotes: `DealDamage`, `PlayerDied`, `RespawnPlayer`
- Leaderstats: Kills, Deaths

**Collection/Tycoon**
- `CollectionService.server.luau` — spawn collectibles, track ownership
- `CollectionController.client.luau` — proximity detection, UI feedback
- Remotes: `CollectItem`, `UpdateCurrency`
- Leaderstats: Coins/Cash/Score

**Parkour/Obby**
- `CheckpointService.server.luau` — save/restore checkpoints
- `TimerService.server.luau` — round timer, personal best
- Remotes: `ReachedCheckpoint`, `FinishedCourse`
- Leaderstats: BestTime, Completions

**Survival**
- `WaveService.server.luau` — spawn enemies, escalate difficulty
- `HealthService.server.luau` — shared health logic
- Remotes: `EnemySpawned`, `PlayerDamaged`, `WaveComplete`
- Leaderstats: Survived, Score

## Always include

**PlayerService** — handles PlayerAdded/PlayerRemoving, initializes leaderstats, sets up per-player data table.

**GameConfig** — all magic numbers in one place. Speed, damage, timer durations, spawn counts. Makes tuning trivial.

**Remotes** — all RemoteEvents declared in one shared module. Never use magic strings scattered across files.

## Leaderstats template (always server-side in PlayerService)

```lua
local function initLeaderstats(player: Player)
    local leaderstats = Instance.new("Folder")
    leaderstats.Name = "leaderstats"
    leaderstats.Parent = player

    local stat = Instance.new("IntValue")
    stat.Name = "Score"
    stat.Value = 0
    stat.Parent = leaderstats
end
```

## After scaffolding scripts, build a starter scene

Use `scene-architect` to build a minimal playable space matching the game type:
- Combat → small arena with spawn pads
- Collection → open area with spawn regions marked
- Parkour → first section of course (3-5 platforms)
- Survival → enclosed arena with clear sightlines

## Rules

- Every script must `require` its dependencies explicitly — no globals beyond Roblox services.
- Server scripts validate everything from the client. Never trust `RemoteEvent` arguments.
- Stub unimplemented logic with `-- TODO: implement X` rather than leaving empty functions or broken code.
- Read `default.project.json` first to confirm the src/ mapping before writing any files.
- After scaffolding scripts, use `scene-architect` to build the starter scene, then instruct the user to right-click each top-level Model in Studio Explorer → **Save to File** → save as `.rbxm` into `src/workspace/` so Rojo tracks the geometry.
- After scaffolding, print a summary: files created, systems wired, what the user should implement next.
- Use `execute_luau` via `studio-debug` to verify the place loads without script errors after bootstrapping.
