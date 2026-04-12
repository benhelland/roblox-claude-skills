---
name: ui-builder
description: Build and modify Roblox UI (ScreenGui, SurfaceGui, BillboardGui, frames, buttons, labels, health bars, inventory panels) in a live Studio session via MCP. Use when the user wants to create or edit any 2D interface element.
---

# ui-builder

Build Roblox UI hierarchies live in Studio using MCP. `create_ui_tree` builds an entire nested UI in one call — use it for any non-trivial UI instead of individual `create_object` calls.

## MCP tools

| Tool | Use |
|---|---|
| `create_ui_tree` | Build a full UI hierarchy from nested JSON in one call — primary tool |
| `create_object` | Add a single UI element to an existing hierarchy |
| `set_properties` | Update multiple properties on an existing element |
| `set_property` | Update a single property |
| `mass_set_property` | Set a property across many elements at once |
| `get_instance_children` | Inspect existing UI hierarchy |
| `get_instance_properties` | Read current properties of a UI element |
| `delete_object` | Remove a UI element |
| `rename_object` | Rename a UI element |
| `undo` / `redo` | Roll back or reapply changes |

## Where UI lives

| Container | Location | Use |
|---|---|---|
| `ScreenGui` | `StarterGui` | 2D HUD overlay — main game UI |
| `BillboardGui` | Child of Part or `Workspace` | Floats above a 3D object in world space |
| `SurfaceGui` | Child of Part | Renders flat on a part's face |
| `PlayerGui` | Under Player (runtime only) | Where ScreenGuis end up at runtime |

Always create `ScreenGui` inside `StarterGui` so it replicates to all players. Set `ResetOnSpawn = false` to persist across deaths.

## Core UI classes

### Containers
| Class | Use |
|---|---|
| `ScreenGui` | Root 2D GUI container |
| `Frame` | Rectangle container — layout, panels, cards |
| `ScrollingFrame` | Scrollable container — lists, inventories |
| `ViewportFrame` | Renders 3D models inside a 2D frame |

### Display
| Class | Use |
|---|---|
| `TextLabel` | Static text display |
| `ImageLabel` | Display an image/icon |
| `VideoFrame` | Play a video asset |

### Interactive
| Class | Use |
|---|---|
| `TextButton` | Clickable button with text |
| `ImageButton` | Clickable button with image |
| `TextBox` | Text input field |

### Layout & Constraints
| Class | Use |
|---|---|
| `UIListLayout` | Stacks children vertically or horizontally |
| `UIGridLayout` | Arranges children in a grid |
| `UIPadding` | Adds inner padding to a container |
| `UICorner` | Rounds the corners of a Frame/Button |
| `UIStroke` | Adds an outline/border |
| `UIAspectRatioConstraint` | Locks width:height ratio |
| `UISizeConstraint` | Clamps min/max pixel size |
| `UITextSizeConstraint` | Clamps text size range |

### Sizing system

Roblox uses `UDim2` for Size and Position: `{scaleX, offsetX, scaleY, offsetY}`
- Scale = fraction of parent size (0–1)
- Offset = fixed pixels

| Pattern | UDim2 | Meaning |
|---|---|---|
| Full parent | `{1,0, 1,0}` | 100% width, 100% height |
| Half width, full height | `{0.5,0, 1,0}` | 50% × 100% |
| Fixed 200×50px | `{0,200, 0,50}` | 200px wide, 50px tall |
| Centered 80% wide | `{0.8,0, 0.8,0}` pos `{0.1,0, 0.1,0}` | 80% size, 10% margin each side |
| Bottom bar | size `{1,0, 0,60}` pos `{0,0, 1,-60}` | Full width, 60px tall, pinned to bottom |
| Top-right corner | pos `{1,-210, 0,10}` size `{0,200, 0,40}` | 10px from top-right |

`AnchorPoint` controls which point of the element maps to its Position:
- `{0,0}` = top-left (default)
- `{0.5,0.5}` = center
- `{1,1}` = bottom-right

## Key properties

