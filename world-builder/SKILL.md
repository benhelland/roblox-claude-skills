---
name: world-builder
description: Create, arrange, and manipulate 3D objects in Roblox Studio Workspace via the MCP server. Use when the user wants to spawn parts, build structures, set properties, clone/delete objects, or mass-create repeated geometry.
---

# world-builder

Create and manipulate 3D objects in a live Roblox place via MCP — no manual Studio interaction needed.

## MCP tools

| Tool | Use |
|---|---|
| `create_object` | Spawn any instance (Part, Model, Light, Effect, etc.) |
| `set_properties` | Set multiple properties at once — always prefer over repeated `set_property` |
| `mass_create_objects` | Create many objects in one call — grids, arrays, repeated geometry |
| `mass_set_property` | Set one property across many instances |
| `clone_object` | Duplicate an existing instance |
| `smart_duplicate` | Duplicate with positional offset — evenly spaced repetition |
| `mass_duplicate` | Duplicate multiple objects at once |
| `move_object` | Reposition (sets CFrame or Position) |
| `delete_object` | Remove an instance |
| `rename_object` | Rename an instance |
| `get_instance_properties` | Inspect before editing |
| `get_instance_children` | See contents of a container |
| `search_objects` | Find instances by name/class |
| `capture_screenshot` | Verify the build visually |
| `undo` / `redo` | Roll back or reapply |

## Part types

| Class | Shape | Use |
|---|---|---|
| `Part` | Block (default) | General geometry — floors, walls, platforms |
| `Part` | `Shape: Ball` | Spheres, decorations, projectile stand-ins |
| `Part` | `Shape: Cylinder` | Pillars, pipes, barrels |
| `Part` | `Shape: Wedge` | Ramps, diagonal surfaces |
| `Part` | `Shape: CornerWedge` | Corner ramps, angled joins |
| `WedgePart` | Wedge | Explicit wedge class |
| `CornerWedgePart` | Corner wedge | Explicit corner wedge class |
| `TrussPart` | Truss | Climbable scaffold structure |
| `SpawnLocation` | — | Player spawn point |
| `Model` | — | Container for grouping parts |
| `Folder` | — | Logical grouping, no 3D presence |

## Part properties

| Property | Type | Notes |
|---|---|---|
| `Size` | Vector3 | `{X, Y, Z}` in studs |
| `Position` | Vector3 | World position |
| `CFrame` | CFrame | Position + rotation — use for rotated parts |
| `Anchored` | bool | `true` = physics won't move it. Always set for static geometry |
| `CanCollide` | bool | `false` for triggers, kill zones, invisible boundaries |
| `CanTouch` | bool | `false` to disable Touched events entirely |
| `CastShadow` | bool | `false` on small/decorative parts saves performance |
| `Transparency` | number | 0 = solid, 1 = invisible |
| `Reflectance` | number | 0–1, metallic sheen |
| `Color` | Color3 | RGB 0–1. Prefer over BrickColor |
| `Material` | Enum.Material | See material table below |
| `TopSurface` / `BottomSurface` | Enum.SurfaceType | Smooth (default), Studs, Inlet, Weld |
| `Massless` | bool | `true` = no contribution to assembly mass |
| `CollisionGroup` | string | Physics collision filtering group name |

## Materials

**Plastic / Smooth**
| Material | Look |
|---|---|
| `SmoothPlastic` | Clean matte — best default for most builds |
| `Plastic` | Slight sheen, more toy-like |
| `Neon` | Emits light, ignores shadows — indicators, lava, effects |
| `ForceField` | Holographic hexagon pattern |
| `Glass` | Transparent with refraction |

**Metal**
| Material | Look |
|---|---|
| `Metal` | Brushed metal |
| `DiamondPlate` | Checkered grip plate |
| `Foil` | Shiny crinkled foil |
| `CorrodedMetal` | Rusty, aged metal |

