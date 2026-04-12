# roblox-claude-skills

Claude Code skills for Roblox/Luau development. These give Claude context-aware commands tailored to a Rojo-based workflow.

## Skills

| Skill | Trigger | What it does |
|---|---|---|
| `rojo-sync` | "start rojo", "serve the project" | Starts/stops Rojo dev server, builds place files |
| `luau-lint` | "lint this", "format the code" | Runs selene + stylua, fixes warnings |
| `roblox-api` | "how do I use X service" | Looks up Roblox API docs without hallucinating |
| `script-scaffold` | "create a new script for X" | Generates Luau files with correct Rojo naming and boilerplate |
| `world-builder` | "spawn a part", "build a floor grid" | Creates and manipulates 3D objects in Studio via MCP |
| `studio-debug` | "run this code", "check the output log" | Executes Luau and inspects runtime state via MCP |
| `scene-architect` | "build me an arena", "create a parkour course" | Translates plain-English scene descriptions into 3D geometry via MCP |
| `game-bootstrap` | "bootstrap a sword fighting game" | Scaffolds a full game from a concept — scripts, remotes, systems, and a starter scene |
| `ui-builder` | "build a HUD", "create a health bar", "make an inventory screen" | Builds ScreenGui/SurfaceGui/BillboardGui hierarchies in Studio via MCP |

---

## Install

### 1. Clone this repo

```bash
git clone https://github.com/benhelland/roblox-claude-skills
cd roblox-claude-skills
```

### 2. Copy skills into Claude's skills directory

**Mac/Linux:**
```bash
mkdir -p ~/.claude/skills
cp -r rojo-sync luau-lint roblox-api script-scaffold world-builder studio-debug scene-architect game-bootstrap ui-builder ~/.claude/skills/
```

**Windows (bash):**
```bash
mkdir -p "$USERPROFILE/.claude/skills"
cp -r rojo-sync luau-lint roblox-api script-scaffold world-builder studio-debug scene-architect game-bootstrap ui-builder "$USERPROFILE/.claude/skills/"
```

Each skill is a folder containing a `SKILL.md` file. The final structure should look like:

```
~/.claude/skills/
  game-bootstrap/SKILL.md
  luau-lint/SKILL.md
  roblox-api/SKILL.md
  rojo-sync/SKILL.md
  scene-architect/SKILL.md
  script-scaffold/SKILL.md
  studio-debug/SKILL.md
  world-builder/SKILL.md
```

Skills take effect in the next Claude Code session — no restart required in most cases.

---

## Update

Pull the latest and re-copy any changed skills:

```bash
cd roblox-claude-skills
git pull

# re-copy all skills to pick up any changes
cp -r rojo-sync luau-lint roblox-api script-scaffold world-builder studio-debug scene-architect game-bootstrap ui-builder ~/.claude/skills/
```

Or copy only the specific skills that changed, e.g.:

```bash
cp -r world-builder ~/.claude/skills/
```

---

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/claude-code) installed and authenticated
- [Aftman](https://github.com/LPGhatguy/aftman) with `rojo`, `selene`, `stylua` installed via `aftman install`
- For MCP tools (`world-builder`, `scene-architect`, `studio-debug`, `game-bootstrap`): [boshyxd/robloxstudio-mcp](https://github.com/boshyxd/robloxstudio-mcp) registered via:

```bash
claude mcp add robloxstudio -- npx -y robloxstudio-mcp@latest
```
