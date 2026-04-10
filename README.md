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

## Install

Copy each skill folder into `~/.claude/skills/`:

```
~/.claude/skills/
  rojo-sync/SKILL.md
  luau-lint/SKILL.md
  roblox-api/SKILL.md
  script-scaffold/SKILL.md
  world-builder/SKILL.md
  studio-debug/SKILL.md
  scene-architect/SKILL.md
  game-bootstrap/SKILL.md
```

On Windows: `~/.claude/` is at `C:\Users\<you>\.claude\`

### Quick install (bash)

```bash
git clone https://github.com/benhelland/roblox-claude-skills
cd roblox-claude-skills
mkdir -p ~/.claude/skills
cp -r rojo-sync luau-lint roblox-api script-scaffold world-builder studio-debug scene-architect game-bootstrap ~/.claude/skills/
```

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/claude-code) installed and authenticated
- [Aftman](https://github.com/LPGhatguy/aftman) with `rojo`, `selene`, `stylua` installed via `aftman install`
- For MCP tools (`world-builder`, `studio-debug`): [boshyxd/robloxstudio-mcp](https://github.com/boshyxd/robloxstudio-mcp) registered with `claude mcp add`