**Stone / Earth**
| Material | Look |
|---|---|
| `Slate` | Dark flat stone |
| `Concrete` | Gray urban surface |
| `Granite` | Speckled stone |
| `Marble` | Polished veined stone |
| `Brick` | Classic red brick |
| `Cobblestone` | Irregular stone road |
| `Rock` | Rough natural rock |
| `Sandstone` | Warm tan layered stone |
| `Basalt` | Dark volcanic rock |
| `Limestone` | Light sedimentary stone |
| `Pebble` | Rounded pebble texture |
| `Pavement` | Smooth urban pavement |
| `Asphalt` | Dark road surface |
| `CrackedLava` | Hardened lava with glowing cracks |

**Nature**
| Material | Look |
|---|---|
| `Grass` | Green grass |
| `LeafyGrass` | Denser, leafy grass variant |
| `Ground` | Bare dirt/earth |
| `Mud` | Wet dark earth |
| `Sand` | Tan sandy surface |
| `RedCrackedDirt` | Dry cracked desert earth |
| `Salt` | White crystalline flat |
| `Snow` | White fluffy snow |
| `Glacier` | Smooth blue-white ice |
| `Ice` | Clear blue ice |

**Fabric / Organic**
| Material | Look |
|---|---|
| `Fabric` | Cloth/canvas texture |
| `Wood` | Plank-cut wood |
| `WoodPlanks` | Horizontal wooden planks |

## Lighting objects

Add lights as children of a Part to illuminate the scene.

| Class | Use | Key properties |
|---|---|---|
| `PointLight` | Omnidirectional light from a part | `Brightness`, `Range`, `Color` |
| `SpotLight` | Cone of light in part's facing direction | `Brightness`, `Range`, `Angle`, `Color` |
| `SurfaceLight` | Light across a surface face | `Brightness`, `Range`, `Face`, `Color` |

Create via `create_object` with `Parent` set to the part. E.g. a torch = orange `PointLight` inside a cylindrical part.

## Effects & decorations

| Class | Parent | Use |
|---|---|---|
| `ParticleEmitter` | Part | Continuous particle effect (fire, smoke, sparks) |
| `Smoke` | Part | Rising smoke column |
| `Fire` | Part | Flame effect |
| `Sparkles` | Part | Floating sparkle effect |
| `BillboardGui` | Part | Floating 2D UI above a part (labels, markers) |
| `SelectionBox` | Workspace | Highlights a part with a colored outline |
| `Decal` | Part | Image on a part face |
| `Texture` | Part | Tiling image on a part face |
| `SpecialMesh` | Part | Custom mesh shape (Sphere, Cylinder, FileMesh, etc.) |

## Coordinate system

- Y is up. Baseplate surface = Y=0, size 512×512 centered at origin.
- Part sitting on baseplate: `Position.Y = Size.Y / 2`
- Player height ≈ 5 studs. Door height = 8–10 studs. Ceiling = 12–16 studs.
- CFrame rotation in degrees: `{0, 90, 0}` = 90° around Y axis.

## Rules

- Always `set_properties` over multiple `set_property` — one round trip.
- `mass_create_objects` for any repetition. Never mentally loop `create_object`.
- Always set `Parent` on creation. Use `game.Workspace` for visible geometry.
- `Anchored = true` on all static parts unless physics is intentional.
- Group all created geometry under a named `Model` before finishing.
- Verify with `capture_screenshot` or `get_instance_children` after bulk operations.
- `undo` on mistakes — faster than manual cleanup.
- Cap single `mass_create_objects` calls at ~100 parts; check with user for larger builds.

## Saving to src/workspace

MCP changes live only in the Studio session — Rojo won't track them unless saved:

1. Group geometry under a named `Model` in Workspace.
2. Studio Explorer → right-click Model → **Save to File** → `src/workspace/ModelName.rbxm`
3. Rojo picks it up on next sync.