### Frame / ScreenGui
| Property | Type | Notes |
|---|---|---|
| `Size` | UDim2 | Element size |
| `Position` | UDim2 | Element position within parent |
| `AnchorPoint` | Vector2 | Pivot point (0–1 each axis) |
| `BackgroundColor3` | Color3 | Fill color |
| `BackgroundTransparency` | number | 0 = opaque, 1 = invisible |
| `BorderSizePixel` | number | Legacy border width (use UIStroke instead) |
| `ClipsDescendants` | bool | Hides children outside bounds |
| `Visible` | bool | Show/hide element and children |
| `ZIndex` | number | Draw order (higher = on top) |
| `LayoutOrder` | number | Order when using UIListLayout/UIGridLayout |

### TextLabel / TextButton
| Property | Type | Notes |
|---|---|---|
| `Text` | string | Display text |
| `TextColor3` | Color3 | Text color |
| `TextSize` | number | Font size in pixels |
| `Font` | Enum.Font | See font table below |
| `TextXAlignment` | Enum | Left, Center, Right |
| `TextYAlignment` | Enum | Top, Center, Bottom |
| `TextWrapped` | bool | Wrap long text |
| `TextScaled` | bool | Auto-scale text to fit frame |
| `TextTransparency` | number | 0 = solid |
| `RichText` | bool | Enables `<b>`, `<i>`, `<font>` tags |

### ImageLabel / ImageButton
| Property | Type | Notes |
|---|---|---|
| `Image` | string | Asset ID: `"rbxassetid://123"` |
| `ImageColor3` | Color3 | Tint the image |
| `ImageTransparency` | number | 0 = opaque |
| `ScaleType` | Enum | Stretch, Slice, Tile, Fit, Crop |
| `SliceCenter` | Rect | 9-slice region (for ScaleType.Slice) |

### UIListLayout
| Property | Notes |
|---|---|
| `FillDirection` | Horizontal or Vertical |
| `HorizontalAlignment` | Left, Center, Right |
| `VerticalAlignment` | Top, Center, Bottom |
| `Padding` | UDim — gap between items |
| `SortOrder` | LayoutOrder or Name |

### UICorner
| Property | Notes |
|---|---|
| `CornerRadius` | UDim — `{0,8}` = 8px radius, `{0.5,0}` = fully round |

### UIStroke
| Property | Notes |
|---|---|
| `Color` | Border color |
| `Thickness` | Pixel width |
| `Transparency` | 0 = solid |
| `ApplyStrokeMode` | Contextual (inside) or Border (outside) |

## Fonts

| Font | Style |
|---|---|
| `GothamBold` | Clean modern bold — good for HUD labels |
| `Gotham` | Clean modern regular |
| `GothamMedium` | Medium weight |
| `FredokaOne` | Rounded, friendly — good for casual games |
| `Bangers` | Bold comic — action games |
| `Oswald` | Condensed modern |
| `SourceSans` | Default Roblox font |
| `SourceSansBold` | Bold default |
| `RobotoMono` | Monospace — good for timers, data |
| `Code` | Monospace code font |
| `Fantasy` | Serif fantasy |
| `Antique` | Old-style serif |

## Common UI patterns

### HUD panel (top bar)
```json
{
  "className": "ScreenGui",
  "name": "HUD",
  "properties": { "ResetOnSpawn": false },
  "children": [{
    "className": "Frame",
    "name": "TopBar",
    "properties": {
      "Size": {"X": [1,0], "Y": [0,50]},
      "Position": {"X": [0,0], "Y": [0,0]},
      "BackgroundColor3": [0.1, 0.1, 0.1],
      "BackgroundTransparency": 0.4
    },
    "children": [
      { "className": "UIListLayout", "properties": { "FillDirection": "Horizontal", "Padding": {"Scale": 0, "Offset": 10}, "VerticalAlignment": "Center" }},
      { "className": "TextLabel", "name": "ScoreLabel", "properties": { "Size": {"X": [0,150], "Y": [1,0]}, "Text": "Score: 0", "TextColor3": [1,1,1], "Font": "GothamBold", "TextSize": 18, "BackgroundTransparency": 1 }}
    ]
  }]
}
```

