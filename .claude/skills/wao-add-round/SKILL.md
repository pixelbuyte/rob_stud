---
name: wao-add-round
description: Use when designing or adding a new round to Wrong Answer Only - the fake-rule test, RoundConfig entry, map origin slot, RoundSystem handler and reveal moment.
---

# Adding a round to Wrong Answer Only

## Design test (answer all four before building)

1. The fake rule: one short sentence a first-timer will obey without thinking.
2. What actually happens: the opposite, or a twist on it. Must be learnable after one death.
3. The reveal: what the server shows at round end so everyone sees why they died.
4. Fairness: a veteran can survive it every time; no pure coin-flip unless the round also
   gives a way to copy someone who got it right (like Round 2's safe player).

Reject ideas that need a long explanation, punish lag, or let a paid item skip them.

## Build order

1. **Origin:** pick an unused spot inside +-900 studs that doesn't overlap existing maps
   (current ones: Lobby 0,0,0; Platform -500,0,-500; Button 500,0,-500; Chase 0,0,-700;
   Coins -500,0,500; Swap 500,0,500). Suggested next slots: (0,0,700), (-800,0,0), (800,0,0).
2. **Map:** new Model `Map_<Name>` in Workspace/Maps, built at local origin then pivoted,
   12 SpawnLocations, styled per `wao-art-direction`.
3. **RoundConfig:** append `{ roundNumber, mapName, fakeRule, realRule, timeLimit, origin }`.
4. **RoundSystem:** add a handler keyed by mapName. It must: set up, run until
   `timeLimit` or `AliveCount <= 1`, use `EliminationHandler` with a new reason string,
   run the reveal, and reset every part it moved or destroyed.
5. **Test:** run the `wao-playtest` checklist plus the new round twice in a row to prove
   the reset works.

Follow `wao-studio-build` for the Studio work and `wao-luau-conventions` for the code.
