---
name: world-builder
description: Create, arrange, and manipulate 3D objects in Roblox Studio Workspace via the MCP server. Use when the user wants to spawn parts, build structures, set properties, clone/delete objects, or mass-create repeated geometry.
---

# world-builder

Create and manipulate 3D objects in a live Roblox place via MCP tools — no manual Studio interaction needed.

## When to use

- "Spawn a part at X position"
- "Create a grid of platforms"
- "Make a 10x10 floor out of parts"
- "Clone this object 5 times"
- "Set the color/size/material of all parts in X"
- "Delete everything in the Workspace called Y"
- "Build a basic room/arena/obstacle"

## Core MCP tools

| Tool | Use |
|---|---|
| `create_object` | Spawn a single instance (Part, Model, Folder, etc.) |
| `set_property` | Set one property on an instance |
| `set_properties` | Set multiple properties on one instance at once (prefer over repeated set_property) |
| `mass_create_objects` | Create many objects in one call — use for grids, arrays, repeated geometry |
| `mass_set_property` | Set a property on many instances at once |
| `clone_object` | Duplicate an existing instance |
| `smart_duplicate` | Duplicate with offset — good for evenly spaced repeated objects |
| `mass_duplicate` | Duplicate multiple objects at once |
| `move_object` | Reposition an instance (sets CFrame or Position) |
| `delete_object` | Remove an instance |
| `rename_object` | Rename an instance |
| `get_instance_properties` | Inspect current properties before editing |
| `get_instance_children` | See what's inside a container before modifying |
| `search_objects` | Find instances by name/class before acting on them |
| `undo` / `redo` | Roll back or reapply changes |

## Rules

- Always prefer `set_properties` over multiple `set_property` calls — it's one round trip.
- Use `mass_create_objects` for anything involving repetition (grids, rows, walls). Never loop `create_object` in your head and make 20 individual calls.
- Always specify `Parent` when creating — default parent is not guaranteed. Use `game.Workspace` for visible geometry.
- For positions, Roblox uses Y-up coordinates. Ground level is Y=0 (or Y=surface/2 to sit on the baseplate). The Baseplate is at Y=0 and is 512x512 studs by default.
- Parts are anchored by default via MCP — set `Anchored = true` explicitly if physics shouldn't move them.
- After bulk operations, call `get_instance_children` or `search_objects` to confirm the result looks right before continuing.
- Always call `undo` if something goes wrong rather than manually deleting — it's faster and cleaner.

## Common property reference

| Property | Type | Notes |
|---|---|---|
| `Size` | Vector3 | `{X, Y, Z}` in studs |
| `Position` | Vector3 | World position |
| `CFrame` | CFrame | Position + rotation together |
| `Anchored` | bool | Prevents physics from moving the part |
| `BrickColor` | BrickColor | Legacy color — prefer `Color` |
| `Color` | Color3 | RGB 0–1 range |
| `Material` | Enum.Material | SmoothPlastic, Neon, Glass, etc. |
| `Transparency` | number | 0 = opaque, 1 = invisible |
| `CanCollide` | bool | Whether other parts collide with it |
| `Shape` | Enum.PartType | Block, Ball, Cylinder, Wedge |
| `Name` | string | Instance name in Explorer |

## Workflow example — building a floor grid

1. Decide part size and grid dimensions
2. Use `mass_create_objects` with calculated positions for each tile
3. Call `get_instance_children` on Workspace to verify count
4. If something's off, `undo` and adjust

## Gotchas

- Roblox parts placed via MCP appear immediately in Studio — no need to save or sync.
- Don't create more than ~100 parts in a single `mass_create_objects` call without checking with the user first.
- `Model` objects need a `PrimaryPart` set if you plan to use `Model:SetPrimaryPartCFrame` later.
- Neon material ignores lighting and always glows — useful for indicators/effects but not structural parts.
