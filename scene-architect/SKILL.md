---
name: scene-architect
description: Translate a plain-English description of a game scene into 3D geometry built live in Roblox Studio via MCP. Use when the user describes what they want built — arenas, courses, environments, rooms, maps — rather than specifying individual parts.
---

# scene-architect

Turn a natural language description into a complete 3D scene in Roblox Studio using MCP tools.

## When to use

- "Build me a circular arena with stone walls"
- "Create an obstacle course over lava"
- "Make a small medieval town square"
- "I want a rooftop parkour map"
- "Build a simple starter area for my game"
- Any time the user describes a scene rather than a specific object

## How to approach a build

1. **Clarify before building** — if the description is vague, ask 2-3 targeted questions: rough scale (studs), theme/material palette, any gameplay constraints (spawn points needed? specific height limits?). Don't ask more than necessary — make reasonable assumptions and state them.
2. **Plan the geometry** — break the scene into primitive shapes. Most scenes are combinations of: floors (large flat parts), walls (thin tall parts), platforms (raised flat parts), pillars (tall narrow parts), ramps (wedge parts). Sketch it mentally before issuing MCP calls.
3. **Build bottom-up** — floor/terrain first, then walls/boundaries, then features, then decoration last.
4. **Use mass_create_objects for repetition** — rows of platforms, sets of pillars, grid floors. Never loop individual create_object calls for repeated geometry.
5. **Verify as you go** — after major sections, call `get_instance_children` on Workspace or `capture_screenshot` to confirm the build looks right before continuing.
6. **Name things clearly** — use descriptive names (ArenaFloor, NorthWall, SpawnPad_1) so the user can find objects in Explorer.

## Coordinate system

- Y is up. Ground level = Y=0 (Baseplate surface).
- A part sitting on the Baseplate: Position.Y = Size.Y / 2
- The Baseplate is 512×512 studs centered at 0,0,0.
- Build outward from the center (0,0,0) unless told otherwise.
- 1 stud ≈ 1 meter for scale intuition. A player is ~5 studs tall.

## Common scene recipes

**Floor grid** — `mass_create_objects` with Size {4,1,4} parts tiled across X/Z.

**Arena walls** — 4 thin high parts arranged as a perimeter. Use Size {width, height, 1} for flat walls. Rotate with CFrame for angled walls.

**Platforms over void** — series of parts at increasing Y values, staggered X/Z positions. Gap between platforms should be jumpable (max ~12 studs horizontal for a normal jump).

**Spawn pad** — flat neon-colored part (~6×0.5×6), slightly above floor, with a SpawnLocation inside it or nearby.

**Ramp** — use `ClassName: "WedgePart"` with appropriate Size and CFrame rotation.

**Lava/water floor** — large flat part with Material "Neon" (lava: deep red/orange) or a semi-transparent blue part for water.

## Material quick reference

| Look | Material | Color hint |
|---|---|---|
| Stone/dungeon | SmoothPlastic or Slate | Gray |
| Metal | Metal or DiamondPlate | Silver |
| Wood | Wood or WoodPlanks | Brown |
| Grass | Grass or LeafyGrass | Green |
| Lava | Neon | R=1 G=0.2 B=0 |
| Water (fake) | Glass or ForceField | Blue, low transparency |
| Neon indicator | Neon | Any bright color |
| Dirt/ground | Ground or Mud | Brown/tan |

## Rules

- Always set `Anchored = true` on all static geometry. Unanchored parts fall through the floor.
- Keep total part count reasonable — under 200 parts for a first pass. Dense decoration can always be added later.
- Group related parts under a `Model` or `Folder` in Workspace for organization. Create the container first, then parent parts to it.
- Don't place parts at Y=0 — they'll clip into the Baseplate. Minimum Y = Size.Y / 2.
- After the build, summarize what was created (part count, rough dimensions, notable features) so the user knows what they're working with.
- If the user asks for something that would require hundreds of parts (a full city, detailed terrain), suggest using Roblox's Terrain tools instead and build a simpler stand-in.

## Saving to src/workspace

After the user is happy with the scene, save it so Rojo tracks it:

1. All scene geometry should already be grouped under a named `Model` in Workspace — create one if not.
2. In Studio's Explorer, right-click the Model → **Save to File** → save as `.rbxm` into `src/workspace/`.
3. Rojo syncs it automatically on the next serve.

Name the file to match the Model (e.g. `Arena.rbxm`, `ObstacleCourse.rbxm`). Individual persistent objects (Baseplate, SpawnLocation) should be saved as their own `.rbxm` files.

## Asking for changes

After building, always invite feedback: "Want me to adjust the scale, materials, or layout?" Changes are faster with `undo` + rebuild than trying to surgically edit dozens of parts.
