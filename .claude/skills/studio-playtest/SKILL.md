---
name: studio-playtest
description: Use when testing a Roblox game in this repo, fixing an error pasted from Studio's Output, or writing a test checklist - covers the fix loop, the core multiplayer matrix and honest reporting.
---

# Playtest and fix loop

Game-specific checklists (e.g. `wao-playtest`) add to the matrix below.

## Fix loop

1. Read the script name and line from the pasted error first.
2. Read that code before changing anything.
3. Fix the one cause. Do not rewrite working code.
4. One line: what was wrong. One line: what changed.
5. Ask the owner to press Play again and paste the next error.

Common causes: requiring a Script instead of a ModuleScript, duplicate instances, remote name
typos, missing PrimaryPart, a part over 2048 studs, a Heartbeat connection left from last round.

## Core matrix (every game)

| Area | Check |
|---|---|
| Boot | No red Output errors on server start |
| Solo | Start, play, finish, result, replay with one client |
| Multi | 2 then 4 clients see the same state; one owner per shared control |
| Races | Double activation grants once; two players racing for control get one winner |
| Leave | Player leaving mid-round never stalls the round; remaining players can finish |
| Timeout | Timer expiry during an action cancels cleanly |
| Late join | Late player spectates/waits with the HUD in sync |
| Reset | Two rounds in a row; second starts clean (positions, timers, connections) |
| Phone | Studio device emulator: every action reachable, text readable, camera sees the goal |
| Gamepad | Start, play, exit and replay with native navigation |
| Money | Test purchases grant once; edge limits hold; no prompt mid-round (if shop exists) |
| Art | No Neon spam, no emoji, Builder Sans only |

Studio: Test tab → Clients and Servers → Local Server, 2 players (then 4). Output open.

## Reporting

Report the matrix with each row marked: Passed (owner-supplied result), Failed (what), or
Pending (exact manual steps). Never mark a row Passed from reading code alone.
