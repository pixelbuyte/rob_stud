> **Parked reference, not active instructions.** Original guide by @zen, Nov 21, 2026, reformatted
> into tables so it reads cleanly. Content unchanged. See `suggestions.md` for spec problems found
> (Round 3 is unwinnable as written, Round 5 has a camping exploit) before building from this.

# Wrong Answer Only — Full Build Guide

## Overview

Wrong Answer Only is a 2–12 player Roblox elimination game: every round shows a rule, the rule is
a lie, and doing the opposite keeps you alive. Last player standing wins.

The hook is the gap between new and returning players. A first-timer confidently steps on the
platform labelled SAFE and dies; a veteran watches and laughs. That moment is the clip people
share and the reason friends get invited.

| Round | Map | Fake rule shown | What actually happens | Time |
|---|---|---|---|---|
| 1 | Map_Platform | Step on the SAFE platform | SAFE (green) kills; DANGER (red) is safe | 30 s |
| 2 | Map_Button | Do not press any buttons | One random button saves you; the other 19 kill; pressing nothing kills | 40 s |
| 3 | Map_Chase | Reach the finish line | The finish arch chases you; run away from it | 60 s |
| 4 | Map_Coins | Collect 10 coins | Every coin is a trap; touch none | 45 s |
| 5 | Map_Swap | Stay in the blue zone | Honest for 20 s, then the safe zone swaps at 20 s and 40 s | 60 s |

Replayability comes from three things: the correct button is randomised every game, Round 5
punishes players who memorised Round 1's logic, and spectators stay engaged with reactions while
they wait.

## Art direction

The look is a dark, matte game-show stage: near-black and slate surfaces, one blue accent for the
interface, and colour saved for things that mean something. The earlier builds read as AI-made
because every part was Neon, every sign had an emoji, and every colour was a default BrickColor.

Rules every prompt must follow:

- Materials: Concrete, Slate, Metal and SmoothPlastic. Neon only on a kill-zone reveal or a single
  accent strip, never on floors or whole platforms.
