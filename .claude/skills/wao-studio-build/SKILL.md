---
name: wao-studio-build
description: Use for any change made to the Wrong Answer Only Roblox place through the Roblox Studio MCP (execute_luau) - creating instances, writing script Source, building or moving maps, or querying the hierarchy.
---

# Building Wrong Answer Only in Studio over MCP

You are editing a live Roblox Studio place through the Roblox Studio MCP. Zen works one
step at a time. Do exactly the step asked, nothing more, then report.

## Before every step

1. Confirm the MCP tools are registered (tool search for `execute_luau`). If missing, stop
   and say: turn the Studio MCP plugin on in the Plugins tab with a place open, fully quit
   Claude Desktop from the system tray, relaunch, open a new chat. "Running" in settings
   does not mean the tools are loaded.
2. Query the current hierarchy for anything you are about to create.
3. Deduplicate: if two or more instances share a name under the same parent, keep the
   first and destroy the rest. Duplicates pile up across sessions.
4. If a script you need exists but is empty, recreate the instance before writing Source.

## Project layout (source of truth)

| Location | Instance | Class |
|---|---|---|
| ServerScriptService | GameManager | Script |
| ServerScriptService | RoundSystem | ModuleScript |
| ServerScriptService | EliminationHandler | ModuleScript |
| ServerScriptService | DataStoreManager | ModuleScript |
| ServerScriptService | SpectatorSystem | Script |
| ReplicatedStorage | RoundConfig, GameUtils, Theme | ModuleScript |
| ReplicatedStorage/GameEvents | RoundStarted, RoundEnded, PlayerEliminated, GameOver, CountdownTick, RuleReveal, SpectatorMode, UpdateLeaderboard | RemoteEvent |
| StarterPlayerScripts | ClientController, SpectatorController | LocalScript |
| Workspace/Maps | Lobby, Map_Platform, Map_Button, Map_Chase, Map_Coins, Map_Swap | Model |

Anything `require`d must be a ModuleScript. A plain Script cannot be required.

## Map placement rules

- A Part cannot exceed 2048 studs on any axis; Roblox silently caps it. Baseplate is
  2048 x 1 x 2048 at (0, -0.5, 0). Never try to make it bigger.
- Build each map around local (0, 0, 0) inside its Model, set PrimaryPart to the floor,
  then `Model:PivotTo(CFrame.new(origin))`. Never hand-place at world coordinates.
- Origins (must match RoundConfig):
  - Lobby (0, 0, 0)
  - Map_Platform (-500, 0, -500)
  - Map_Button (500, 0, -500)
  - Map_Chase (0, 0, -700)
  - Map_Coins (-500, 0, 500)
  - Map_Swap (500, 0, 500)
- Every Part `Anchored = true`.
- After building, print `Model:GetBoundingBox()` and confirm it sits inside +-1000 on X/Z.

## Writing script Source

Write the whole script as one long string and assign it in a single `execute_luau` call:

```lua
local src = [==[
-- full script here
]==]
local s = game:GetService("ServerScriptService"):FindFirstChild("GameManager")
s.Source = src
print(#s.Source)
```

Use `[==[ ... ]==]` so brackets inside the code don't end the string early.

## Done report (always this shape)

```
Step N done.
Created: <instances with full paths>
Changed: <what moved or was edited>
Removed: <duplicates destroyed>
Checks: <bounding boxes, Source lengths, require test>
Problems: <anything that failed or still looks risky, or "none">
```

Also apply `wao-art-direction` for anything visible and `wao-luau-conventions` for code.
