---
name: wao-playtest
description: Use when testing Wrong Answer Only or fixing an error from Studio's Output - runs the playtest checklist and the paste-error, fix-one-thing, retest loop.
---

# Wrong Answer Only - playtest and fix loop

## How to test

Studio: Test tab, Clients and Servers, Local Server, 2 players (then 4). Keep View > Output
open. Test on the server window and both client windows.

## Fix loop

1. Zen pastes a red error. Read the script name and line number in it first.
2. Read that script's Source around the line before changing anything.
3. Fix the one cause. Do not rewrite working code or "improve" nearby parts.
4. Say what was wrong in one line and what you changed in one line.
5. Ask Zen to press Play again and paste the next error, if any.

Common causes in this project: a Script being `require`d (must be a ModuleScript), a
duplicate instance with the same name, a RemoteEvent name typo, a map Model missing its
PrimaryPart, a part over 2048 studs, a Heartbeat connection left running from last round.

## Checklist (all must pass before publishing)

- [ ] No red errors in Output on server start
- [ ] Lobby countdown starts at 2 players and cancels if one leaves
- [ ] Round 1: SAFE kills, DANGER survives, lava kills
- [ ] Round 2: correct button changes between games; pressing nothing kills
- [ ] Round 3: arch speeds up and is back at its start next game
- [ ] Round 4: every coin kills; coins reset after the round
- [ ] Round 5: both swaps fire on time with grace periods
- [ ] Round ends early when one player is left
- [ ] Spectator camera follows and cycles alive players
- [ ] Winner card shows, wins save, loop returns to lobby
- [ ] A player leaving mid-round doesn't stall the game
- [ ] UI readable in the phone emulator
- [ ] No Neon except lava, no emoji, all text Builder Sans

Report results as the checklist with each box ticked or marked with what failed.
