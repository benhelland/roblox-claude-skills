---
name: scene-architect
description: Translate a plain-English description of a game scene into 3D geometry built live in Roblox Studio via MCP. Use when the user describes what they want built — arenas, courses, environments, rooms, maps — rather than specifying individual parts.
---

# scene-architect

Turn a natural language description into a complete 3D scene in Roblox Studio using MCP tools.

## Build approach

1. **Clarify if needed** — ask at most 2–3 targeted questions: scale (studs), theme, gameplay constraints (spawn points, height limits). Make reasonable assumptions and state them rather than over-asking.
2. **Plan geometry** — break scene into primitives: floors (flat parts), walls (thin tall parts), platforms (raised parts), pillars (tall narrow parts), ramps (WedgePart), ceilings (flat parts above).
3. **Build bottom-up** — ground/floor → walls/boundaries → features → decoration.
4. **Repeat with mass_create_objects** — rows, grids, pillar sets. Never loop individual calls.
5. **Verify with capture_screenshot** after major sections before continuing.
6. **Name descriptively** — `ArenaFloor`, `NorthWall`, `SpawnPad_1`. Users navigate by Explorer.

## Coordinate system & scale

| Reference | Value |
|---|---|
| Y-up, ground level | Y = 0 (Baseplate surface) |
| Part on baseplate | `Position.Y = Size.Y / 2` |
| Baseplate | 512×512 studs, centered at 0,0,0 |
| Player height | ~5 studs |
| Door height | 8–10 studs |
| Ceiling height | 12–20 studs (room), 30–50 studs (arena) |
| Max horizontal jump | ~12 studs |
| Max vertical jump | ~8 studs |
| 1 stud | ≈ 1 meter |

## Materials by theme

**Stone / Dungeon / Castle**
| Material | Color | Use |
|---|---|---|
| `Slate` | Dark gray | Walls, floors |
| `Cobblestone` | Mid gray-brown | Roads, plazas |
| `Brick` | Reddish-brown | Walls, towers |
| `Granite` | Speckled gray | Pillars, battlements |
| `Sandstone` | Warm tan | Desert ruins |
| `Limestone` | Off-white | Ancient temples |
| `Marble` | White/gray | Polished floors, statues |
| `Rock` | Dark gray-brown | Cliffs, cave walls |
| `Basalt` | Very dark gray | Volcanic ruins |

**Urban / Industrial / Sci-Fi**
| Material | Color | Use |
|---|---|---|
| `Concrete` | Gray | Walls, floors, brutalist |
| `SmoothPlastic` | Any | Clean futuristic surfaces |
| `Metal` | Silver | Panels, frames |
| `DiamondPlate` | Silver | Industrial floors, grating |
| `CorrodedMetal` | Orange-brown | Rusted/abandoned areas |
| `Foil` | Bright silver | High-tech accents |
| `Asphalt` | Dark gray | Roads, rooftops |
| `Pavement` | Mid gray | Sidewalks, plazas |
| `Glass` | Blue/white, low opacity | Windows, barriers |
| `ForceField` | Cyan | Sci-fi shields, barriers |
| `Neon` | Any bright | Lights, indicators, lava |

**Nature / Outdoor**
| Material | Color | Use |
|---|---|---|
| `Grass` | Green | Ground cover |
| `LeafyGrass` | Bright green | Lush areas |
| `Ground` | Brown | Dirt paths, bare earth |
| `Mud` | Dark brown | Swamp, rain-soaked areas |
| `Sand` | Tan | Beaches, deserts |
| `RedCrackedDirt` | Red-brown | Arid/canyon zones |
| `Salt` | White | Salt flats |
| `Snow` | White | Winter areas |
| `Glacier` | Pale blue | Ice caves, frozen zones |
| `Ice` | Blue-white | Frozen lakes, slippery floors |
| `Wood` | Brown | Logs, rustic structures |
| `WoodPlanks` | Light brown | Wooden floors, docks, bridges |

**Effects / Special**
| Material | Use |
|---|---|
| `Neon` | Lava (red-orange), glowing signs, kill floors — emits light |
| `Glass` | Windows, water surfaces (low transparency) |
| `ForceField` | Sci-fi barriers, force walls |
| `CrackedLava` | Cooling lava, volcanic ground |
| `Fabric` | Tents, banners, soft surfaces |

## Color palettes by theme

