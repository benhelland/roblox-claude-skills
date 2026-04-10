---
name: roblox-api
description: Look up Roblox Luau API documentation — services, classes, methods, events, properties, enums. Use when the user asks about Roblox APIs, how to use a service, what events a class fires, or what a property does.
---

# roblox-api

Answer questions about the Roblox engine API accurately, without guessing signatures.

## When to use

- "How do I use UserInputService?"
- "What events does Humanoid fire?"
- "What's the signature of TweenService:Create?"
- "Is `Instance:IsA` the same as `Instance:IsDescendantOf`?"
- Writing new Luau code that touches engine APIs

## Canonical sources (in priority order)

1. **create.roblox.com/docs/reference/engine** — the official API reference. Prefer this for ground truth.
2. **luau-lsp hover info** — if the user has luau-lsp running with an up-to-date sourcemap, hover types are accurate for method signatures.
3. **robloxstudio-mcp tools** — if the Roblox Studio MCP server is connected, use its inspection tools (`get_instance_properties`, `run_code`, etc.) to verify behavior in the running place.

Use WebFetch against `https://create.roblox.com/docs/reference/engine/classes/<ClassName>` for class docs — the URL pattern is stable.

## Rules

- **Never invent method names or signatures.** If unsure, fetch the docs page or ask the user to confirm. Hallucinating API calls wastes their time and produces code that fails at runtime.
- **Check deprecation status.** Many old APIs (`BodyVelocity`, `BodyGyro`, `Workspace.FilteringEnabled` as a toggle) are deprecated in favor of newer ones (`LinearVelocity`, `AlignOrientation`, always-on filtering). Flag deprecations when suggesting code.
- **Mind the context.** Some APIs are server-only (`DataStoreService`), client-only (`UserInputService`, `Camera` manipulation), or plugin-only (`ChangeHistoryService`). Don't suggest a server API in a LocalScript.
- **Yields matter.** Note which methods yield (`:WaitForChild`, `:InvokeServer`, `DataStore:GetAsync`) so the user wraps them in `task.spawn` or coroutines when appropriate.

## Common quick-reference

| Need | Service / API |
| --- | --- |
| Player input (keyboard/mouse/touch) | `UserInputService` (client) |
| Server↔client messaging | `RemoteEvent` / `RemoteFunction` in `ReplicatedStorage` |
| Persistent data | `DataStoreService:GetDataStore(...)` (server) |
| Tweens | `TweenService:Create(instance, TweenInfo.new(...), {props})` |
| Raycasting | `workspace:Raycast(origin, direction, params)` |
| Animations | `Humanoid:LoadAnimation(animation)` → deprecated; use `Animator:LoadAnimation(...)` |
| HTTP requests | `HttpService:RequestAsync(...)` (server, place must allow HTTP) |
| Attribute storage on instances | `Instance:SetAttribute(name, value)` / `GetAttribute` / `GetAttributeChangedSignal` |

## Gotchas

- `wait()` and `spawn()` are deprecated — use `task.wait()` and `task.spawn()`.
- `Instance.new("Part", workspace)` with a parent arg is a performance footgun (sets parent before properties); prefer `local p = Instance.new("Part"); p.Size = ...; p.Parent = workspace`.
- `script.Parent` inside a ModuleScript in ReplicatedStorage replicates to clients — don't rely on server-only state there.
- `pcall` all DataStore calls — they throttle and can error.