- Colours: set with Color3.fromRGB, never BrickColor names like "Bright red".
- Signal colours are muted, not saturated: the SAFE platform is a calm green, not lime.
- No emoji in world signs or UI text. Icons, if any, are simple shapes.
- Font: Builder Sans (Roblox's current UI font). Bold for headings, Medium for body.
- No gradients, drop shadows or rainbow UIStroke. One 1–2 px stroke at most.
- Lighting does the mood: Future lighting, low ambient, one key light per map.

### Palette

| Role | Color3.fromRGB | Use |
|---|---|---|
| Base dark | 14, 16, 20 | Lobby floor, UI panels |
| Surface | 32, 36, 44 | Walls, pedestals, corridors |
| Surface light | 58, 64, 76 | Trim, edges, platforms not in play |
| Text | 232, 236, 242 | All UI text |
| Text muted | 140, 148, 160 | Secondary labels, timers at rest |
| Accent blue | 47, 111, 237 | UI highlights, progress bars, selected state |
| Signal green | 76, 160, 104 | The SAFE platform and "survived" states |
| Signal red | 200, 64, 58 | Danger, elimination, lava |
| Signal amber | 214, 160, 54 | Coins, warnings, the correct-button reveal |

Lighting baseline: Technology = Future, ClockTime = 18.5, Brightness = 1.5, Ambient = (20, 22, 28),
OutdoorAmbient = (40, 44, 54), EnvironmentDiffuseScale = 0.4, a ColorCorrectionEffect with
Contrast 0.1 and Saturation -0.15, a subtle Atmosphere with Density 0.3. No lightning flashes or
rain.

## Toolchain and setup

Build everything through Claude connected to Roblox Studio over MCP, one step per prompt. Cursor
and Rojo were dropped: Cursor can't reach Studio, and Rojo had ARM problems.

1. Open Roblox Studio with a Baseplate place loaded.
2. In the Plugins tab, turn the Roblox Studio MCP plugin on.
3. Open Claude Desktop and start a new chat. Run a tool search to confirm execute_luau is actually
   available before prompting.
4. Pick Claude Sonnet for build steps; switch to Opus only for the final integration pass.
5. Paste one step at a time and wait for "done" before the next.

### Fixes learned the hard way

- MCP showing "running" does not mean the tools are registered. If they are missing, quit Claude
  Desktop fully from the system tray, relaunch, and open a new chat.
- Long scripts: write the code as a local Lua string and assign it to script.Source in one
  execute_luau call.
- Duplicates pile up across sessions. Every step should first find existing instances with the
  same name and destroy all but one.
- Scripts in ServerScriptService can come back empty after a session. Recreate the instance before
  writing its Source.
- Maps kept landing off the baseplate. Every map now builds from an explicit origin, and the
  baseplate is set once in Step 0.

## Architecture

The server owns every rule and every death; clients only draw what the server tells them.

Why maps kept landing off the baseplate: a Roblox Part can't be larger than 2048 studs on any
axis, so setting the baseplate to 4000, 10000 or 99999 silently capped it at 2048. The fix is a
2048 × 2048 baseplate and every map placed inside ±900 studs of the centre.

Each map is built around (0, 0, 0) inside its Model, then moved with
`Model:PivotTo(CFrame.new(origin))`.

| Model | Origin (X, Y, Z) | Footprint (studs) |
|---|---|---|
| Lobby | 0, 0, 0 | 100 × 100 |
| Map_Platform | -500, 0, -500 | 150 × 150 |
| Map_Button | 500, 0, -500 | 80 × 80 |
| Map_Chase | 0, 0, -700 | 200 × 30 (runs along X) |
| Map_Coins | -500, 0, 500 | 120 wide |
| Map_Swap | 500, 0, 500 | 105 × 50 |

| Location | Instance | Type | Job |
|---|---|---|---|
| ServerScriptService | GameManager | Script | State machine and round order |
| ServerScriptService | RoundSystem | ModuleScript | Runs each round's mechanic |
| ServerScriptService | EliminationHandler | ModuleScript | Alive list, deaths, reasons |
| ServerScriptService | DataStoreManager | ModuleScript | Wins, games, streaks, leaderboard |
| ServerScriptService | SpectatorSystem | Script | Ghost state and collision groups |
| ReplicatedStorage | RoundConfig | ModuleScript | Rule text, time limits, map names |
| ReplicatedStorage | GameUtils | ModuleScript | Teleport, map show/hide, formatting |
| ReplicatedStorage | Theme | ModuleScript | Palette and fonts from Art direction |
| ReplicatedStorage/GameEvents | 8 RemoteEvents | RemoteEvent | RoundStarted, RoundEnded, PlayerEliminated, GameOver, CountdownTick, RuleReveal, SpectatorMode, UpdateLeaderboard |
| StarterPlayerScripts | ClientController | LocalScript | Builds and drives all UI |
| StarterPlayerScripts | SpectatorController | LocalScript | Spectator camera and reactions |
| Workspace | Maps | Folder | The six map Models |

RoundSystem and DataStoreManager are ModuleScripts (not Scripts) so GameManager can require them.
A plain Script can't be required, which is a common reason the loop silently never starts.

GameManager never skips a state; a round with zero survivors goes straight to Game over with no
winner.

## Build steps

Sixteen prompts, in order, each scoped to one thing. Start every step with the house rules block
so each session builds the same way; if the repo skills are installed, the first line alone is
enough.

### House rules (top of every prompt)

```text
Use the wao-studio-build and wao-art-direction skills.
Rules: work through execute_luau. Before creating anything, find existing
instances with the same name and destroy duplicates. Every Part Anchored.
Colours from the Theme module or Color3.fromRGB, never BrickColor names.
Materials: Concrete, Slate, Metal, SmoothPlastic. Neon only where stated.
No emoji in signs or UI. Font: Builder Sans. Do only this step, then
report what you created, where, and any errors.
```

### Step 0 — World setup

```text
Step 0. Set Workspace.Baseplate Size to 2048x1x2048 at 0,-0.5,0,
material Slate, colour 32,36,44. Set Lighting: Technology Future,
ClockTime 18.5, Brightness 1.5, Ambient 20,22,28, OutdoorAmbient 40,44,54,
EnvironmentDiffuseScale 0.4. Add ColorCorrectionEffect (Contrast 0.1,
Saturation -0.15) and Atmosphere (Density 0.3, Color 40,44,54).
Delete any old Workspace.Maps folder and anything named Map_* or Lobby.
```

### Step 1 — Structure

```text
Step 1. Create the empty structure only:
ServerScriptService: Script GameManager, ModuleScript RoundSystem,
ModuleScript EliminationHandler, ModuleScript DataStoreManager,
Script SpectatorSystem.
ReplicatedStorage: ModuleScript RoundConfig, ModuleScript GameUtils,
ModuleScript Theme, Folder GameEvents with RemoteEvents RoundStarted,
RoundEnded, PlayerEliminated, GameOver, CountdownTick, RuleReveal,
SpectatorMode, UpdateLeaderboard.
StarterPlayerScripts: LocalScript ClientController, LocalScript
SpectatorController.
Workspace: Folder Maps with Models Lobby, Map_Platform, Map_Button,
Map_Chase, Map_Coins, Map_Swap.
Print the full tree when done.
```

### Step 2 — Theme, RoundConfig, GameUtils

```text
Step 2. Write three ModuleScripts.
Theme: return the palette from the build guide as Color3 values (BaseDark,
Surface, SurfaceLight, Text, TextMuted, Accent, SignalGreen, SignalRed,
SignalAmber) plus Fonts: Heading = BuilderSansBold, Body = BuilderSansMedium.
RoundConfig: array of 5 rounds with roundNumber, mapName, fakeRule,
realRule, timeLimit, origin (Vector3). Use: 1 Map_Platform "Step on the
safe platform" 30s (-500,0,-500); 2 Map_Button "Do not press any buttons"
40s (500,0,-500); 3 Map_Chase "Reach the finish line" 60s (0,0,-700);
4 Map_Coins "Collect 10 coins" 45s (-500,0,500); 5 Map_Swap "Stay in the
blue zone" 60s (500,0,500). Lobby origin (0,0,0). Minimum players 2.
GameUtils: ShuffleTable, FormatTime(sec) -> "0:45", GetSpawns(mapName),
TeleportToMap(players, mapName) with no two players on one spawn,
GetMapCenter(mapName), SetMapEnabled(mapName, bool).
```

### Step 3 — EliminationHandler

```text
Step 3. Write EliminationHandler. Keep an Alive set keyed by player.
Functions: Begin(players), Eliminate(player, reason) [no-op if already
out], IsAlive, GetAlive, AliveCount, BindTouchKill(part, reason) which
connects Touched with a 0.5s per-player debounce and calls Eliminate,
Reset(). Eliminate fires PlayerEliminated to all clients and
SpectatorMode to that player, and fires a BindableEvent Eliminated for
server listeners. Reasons: "Touched the wrong platform", "Pressed the
wrong button", "Pressed nothing", "Ran the wrong direction", "Touched a
coin", "Stood in the wrong zone", "Fell", "Left the game".
```

### Step 4 — GameManager

```text
Step 4. Write GameManager as a state machine: LOBBY -> COUNTDOWN ->
IN_ROUND -> INTERMISSION -> GAME_OVER -> LOBBY. Never skip a state.
Lobby waits for RoundConfig minimum players, then a 20s countdown that
cancels if players drop below minimum (fire CountdownTick each second).
For rounds 1-5: GameUtils.SetMapEnabled, teleport alive players, fire
RoundStarted(roundNumber, fakeRule, timeLimit), wait 4s, run
RoundSystem.Run(round) with a hard timeout of timeLimit + 10s, fire
RoundEnded(survivors, eliminated), 6s intermission. One alive -> end
game. Zero alive -> no winner, back to lobby. PlayerRemoving eliminates
with "Left the game"; players who join mid-game spectate.
GameOver(winnerName) for 8s, record via DataStoreManager, Reset, lobby.
```

### Step 5 — Lobby

```text
Step 5. Build Lobby around origin 0,0,0: floor 100x2x100 Concrete in
BaseDark, a 1-stud SurfaceLight trim, 12 SpawnLocations (Neutral,
Transparency 1 decal-free, 2 studs above floor) in a 4x3 grid, one
wall-mounted board 40x12x1 at the north edge with a SurfaceGui showing
"WRONG ANSWER ONLY" in Text colour, Builder Sans Bold, on Surface.
One PointLight overhead, warm white, Brightness 1.5, Range 60.
```

### Step 6 — Map_Platform

```text
Step 6. Build Map_Platform at local 0,0,0, then PivotTo its origin.
Floor 150x2x150 named Lava, SignalRed, Material Neon (the only neon),
kills via BindTouchKill "Fell". Four 30x4x30 Concrete platforms at Y=15
in a 2x2 grid with 10-stud gaps: SafePlatform SignalGreen, DangerPlatform
SignalRed (darker, Slate), MaybePlatform SignalAmber, RiskyPlatform
SurfaceLight. A small sign post on each with SurfaceGui text SAFE,
DANGER, MAYBE, RISKY. Start platform 50x2x50 at Y=25 named StartPad
with 12 spawns. SafePlatform kills ("Touched the wrong platform").
```

### Step 7 — Map_Button

```text
Step 7. Build Map_Button at local 0,0,0, then PivotTo its origin.
Room 80x20x80, Concrete in Surface, ceiling included. Front wall in two
halves with a 10-stud doorway. 20 cylinder buttons (3 wide, 1 tall,
SmoothPlastic, TextMuted grey) on 3x3x3 Metal pedestals in a 4x5 grid,
named Button1..Button20, each with a ClickDetector (MaxActivationDistance
12). 12 spawns inside the doorway. Back-wall sign "DO NOT PRESS ANY
BUTTONS". Two dim SpotLights, Brightness 0.8. No mechanic yet.
```

### Step 8 — Map_Chase

```text
Step 8. Build Map_Chase at local 0,0,0, then PivotTo its origin. Floor
200x2x30 Concrete, side walls 200x10x2 Slate. Finish arch at local X=+95:
two 2x12x2 pillars and a 24x2x2 top bar, Metal in SignalGreen, grouped in
a Model FinishArch with a sign "FINISH". Five floor arrows (flat
wedges, SurfaceLight) pointing toward the arch. 12 spawns at local X=0.
Invisible Part ArchKillZone (CanCollide false, Transparency 1) slightly
larger than the arch, inside FinishArch. No movement yet.
```

### Step 9 — Map_Coins

```text
Step 9. Build Map_Coins at local 0,0,0, then PivotTo its origin.
Octagon floor ~120 wide (8 Concrete wedges/parts), 4-stud Slate rim
wall, four 15x4x15 raised pads. Folder Coins with 60 coins: cylinders
3x0.6x3, Metal, SignalAmber, CanCollide false, scattered within 50 studs,
Y 2-6, none on spawns. 12 spawns around the inner edge. Coins don't
spin yet.
```

### Step 10 — Map_Swap

```text
Step 10. Build Map_Swap at local 0,0,0, then PivotTo its origin.
RedZone 50x2x50 at local X=-27 and BlueZone 50x2x50 at X=+27, SmoothPlastic
in SignalRed and Accent, 4-stud gap with a 4x2x50 Metal walkway named
CenterStrip holding 12 spawns. A sign above each zone: RED ZONE, BLUE ZONE.
```

### Step 11 — RoundSystem

```text
Step 11. Write RoundSystem.Run(round). Returns when the round is over.
R1: after 4s destroy StartPad collision (CanCollide false, fade); green
kills; survive the timer. At end flash SafePlatform red.
R2: pick a random correct button server-side only; correct click marks
player safe and turns that button SignalAmber for them; any other click
eliminates "Pressed the wrong button"; anyone not safe at time-out is
eliminated "Pressed nothing". Reveal the correct button at the end.
R3: move FinishArch toward -X with Heartbeat: 6 studs/s, +2 every 10s,
max 20; ArchKillZone eliminates "Ran the wrong direction". Reset after.
R4: coins rotate slowly; touching one eliminates "Touched a coin"; every
8s add 4 coins near random alive players. Restore 60 coins after.
R5: safeZone = "Blue". Every 0.5s eliminate alive players standing on the
unsafe zone ("Stood in the wrong zone"), using a downward raycast. At 20s
fire RuleReveal, give 5s grace, swap; at 40s swap again with 3s grace.
All rounds end early when AliveCount <= 1.
```

### Step 12 — ClientController (UI)

```text
Step 12. Write ClientController. Build all UI in code inside one
ScreenGui (ResetOnSpawn false, IgnoreGuiInset true). Use Theme colours
and Builder Sans only; panels BaseDark at 0.15 transparency, UICorner 6,
one 1px UIStroke in Surface. Scale-based sizes + UIAspectRatioConstraint.
Screens: Lobby status (players X/2, countdown), Rule card (round number
small in TextMuted, rule large in Text, 4s, simple fade + 8px slide),
HUD (round x/5 top-left, timer top-right turning SignalRed under 10s,
alive count), Eliminated card ("Eliminated" + reason, 2.5s), Intermission
(survivors list), Winner card (name + Play again). Feed line for other
players' eliminations, max 3 visible. TweenService only, 0.2-0.35s,
Quad Out. No emoji, no gradients, no shake.
```

### Step 13 — Spectators

```text
Step 13. SpectatorSystem: register collision group "Spectators" that
doesn't collide with Default; on elimination move the character 60
studs above the current map centre on an invisible pad, set parts to
the group, Transparency 0.6, WalkSpeed 16. Restore on Reset.
SpectatorController: on SpectatorMode, camera follows the nearest alive
player; Q/E (and two on-screen buttons) cycle targets; label "Watching
<name>". Five reaction buttons (text labels: LOL, RIP, WOW, OOF, GG)
showing over the spectator for 2s, seen by spectators only.
```

### Step 14 — DataStoreManager

```text
Step 14. Write DataStoreManager with DataStore "WAO_Stats_v1": Wins,
Games, RoundsSurvived, BestStreak, CurrentStreak per UserId. Load on
join, save on leave and at game end, pcall + 3 retries with backoff,
never error the game. leaderstats folder showing Wins. OrderedDataStore
"WAO_Wins_v1" top 10, refreshed every 120s, sent via UpdateLeaderboard.
One physical leaderboard board in the Lobby using Theme.
```

### Step 15 — Integration pass

```text
Step 15. Audit the whole game, don't rewrite working code. Check every
RemoteEvent has a sender and a listener; require paths resolve; each
map origin matches RoundConfig; no duplicate instances; no Part over
2048 studs; no BrickColor names or stray Neon; all text uses Builder
Sans. Simulate the loop in a 2-player local test and fix what breaks.
Report: issues found, fixes made, anything still risky.
```

## Testing checklist

Test in Studio with Test → Local Server → 2 players (then 4), with the Output window open. Paste
any red error straight back into the chat with "fix this, nothing else."

- [ ] No red errors in Output on server start
- [ ] Lobby countdown starts at 2 players and cancels if one leaves
- [ ] Round 1: SAFE kills, DANGER survives, lava kills
- [ ] Round 2: correct button differs between games; pressing nothing kills
- [ ] Round 3: arch speeds up and resets to its start for the next game
- [ ] Round 4: every coin kills; coin count resets after the round
- [ ] Round 5: both swaps fire on time with grace periods
- [ ] Round ends early when one player is left
- [ ] Spectator camera follows and cycles alive players
- [ ] Winner card shows, wins save, loop returns to the lobby
- [ ] Leaving mid-round doesn't stall the game
- [ ] UI readable on a phone (Studio device emulator, iPhone size)
- [ ] No Neon except lava; no emoji; all text in Builder Sans

## Publish and launch

Publish only after every box above is ticked; one broken round loses a new player for good.

1. Game Settings → Security: turn on Studio Access to API Services (DataStores need it).
2. File → Publish to Roblox. Name: Wrong Answer Only. Genre: Party & Casual. Max players: 12.
3. Description: "Every round tells you what to do. It's lying. Do the opposite and be the last one
   standing."
4. Thumbnail: a real in-game screenshot of a player mid-step onto the SAFE platform, dark stage
   lighting, title text in Builder Sans. No AI art, no emoji.
5. Complete the maturity questionnaire, set the experience Public.
6. Play the live version once with a friend before sharing.
7. Record the first-timer dying on SAFE. Post the clip to TikTok and YouTube Shorts, then share the
   link in Roblox Discords and r/roblox.

## Monetization roadmap

Ship free first; add paid items only once players come back on their own. Each phase starts when
the gate is met.

| Phase | Gate | Add | Price (Robux) |
|---|---|---|---|
| 1 — Launch | Live and stable | Private servers | 100 / month |
| 2 — Early | ~100 daily players | Revive (once per round, not in Round 5), VIP pass (tag + trail + extra reactions) | 25–50 revive, 199 VIP |
| 3 — Growth | ~1,000 daily players | Cosmetic shop (trails, death effects, titles), weekly new round | 25–200 |

Keep paid items cosmetic or one-off. A revive is fine; anything that lets a player skip a round or
see the correct button makes the game pay-to-win and kills the competitive hook. Skip hard daily
play limits: in a 2–12 player lobby game they shrink the lobbies everyone else needs.

## Repo skills

| Skill | Loads when | What it enforces |
|---|---|---|
| wao-studio-build | Any change made in Studio over MCP | Dedupe first, map origins, 2048-stud limit, writing long Source, the done-report format |
| wao-art-direction | Building parts, lighting or UI | Palette, materials, Builder Sans, no Neon/emoji/gradients |
| wao-luau-conventions | Writing or editing any script | Server authority, debounces, module layout, no infinite loops, pcall on DataStores |
| wao-playtest | Testing or fixing errors | The checklist above and the paste-error-fix-retest loop |
| wao-add-round | Designing or adding a new round | RoundConfig entry, origin slot, RoundSystem handler, the reveal moment |

With the skills installed, a step prompt can be one line, for example:
`Use wao-studio-build. Do Step 7 from the build guide.`
