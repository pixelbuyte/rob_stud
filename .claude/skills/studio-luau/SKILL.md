---
name: studio-luau
description: Use when writing, editing or reviewing any Luau script for a Roblox game in this repo - server authority, remote validation, round state machines, loops, cleanup and DataStore safety.
---

# Luau rules

Game-specific conventions (e.g. `wao-luau-conventions`) add to these; they never loosen them.

## Authority

- Server decides every death, result, reward, purchase grant and save. Clients draw.
- Client → server remotes carry intent only: `ClaimControl`, `PrimaryAction(targetId)`,
  `Ping(targetId)`, `Reaction(name)`, `PlayAgain`. Never a position, score, item or result.
- Validate each request: types and finite numbers, sender is a participant, current round ID,
  phase, alive state, range, cooldown, legality. Rate-limit per player and action.
- Do not yield between the check and the state change. Duplicate requests are harmless.
- Secrets (correct answer, next safe zone) stay in server-only variables. Never replicate them.

## Structure

- Shared config and data in ModuleScripts under ReplicatedStorage. Server services in
  ServerScriptService. One composition root (bootstrap) per side.
- Anything `require`d must be a ModuleScript.
- `--!strict` where practical; a 2-line header comment saying what the file owns.
- Tuning values only in the config module.

## Rounds

- Phases are an explicit state machine (e.g. Lobby → Countdown → Running → Result → Lobby).
  Never skip a phase.
- Unique round ID and a server deadline. Every async callback checks round ID and phase first.
- Every round has a hard timeout. Zero survivors / disconnects resolve to a defined outcome.
- Reset deterministically: rebuild or restore everything a round moved or destroyed.

## Loops and cleanup

- `task.wait`, `task.spawn`, `task.delay` only. No `wait()`, `spawn()`, `delay()`.
- No `while true do` without a wait and an exit tied to round state.
- Per-frame work on `RunService.Heartbeat`; keep connections in a table and disconnect them in
  one cleanup function at round end. Same for player-leave connections.

## Remotes

- Fire plain data (strings, numbers, small arrays). One client listener per remote.
- Group remotes in one folder created by the server at start.

## DataStores

- `pcall` every call, 3 retries with 1/2/4 s backoff, versioned record shape.
- `UpdateAsync` for anything that grants (wins, currency, purchases) with grant IDs to dedupe.
- Never overwrite with defaults when a load failed; show a retry state instead.
- Save on leave, at game end and in `BindToClose`. Separate data store names for Studio.
- No DataStore calls in per-frame code. A failure never stops the game loop.

## Done means

No Output errors on Play, all requires resolve, and the behaviour was exercised at least once in
the right test mode (solo or multi-client). Say which, or say it is pending.
