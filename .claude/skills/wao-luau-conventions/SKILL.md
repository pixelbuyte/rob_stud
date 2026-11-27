---
name: wao-luau-conventions
description: Use when writing or editing any Luau script for Wrong Answer Only - server authority, debounces, module layout, loops, remotes and DataStore safety.
---

# Wrong Answer Only - Luau conventions

## Authority

- The server decides every death, every round result and every stat. Clients only draw.
- Never accept a RemoteEvent from a client that eliminates, saves, or reveals anything.
  The only client-to-server remotes allowed are spectator reactions and "play again",
  and both are rate-limited (1 per 0.5 s per player) and type-checked.
- The correct button in Round 2 lives in a server-only variable. Never replicate it.

## Structure

- Shared data and helpers live in ModuleScripts (`RoundConfig`, `GameUtils`, `Theme`).
- `GameManager` is the only Script that drives the loop; `RoundSystem`,
  `EliminationHandler` and `DataStoreManager` are modules it requires.
- Start every file with `--!strict` where practical and a 2-line header comment saying
  what it owns.
- Get services once at the top with `game:GetService`.

## Kills and touches

- All touch kills go through `EliminationHandler.BindTouchKill(part, reason)`: resolve the
  player from the touching part's ancestor Humanoid, 0.5 s per-player debounce, ignore
  players already out.
- Zone checks (Round 5) use a downward raycast every 0.5 s, not Touched.
- `Eliminate` is idempotent.

## Loops and timing

- `task.wait`, `task.spawn`, `task.delay` only. Never `wait()` or `spawn()`.
- No bare `while true do` without a wait and an exit condition tied to round state.
- Per-frame motion uses `RunService.Heartbeat` and the connection is disconnected when
  the round ends. Track every connection in a table and clean it up in one place.
- Every round has a hard timeout of `timeLimit + 10` s in GameManager.

## Remotes

- Fire with plain data (strings, numbers, small arrays of names), never Instances you
  didn't create for the client.
- One listener per RemoteEvent on the client, in ClientController or SpectatorController.

## DataStores

- Every call wrapped in `pcall`, 3 retries with 1/2/4 s backoff.
- Save on PlayerRemoving, at game end, and in `game:BindToClose`.
- A DataStore failure must never stop the game loop or the winner screen.

## Done means

The script parses (no Output errors on Play), every require resolves, and the step's
behaviour was exercised at least once in a 2-player local test.
