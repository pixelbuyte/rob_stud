---
name: studio-build
description: Use when creating map geometry, instances or project structure for any Roblox game in this repo, through Rojo source files or the Roblox Studio MCP (execute_luau).
---

# Building in Roblox

First read the active game's `games/<name>/GAME.md`. It declares the pipeline. If a game-specific
build skill exists (e.g. `wao-studio-build`), follow it instead of this one.

## Pick the pipeline (one per game, written in GAME.md)

| Pipeline | Use when | Source of truth |
|---|---|---|
| Rojo | Code and map spec live in Git; CI can build the place | `src/` files + a data-only `MapSpec` and a server `MapBuilder` |
| Studio MCP | Owner builds live in Studio through Claude Desktop | The Studio place; scripts written whole via `execute_luau` |

Never mix both for the same instances. Moving from one to the other needs a written decision in
GAME.md.

## Rules for both

- Maps are generated from named data (origin, size, floor Y) so they can be rebuilt exactly.
- Build each map around local (0, 0, 0) inside its Model, set PrimaryPart, then
  `Model:PivotTo(CFrame.new(origin))`. Keep all maps inside ±900 studs of world centre.
- A Part is capped at 2048 studs per axis. Never try to make anything bigger.
- Every gameplay part `Anchored = true`. Moving machinery is server-driven and non-colliding.
- Generated content goes in one dedicated Workspace folder with an ownership attribute; rebuilds
  replace only that folder, never the owner's hand-made work.
- Interaction anchors, collision geometry and visual geometry are separate parts when needed.
- Apply `studio-art` to anything visible.

## Studio MCP extras

1. Confirm `execute_luau` is registered before starting. If not: plugin on, fully quit Claude
   Desktop from the tray, relaunch, new chat.
2. Query the hierarchy for anything you are about to create; destroy duplicates (keep one).
3. If a script exists but is empty, recreate the instance before writing Source.
4. Write long scripts as one `[==[ ... ]==]` string assigned to `.Source` in a single call.

## Rojo extras

- `default.project.json`: `src/shared` → ReplicatedStorage folder, `src/server` →
  ServerScriptService, `src/client` → StarterPlayerScripts.
- `rojo serve` for live editing, `rojo build -o <Game>.rbxlx` for a portable build.
- Pin tool versions (Rojo, StyLua, Selene) after checking their release pages.
- CI proves the place assembles; it does not prove gameplay. Gameplay is proven in Studio.

## Report

Use the done report from `CLAUDE.md`, plus bounding boxes of new maps.