### Centered modal / popup
```json
{
  "className": "Frame",
  "name": "Modal",
  "properties": {
    "Size": {"X": [0,400], "Y": [0,250]},
    "Position": {"X": [0.5,0], "Y": [0.5,0]},
    "AnchorPoint": [0.5, 0.5],
    "BackgroundColor3": [0.12, 0.12, 0.15],
    "BackgroundTransparency": 0.05
  },
  "children": [
    { "className": "UICorner", "properties": { "CornerRadius": {"Scale": 0, "Offset": 12} }},
    { "className": "UIStroke", "properties": { "Color": [0.3,0.3,0.4], "Thickness": 1 }},
    { "className": "TextLabel", "name": "Title", "properties": { "Size": {"X": [1,0], "Y": [0,40]}, "Text": "Title", "TextColor3": [1,1,1], "Font": "GothamBold", "TextSize": 22, "BackgroundTransparency": 1 }},
    { "className": "TextButton", "name": "CloseButton", "properties": { "Size": {"X": [1,-20], "Y": [0,40]}, "Position": {"X": [0,10], "Y": [1,-50]}, "Text": "Close", "Font": "GothamBold", "TextSize": 16, "BackgroundColor3": [0.2,0.4,0.8] }}
  ]
}
```

### Health bar
```json
{
  "className": "Frame",
  "name": "HealthBarBG",
  "properties": {
    "Size": {"X": [0,200], "Y": [0,18]},
    "BackgroundColor3": [0.15, 0.05, 0.05]
  },
  "children": [
    { "className": "UICorner", "properties": { "CornerRadius": {"Scale": 0, "Offset": 6} }},
    {
      "className": "Frame",
      "name": "HealthBarFill",
      "properties": {
        "Size": {"X": [1,0], "Y": [1,0]},
        "BackgroundColor3": [0.2, 0.85, 0.2]
      },
      "children": [
        { "className": "UICorner", "properties": { "CornerRadius": {"Scale": 0, "Offset": 6} }}
      ]
    }
  ]
}
```

### Inventory grid
```json
{
  "className": "ScrollingFrame",
  "name": "Inventory",
  "properties": {
    "Size": {"X": [0,320], "Y": [0,400]},
    "BackgroundColor3": [0.1,0.1,0.1],
    "ScrollBarThickness": 6,
    "AutomaticCanvasSize": "Y"
  },
  "children": [
    { "className": "UIGridLayout", "properties": { "CellSize": {"X": [0,72], "Y": [0,72]}, "CellPadding": {"X": [0,6], "Y": [0,6]} }},
    { "className": "UIPadding", "properties": { "PaddingTop": {"Scale":0,"Offset":8}, "PaddingLeft": {"Scale":0,"Offset":8} }}
  ]
}
```

### Notification toast (bottom center)
```json
{
  "className": "Frame",
  "name": "Toast",
  "properties": {
    "Size": {"X": [0,300], "Y": [0,48]},
    "Position": {"X": [0.5,0], "Y": [1,-70]},
    "AnchorPoint": [0.5, 1],
    "BackgroundColor3": [0.15,0.15,0.15],
    "BackgroundTransparency": 0.1
  },
  "children": [
    { "className": "UICorner", "properties": { "CornerRadius": {"Scale":0,"Offset":8} }},
    { "className": "TextLabel", "name": "Message", "properties": { "Size": {"X":[1,0],"Y":[1,0]}, "Text": "Item collected!", "TextColor3": [1,1,1], "Font": "Gotham", "TextSize": 16, "BackgroundTransparency": 1 }}
  ]
}
```

## Rules

- Always use `create_ui_tree` for any UI with more than 2 elements — one call beats many.
- Set `ResetOnSpawn = false` on `ScreenGui` unless respawn reset is intentional.
- Use `AnchorPoint {0.5, 0.5}` + `Position {0.5,0, 0.5,0}` to center elements.
- Use `UIListLayout` or `UIGridLayout` for dynamic lists — never manually position items that could be laid out automatically.
- Use `UICorner` for modern rounded look. `CornerRadius {0,8}` is a good default.
- Use `TextScaled = true` only when the frame size is fixed and text length varies.
- `ZIndex` controls draw order within a ScreenGui — higher = drawn on top.
- `BackgroundTransparency = 1` to make a frame invisible but keep its children (useful for layout containers).
- Button logic (click handlers) must be wired in a `LocalScript` — MCP only creates the visual structure.
- After building UI in Studio, instruct the user to right-click the `ScreenGui` in Explorer → **Save to File** → save as `.rbxm` into `src/gui/` so Rojo tracks it. Alternatively wire it directly into `src/gui/` as Luau files using `script-scaffold`.