| Theme | Colors (RGB approx) |
|---|---|
| Medieval stone | `0.4,0.35,0.3` walls / `0.3,0.25,0.2` floors |
| Sci-fi clean | `0.85,0.9,1.0` surfaces / `0.0,0.6,1.0` accents |
| Lava/volcanic | `0.1,0.1,0.1` rock / `1.0,0.2,0.0` neon lava |
| Ice/frozen | `0.7,0.85,1.0` ice / `0.9,0.95,1.0` snow |
| Forest/nature | `0.25,0.5,0.2` ground / `0.2,0.35,0.15` shadow |
| Desert | `0.85,0.7,0.45` sand / `0.65,0.45,0.25` rock |
| Neon/cyberpunk | `0.05,0.05,0.1` dark / `0.0,1.0,0.8` + `1.0,0.0,0.6` neon |

## Scene recipes

**Floor grid** — `mass_create_objects`, Size `{4,1,4}`, tiled across X/Z.

**Arena walls** — 4 parts as perimeter. `Size {width, height, 2}`. Use CFrame for rotation. Add battlements by placing smaller parts along the top edge.

**Pillars** — `mass_create_objects` column of `{2,12,2}` Cylinder parts at regular intervals.

**Curved wall (approximate)** — series of short wall segments each rotated slightly (e.g. 16 segments × 22.5° = full circle). Radius determines X/Z offset per segment.

**Tower** — stacked rings of wall parts, each ring slightly smaller, topped with a conical roof using WedgeParts arranged in a circle.

**Arch** — two vertical pillars + one horizontal lintel across the top. Add WedgeParts on each side of the lintel for a pointed arch.

**Bridge** — long flat floor part, two side rail parts, optional support pillars below.

**Ramp** — `WedgePart`, Size `{width, rise, run}`, rotated to face the correct direction.

**Staircase** — `mass_create_objects` of steps: each step offset +1 in Y and +depth in X, size `{width, 1, depth}`.

**Tunnel** — hollow rectangular tube: floor + ceiling + 2 side walls + optional end caps with doorway gap.

**Platform course** — staggered parts at increasing Y, X/Z offset per jump. Max gap 12 studs horizontal, 8 studs vertical.

**Spawn area** — flat Neon part `{8,0.5,8}` slightly above floor + `SpawnLocation` child.

**Kill floor** — large flat part with `Neon` material, red/orange color. Add a `Script` child to kill players on touch.

**Lava** — large flat `Neon` part, `Color {1, 0.2, 0}`, optionally add `Fire` effect children.

**Water surface** — large flat `Glass` or `ForceField` part, blue tint, `Transparency 0.4`, `CanCollide false`.

**Crate/box** — `Part` with `WoodPlanks` or `Concrete`, equal Size, slight variation in scale for realism.

**Decorative pillar** — cylinder base + cylinder shaft + wider cylinder cap (3 parts grouped in a Model).

## Lighting & atmosphere

Add to `Lighting` service (child of DataModel) to set scene mood:

| Property | Effect |
|---|---|
| `Ambient` | Color3 — shadow color. Dark blue for night, warm for sunset |
| `OutdoorAmbient` | Color3 — skylight color |
| `Brightness` | number — overall light intensity |
| `ClockTime` | 0–24 — time of day (14 = afternoon, 0 = midnight) |
| `FogColor` | Color3 — fog tint |
| `FogStart` / `FogEnd` | studs — fog distance range |

Add `Atmosphere` object inside `Lighting` for volumetric effects:
- `Density` — haze thickness
- `Offset` — horizon glow
- `Color` — atmosphere tint
- `Glare`, `Haze` — sun glare and sky haze

## Rules

- `Anchored = true` on all static geometry — unanchored parts fall.
- Group parts under a named `Model` or `Folder`. Create container first, parent parts to it.
- Never place parts at exactly Y=0 — they clip into the Baseplate. Min Y = `Size.Y / 2`.
- Under 300 parts for a first pass. Decoration can be added later.
- For full cities or dense terrain, suggest Roblox Terrain tools + build a simple stand-in.
- After building, summarize: part count, dimensions, notable features.
- Invite feedback: "Want me to adjust scale, materials, or layout?"

## Saving to src/workspace

1. Group all geometry under a named `Model` in Workspace.
2. Studio Explorer → right-click Model → **Save to File** → `src/workspace/ModelName.rbxm`
3. Save individual persistent objects (Baseplate, SpawnLocation) as separate `.rbxm` files.
4. Rojo syncs on next serve.
