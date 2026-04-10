---
name: luau-lint
description: Lint and format Luau code in a Roblox project using selene and stylua. Use when the user asks to lint, format, check style, or fix warnings in .luau/.lua files.
---

# luau-lint

Run selene (linter) and stylua (formatter) against Luau source in a Roblox project, and fix what can be auto-fixed.

## When to use

- "Lint this file" / "lint the project"
- "Format the Luau code" / "run stylua"
- "Fix the selene warnings"
- Before committing Luau changes

## Prerequisites

- `selene` and `stylua` on PATH (via Aftman/Rokit) — verify with `selene --version` and `stylua --version`
- A `selene.toml` at the project root with at least `std = "roblox"`
- A `stylua.toml` at the project root (indent style, column width, etc.)

If either config is missing, create a sensible default before running — don't silently skip.

## Core commands

| Action | Command |
| --- | --- |
| Lint whole project | `selene src` |
| Lint a single file | `selene src/server/Main.server.luau` |
| Lint with JSON output (for parsing) | `selene --display-style=json src` |
| Check formatting (no changes) | `stylua --check src` |
| Apply formatting | `stylua src` |
| Format a single file | `stylua src/server/Main.server.luau` |

## Workflow

1. Run `stylua --check src` first. If it reports diffs and the user asked to "fix" or "format", run `stylua src` to apply.
2. Run `selene src`. Parse output for `error` vs `warning` counts.
3. For each selene finding, try to fix in code — do NOT silence via `-- selene: allow(...)` unless the user asks or the lint is genuinely a false positive (explain why in that case).
4. Re-run both after edits to confirm clean.

## Roblox-specific selene std

The `std = "roblox"` line in `selene.toml` is required for selene to know about `game`, `workspace`, `script`, `Instance.new`, etc. Without it you'll get a flood of `undefined_variable` errors. If you see that flood, check `selene.toml` first.

For user-defined globals beyond the Roblox std, generate a project-specific std with:

```
selene generate-roblox-std
```

## Gotchas

- stylua's `call_parentheses` setting controls whether `foo "bar"` becomes `foo("bar")` — match the project's existing style, don't flip it mid-refactor.
- selene and stylua have no overlap in what they check; always run both.
- Test files sometimes need their own selene config (e.g., `TestEZ` globals) — use a `selene.toml` in the tests directory.
