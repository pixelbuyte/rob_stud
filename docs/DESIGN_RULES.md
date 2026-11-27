# Design rules

These apply to every game in this repo. They come from what the three parked ideas (Wrong Answer
Only, TIDELIFT, CART PANIC) have in common. Each rule is a hypothesis we check in playtests, not
a guarantee.

## 1. The first minute

| Rule | Why | How we check it |
|---|---|---|
| The goal is visible from spawn: what to do, where, and what danger looks like. | Roblox recommendation signals include whether new players actually play through. A confused first minute loses them for good. | 7 of the first 10 new testers do the first meaningful action within 60 s without coaching. |
| One next action on screen at a time ("Hook a pod", "Do the opposite"). | Phones have small screens and short attention. | Testers can say the goal after 15 s. |
| No mandatory tutorial. Teach with 2–3 contextual prompts. | Tutorials get skipped; doing beats reading. | Time to first input under 10 s. |
| A solo player can start immediately or do something useful while waiting. | Empty lobbies kill new games with low player counts. | Nobody waits more than 20 s with nothing to do. |

## 2. Readability

- Every landmark has one clear silhouette before any clutter is added.
- State is shown three ways: colour **and** shape/text **and** sound. Never colour alone.
- Big, honest telegraphs before anything that can kill or fail a player (warning time is a tuning
  value, start around 3–5 s for traps, 15 s for big map events).
- Touch first: 44 px minimum tap targets, controls clear of the native thumbstick and jump button
  and screen insets, camera never trapped in tight corridors.
- Keyboard, gamepad and touch all reach the same action through one action handler.

## 3. Friends

- Friends must be useful in the first minute, but a solo player still gets a complete game.
- Roles are voluntary and swappable; no rigid classes.
- No voice chat required. Use pings, markers and big shared signals instead.
- Debrief credits what each player did. Never rank friends against each other on rewards.
- Invites come only from a deliberate button (lobby, debrief), using native Roblox prompts with an
  eligibility check. No invite rewards.

## 4. Rewards and return

Built on three motivations from game research (autonomy, competence, relatedness):

| Motivation | What we give | What we look for |
|---|---|---|
| Autonomy | Real choices: route, risk, role, when to press | Players can explain why they chose |
| Competence | Big feedback on clean plays, personal bests vs the same variant | Run two is better and they can say how |
| Relatedness | "You called it, I did it" moments, shared credit | Each player names something a teammate did |

- Progress is predictable and permanent: lifetime unlocks, visible "next unlock" bar.
- No streak loss, no expiring progress, no fear-based daily chores.
- Return comes from variety (rotating rounds/layouts, new twists) and collection, not punishment.
- Failed runs keep what was earned.

## 5. Replay

- Each run has a different decision: random layout seed, round pool, optional risk.
- Something veterans know that newcomers do not (the shareable moment), but it must be learnable
  after one failure.
- "Play again" with the same group is one tap from the result screen.
- A run has a clear finish. Hard server deadline on every round so nothing can stall.

## 6. Fairness

- No pure coin flips without a way to read or copy information (watch others, clues over time).
- Nothing punishes lag (generous hitboxes, grace windows, server-side checks with tolerance).
- Disconnects resolve cleanly: the remaining players can always finish.
- Paid items follow the Edge Ladder in `MONETIZATION.md`. Competitive modes never sell the answer.

## 7. The design test (answer before building anything new)

1. **One sentence:** can a first-timer understand it in one short line?
2. **The moment:** what is the clip or story players will retell?
3. **The lesson:** what does a player learn after failing once?
4. **The reveal:** how does everyone see why something happened?
5. **Friends:** what does a second player add?
6. **Solo:** can one player still do it?
7. **Fairness:** can a skilled player succeed every time? Does lag or money decide it?
8. **Cost:** how many days, and what can be cut?

Reject ideas that need a long explanation, punish lag, or let a paid item skip them.

## 8. Playtest gates

| Gate | Target (directional with small samples) |
|---|---|
| First action | 7 of 10 new testers within 60 s, no coaching |
| Voluntary replay | 3 of 5 friend groups replay without being asked |
| Credit | Each player can answer "who did what?" |
| Stability | No red Output errors; round reset works twice in a row |
| Phone | All actions reachable, text readable, 30+ FPS on one real mid-range phone |

Track the funnel: spawn → start → first action → first success → result → replay. Compare by
device and by solo vs group. Track invite clicks as clicks, not as friends who joined.

## 9. What to fix first when a gate fails

| Symptom | Fix first |
|---|---|
| Players miss the objective | Sightlines from spawn and the first prompt |
| Players idle | Shorten trips, give idle players a useful action |
| Groups finish but do not replay | Better second run variety or finale before adding content breadth |
| One player dominates | Swap roles, spread credit, add a reason to hand off |
| Rage quits | Telegraphs, grace windows, faster respawn/spectate |
