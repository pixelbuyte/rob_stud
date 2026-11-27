> **Parked reference, not active instructions.** Kept so the idea is not lost. Do not build from this until the owner makes this game active in `CLAUDE.md`. Studio-wide rules in `/CLAUDE.md`, `docs/DESIGN_RULES.md` and `docs/MONETIZATION.md` win over anything here.

# TIDELIFT
## Roblox game pitch and Claude Code build brief

Working title. Research snapshot dated November 26, 2026. Assumption: small team, Roblox Studio plus Claude Code, touch-first, original assets, private prototype first.

**Player pitch:** “You drive the crane. I’ll hook the rescue pod. Get me out before the pier goes under.”

TIDELIFT is a 1–4 player co-op salvage game. Recover stranded little rescue bots from a storm-damaged waterfront using one shared crane. Carry pods through a marked lift lane and land them on a ferry before the tide eats the low paths. Make friends useful in the first minute. Aim for a complete first run in roughly six minutes.

## What the market says

A dated, third-party concurrent-player snapshot at 17:20 UTC on Nov 26 showed Steal An Egg at ~656k, Brookhaven RP ~415k, 99 Nights in the Forest ~238k, and Adopt Me! ~200k. The chart also showed Throw Food For Animals ~196k and RIVALS ~179k. These are one snapshot of current play, not growth data or evidence that a specific mechanic caused success. [Chart](https://endgametools.com/en/roblox/charts)

| Signal | Design hypothesis to test |
| --- | --- |
| Collecting sits alongside social play at the top | Make the rescued bots recognizable and give players a visible rescue log with cosmetic progress. |
| Timed survival and repeatable action have large audiences | Use a visible tide and a short run with a clear finish. Give each replay a different route decision. |
| A chart spot does not tell us how players found friends | Observe whether playtesters ask for a rematch or tell a friend what role to play without prompting. |

Roblox documents qualified play-through rate, repeat play, and intentional co-play among Home recommendation signals. Treat these as reasons to make an honest thumbnail, a clear first minute, and a frictionless replay together. [Roblox Discovery](https://create.roblox.com/docs/discovery)

## The first playable run

1. **0:00–0:20, dock.** Spawn facing a yellow crane, marked ferry pad, and flashing rescue pod. HUD says “Hook a pod.” Touch, keyboard, and gamepad should all reach the same action. Let the host start immediately. A new arrival during a run watches from the safe dock and can join at the next start.
2. **0:20–1:00, first rescue.** The hook magnet snaps to a pod in a valid pickup zone. At two or more players, a ground player can ping a landing point and guide the operator. Show a strong light and sound when a pod locks onto the ferry. A solo player calls the hook from a local console and uses assisted crane mode from the mast.
3. **1:00–4:30, choose a route.** Deliver at least two pods total. Go after an optional third pod via the warehouse shortcut or the exposed pier, depending on the run variant. One player operates, one scouts, and two more can guide from opposite ends or activate a gate. All roles are voluntary and swappable at the crane. Nobody needs voice chat.
4. **4:30–6:00, finish.** Dock lever ends the run early once two pods are delivered. At timeout, resolve against delivered count. Debrief shows deliveries and useful actions, awards earned progress, and offers “Run Again” and “Invite Friend.” Allow players to replay with the current group from the same dock.

If a player is swept off a route, respawn on the safe dock after three seconds. If a suspended pod loses its route or the operator disconnects, return it to its last valid pickup zone and release controls. A ground teammate can reset a stalled hook. After a disconnect, remaining players can use solo assist and finish.

## Map graybox, Tenderlight Docks

Use one Roblox place. Author the graybox in Luau from a data table so Claude can reproduce it without Studio access. Keep the generated world under a dedicated runtime folder. Start with anchored parts and primitive geometry. All coordinates below are approximate X/Z positions in studs. Y is floor height.

| Area | Center X/Z | Size and floor | Gameplay |
| --- | --- | --- | --- |
| Safe dock | 0 / 0 | 64 × 32, Y=12 | Spawn, crane console, replay board, and clear view of first pod. |
| Ferry | 64 / 0 | 40 × 28, Y=12 | Landing pad and finish lever. Connect via high catwalk. |
| Training platform | 0 / 48 | 40 × 30, Y=6 | First pod in a clearly marked pickup ring. Connect to dock via ramp. |
| Warehouse | -58 / 76 | 56 × 46, Y=8 | Second pod or alternate pickup. Gate opens from both sides with a time-saving teammate interaction. |
| Outer pier | 60 / 96 | 28 × 54, Y=4 | Optional pod and earliest route closure. Elevated escape ramp. |
| High loop | around outer edge | 12+ wide, Y=14 | Always-open return path linking dock to the pickup areas. |

Approximate footprint 210 × 175 studs. Lay out paths from dock to training platform, training platform to warehouse and outer pier, and ferry to dock. Use blue rails for the high route, crane yellow for interactables, and pulse or stripes plus text for hazard cues. Show first pod, crane, and ferry from spawn. Use one sunken warehouse silhouette, oversized numbers, and a red tide gauge to orient players. Avoid tight mobile camera corridors. Time the low route vs high route before doing art.

Crane prototype: server-controlled kinematic lift. Use waypoint-based hook travel in a bounded rectangular footprint plus a few fixed vertical states for pickup, carry, and drop. The operator chooses a marked zone and actuates lift or drop. Client interpolates visuals. Ignore swinging physics until the full loop is fun; test a visual sway later. Ground players use pings that expire. First pod platform, warehouse, pier, and ferry must all lie in the crane's footprint. Add short beeps, a projected landing circle, and a chunky mechanical animation. Flood in three stages at 90, 180, and 270 seconds, with 15-second warnings. The first pod always spawns at training. Seed the second and optional pod layout from two authored configurations; each must leave two pods recoverable using the high loop at any tide stage.

## Social design and rewards

| Player motivation | Mechanic | What to look for |
| --- | --- | --- |
| Autonomy | Players choose crane, ground, or flex and decide whether to risk an extra rescue | Players make a route choice and can explain it. |
| Competence | Big signals on a clean latch, landing, and clutch recovery. Personal bests compare against the same variant. | Players improve on run two and name what they did better. |
| Relatedness | One player can call “drop it here” while another executes the rescue. Debrief credits both. | Each player can name something a teammate did that mattered. |

These are design hypotheses informed by game motivation research. [Ryan, Rigby, and Przybylski](https://selfdeterminationtheory.org/SDT/documents/2006_RyanRigbyPrzybylski_MandE.pdf)

Prototype economy: 20 bolts per delivered pod for each participating crew member and 10 extra when all three pods make it. Failed runs keep progress from deliveries that happened. Contributions include operating a successful lift, guiding an objective, or interacting with a gate. Track contribution credit without ranking friends against each other. Unlock a teal hard hat at 40 bolts, crane stripes at 120, and a ferry horn at 240. Cosmetics are permanently earned by lifetime bolts; a displayed progress bar shows the next unlock. No paid currency, random purchases, streak loss, invite payouts, or grind multipliers in the prototype. Use the rescue log and a different run layout to motivate return. After validating play, consider clearly priced cosmetics or crew paint sets.

Invite only from a voluntary button in the dock and debrief, using Roblox's native prompts. Check eligibility via CanSendGameInviteAsync before prompting and handle unsupported cases gracefully. A Roblox invite alone does not guarantee a reserved slot. First test native invites on private published Roblox clients. If parties routinely split, evaluate reserved-server routing as a follow-up with a real-device test. [Roblox player invite prompts](https://create.roblox.com/docs/production/promotion/invite-prompts)

## Repository and Claude workflow

Use Git, Luau, Roblox Studio, and Rojo v7 with the Studio plugin. Pin tool versions after checking release docs. Start with StyLua for formatting, Selene configured for Roblox if used, and a simple CI job that formats in check mode, lints, and builds the place. Add Wally only when there is a dependency to manage. Commit source and config. Keep generated place builds as artifacts. [Rojo v7](https://rojo.space/docs/v7/)

```text
CLAUDE.md
README.md
default.project.json
docs/design.md
docs/playtest.md
src/shared/          RoundTypes, GameConfig, MapSpec
src/server/          Bootstrap, MapBuilder, RoundService, CraneService,
                     PodService, TideService, RewardService
src/client/          Bootstrap, InputController, HUD, CraneView
.claude/skills/      roblox-slice/, roblox-playtest/ (create when useful)
.github/workflows/   checks.yml
```

Rojo maps shared to ReplicatedStorage, server to ServerScriptService, and client to StarterPlayerScripts. Use `rojo serve` for Studio iteration and `rojo build -o Tidelift.rbxlx` for a portable artifact. Keep map ownership in MapSpec and MapBuilder through the prototype. Any later move to Studio-authored assets needs a documented source-of-truth decision. Run Studio's multi-client tests for behavior that depends on replication. CI build success proves the project can be assembled. Test behavior in Studio or published Roblox clients. [Studio testing](https://create.roblox.com/docs/studio/testing-modes)

Put persistent rules in root `CLAUDE.md`: one milestone at a time, use strict Luau where practical, server owns shared game state and awards, touch-first, and report executed checks separately from checks pending Studio. When the workflows repeat, create project skills with a small `SKILL.md` in each directory. `roblox-slice` should tell Claude to read the map and event contracts, implement one playable change, run available checks, and document manual verification. `roblox-playtest` should list the one- and two-client checklist, mobile input checks, and regressions. Use your own project instructions instead of installing generic Roblox skill packs you haven't reviewed.

Server contracts: RoundService controls Dock → Running → Debrief → Dock using a round ID and server clock. CraneService owns a single operator and validates station proximity, allowable target, round phase, and cooldown on requests. PodService records Ground → Hooked → Delivered and commits a delivery once per pod per round. TideService reads authored zones and reports stage from the round timer. RewardService issues rewards once per eligible player per round. The client requests intent and renders replicated state. Native invite UI stays client-side. When persistence lands, use a versioned data record and atomic UpdateAsync with bounded retries and grant deduplication. Keep Studio tests on isolated data and display a retry state if loading fails.

## Build order and gates

| Milestone | Scope | Evidence to collect |
| --- | --- | --- |
| 1. One rescue | Rojo skeleton, generated dock, crane console, one pod, ferry, minimal HUD, reset | Solo and two-client Studio tests. One pod lands, delivery happens once, a second run resets cleanly, and touch controls are reachable. |
| 2. Complete run | All map zones, tide stages, two layouts, voluntary roles, disconnect recovery, debrief | Solo and paired teams can both finish. Disconnect during lift, late join, falling, and timer expiry all resolve. |
| 3. Closed playtest | Bolts and cosmetics, native invites, basic telemetry, sharper VFX and audio | Private published invite test across devices. Run with about 8–12 testers across solo and friend sessions. |
| 4. Publish candidate | Fix observed friction, performance pass on a real phone, original icon and thumbnail, permissions and experience settings | Owner reviews publish settings and Media. Only open wider testing after the loop meets the gates below. |

Initial playtest gates are product targets, not industry benchmarks. Aim for at least 7 of the first 10 new testers to complete a first rescue within 60 seconds without coaching. Aim for at least 3 of the first 5 friend groups to replay voluntarily, and ask “Who did what?” after the run. Treat results as directional with this sample. Capture time to first latch, first delivery, full run outcome, repeat start, device class and crew size. Track invite button clicks as clicks, and measure actual friend co-play separately where observable. For Roblox funnel analytics, track onboarding from spawn → start → latch → delivery → debrief. [Roblox funnel analytics](https://create.roblox.com/docs/production/analytics/funnel-events)

If players miss the objective, fix sightlines and the first prompt. If teams are idle, shorten the crane trip and give ground players a useful shortcut. If friends finish but don't replay, test a better second route or a stronger finale before adding collection breadth. Prototype launch creative after the loop works: icon shows a yellow crane over blue water, trailer opens on a player guiding a suspended pod while the pier floods, and thumbnail shows the crane, teammate, and ferry in the same shot. Use original game footage.

## Paste this into Claude Code

> Read this brief and inspect my existing repo first. Build only Milestone 1 of TIDELIFT. If the repo is empty, scaffold a Rojo v7/Luau project with source folders, CLAUDE.md and run instructions. For the graybox, create only the safe dock, first pickup platform, crane console, high connection to ferry, landing pad, and rescue pod. Use original anchored primitives generated from MapSpec so the build is reproducible. Implement start/reset, solo assist, a second player ping, server-validated crane commands, a one-time delivery, a small HUD and a debrief with replay. Skip persistent rewards, store, full flood, and matchmaking for now. Build the portable place if tools are available. Run available checks and report what passed. Give me a numbered Studio checklist for mobile emulation, one- and two-client tests, and clean round reset. Ask only when an existing repo or Studio source-of-truth conflict genuinely blocks you. Don't publish.

## What the owner needs

- An editable Roblox Studio place in a Roblox account or group you control, plus access for any human collaborators who need to open it.
- A local Git repo or existing repo link for Claude Code. If you already have a place or art, tell Claude which files own the map and give it the approved assets. Start in a blank private place otherwise.
- Studio with the Rojo plugin, one real phone for touch and performance checks, and a few friends to test. You can start Milestone 1 before arranging testers.
