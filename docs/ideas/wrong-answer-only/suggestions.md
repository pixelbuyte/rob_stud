# Wrong Answer Only — suggestions (Claude, branch `rob-clud_1`)

Status: parked idea, not being built. This page reviews ChatGPT's three suggestions, then adds
mine. Everything is in grids so both agents' takes can be compared side by side. Numbers come
from the build guide (`build-guide.md`); the maths is shown so it can be checked.

## 1. ChatGPT's suggestions, reviewed

| # | ChatGPT suggested | Verdict | Why | Change I'd make |
|---|---|---|---|---|
| 1 | Give Round 2 a clue: reveal one hint every 10 s (e.g. highlight the correct row) | **Agree, it is necessary** | One right button out of 20, one press each: chance nobody survives = 0.95^players. With 2 players that is **90%**; with 12 players it is still **54%**. Most games would end in Round 2 with no winner. | Clue ladder: 10 s the correct row lights (1 in 5), 20 s two wrong buttons in that row go dark (1 in 3), 30 s the right one flickers. Pressing early earns "Called it" cosmetic points, so waiting is safe but pressing early is cool. Also let others see who is standing safe, so watching is a skill. |
| 2 | Sudden death if several players survive Round 5: back to the two-zone arena, shorter grace each swap | **Agree** | The guide has no rule for several survivors after Round 5. | Call it **Final Lie**. Swaps every 8 s, grace 4 s → 3 s → 2 s. Cap at 6 swaps; anyone still alive shares the win. And if everyone is eliminated on the same tick, those players share the win instead of "no winner". |
| 3 | Playtest with 4 friends before adding revives. Start paid items with private servers and funny elimination effects | **Agree** | A 2-player match can end at the first trap and teaches nothing about later rounds or spectating. Revives change who stays in the competition. | Keep it. Revive comes in phase 2 only if it passes the edge test (see section 4): once per game, never in Round 5 or Final Lie, visible to all, earnable free. |

## 2. Spec problems I found (fix before any build)

| Where | Problem | Maths / evidence | Fix |
|---|---|---|---|
| Round 3, Chase | **Nobody can survive.** The arch starts at local X=+95, players at 0, the corridor ends at X=-100. Arch speed 6 → 20 studs/s; walk speed is 16. | The arch covers 60 studs in 10 s, 140 in 20 s, 240 in 30 s. It reaches the far wall (195 studs) at about **26 s** of a 60 s round. | Make the track a loop (oval), or let the arch pass through a gap players can jump/slide past, or cut the round to 20 s. Loop is best: running away stays the answer. |
| Round 5, Swap | **Camping exploit.** Players are only killed for standing on the *unsafe* zone. The 4-stud CenterStrip (where everyone spawns) is neither zone. | Raycast hits CenterStrip → not unsafe → survives the whole round. | Kill anyone **not** on the safe zone after the grace ends. |
| Round 5, Swap | **3 s grace is too short.** From the far edge of one zone to the near edge of the other is 54 studs. | 54 / 16 studs/s = **3.4 s** > 3 s grace. Players at the far edge die no matter what they do. | Grace at least 4 s, or move the zones closer. |
| Round 2, Buttons | Unclear what happens if a safe player clicks again, or if anyone clicks during the 4 s rule card. | Not specified. | Ignore clicks from safe players. Disable buttons until the rule card ends. Use a ProximityPrompt fallback for touch users who miss the small cylinders. |
| Round 1, Platform | Players who stay on the StartPad over the 10-stud lava gaps drop into lava. | StartPad 50×50 covers parts of all four platforms and the gaps. | Fine as a trap, but show the platform outlines through the pad so the death is fair. |
| Lobby | Minimum 2 players. A lone player in a new, empty game waits forever and leaves. | New games start with very few players. | Start a **solo practice run** (no wins recorded) when alone; a second player joining starts the real countdown. |
| Ties | "Zero alive → no winner" feels bad after 5 rounds. | Happens often in small lobbies. | Players eliminated on the final tick share the win. |

## 3. Extra ideas

### Rounds and replay

| Idea | What it adds | Cost |
|---|---|---|
| **Round pool**: 8–10 rounds, pick 5 per game; Round 5 slot is always a "the rule turns honest" twist | 5 fixed rounds get memorised fast; a pool keeps veterans guessing | Medium (one new map each) |
| **Double bluff**: about 1 game in 6, one rule is actually true, with a small fair tell (the announcer says "Honestly..."; the sign uses a different border) | The newcomer-vs-veteran gap becomes veteran-vs-veteran | Low |
| New round: "Don't move" — standing still kills; moving keeps you alive | Easy to read, funny when people freeze | Low |
| New round: "Pick the biggest door" — only the smallest door is safe | Crowds rush the wrong way; good clips | Low |
| New round: "Jump to survive" — jumping gets you hit by ceiling sweepers; stay grounded | Fights instinct | Low |
| New round: "Follow the arrows" — arrows lead to traps; walk against them | Reuses Chase art | Low |

### Spectators (most players spend most of the game here)

| Idea | What it adds | Note |
|---|---|---|
| **Kill cam of the funniest death** shown at intermission | The clip people share, made in-game | Replay the last 3 s from server positions |
| **Spectator vote**: pick which of two rounds comes next | Eliminated players still matter | Vote only between fair rounds |
| **Prediction**: spectators bet free points on who survives the next round | Keeps spectators watching; earns cosmetic points | Free points only, never Robux |
| Reaction bursts visible to alive players (not just spectators), small and off to the side | Social pressure, laughs | Never cover the safe/unsafe cue |

### Return and progress (no punishment)

| Idea | What it adds |
|---|---|
| Free cosmetic currency ("Tickets") from playing, surviving rounds, "Called it" presses | Everyone progresses, not only winners |
| Titles for funny stats ("Trusted the SAFE sign 50 times") | Losing becomes a badge |
| First win of the day gives bonus Tickets; missing a day costs nothing | Reason to return with no streak pressure |
| Weekly new round or twist, announced in-game | Variety that brings people back |

## 4. Money for Wrong Answer Only

Competitive game, so most money comes from **showing off**. Edges are allowed only if they pass
the edge test in `docs/MONETIZATION.md`.

| Product | Effect | Tier | Price (R$) | Edge test | Phase |
|---|---|---|---|---|---|
| Private server | Play with your own group | 2 | 100 / month | n/a | 1 |
| Elimination effects (confetti burst, cartoon trapdoor, slow-mo spin, "wrong buzzer") | Your death becomes a show | 0 | 49–149 | n/a | 1 |
| Trails | Visible while running | 0 | 75–199 | n/a | 1 |
| Starter pack (1 effect + 1 trail + 1 title) | Good value, shown once in shop | 0 | 149 | n/a | 1 |
| VIP pass (tag, chat colour, extra reactions, lobby podium pose) | Status | 0–1 | 199 | n/a | 2 |
| Reaction pack / announcer voice pack | Expression while spectating | 1 | 49 / 149 | n/a | 2 |
| Gift any cosmetic to a player in the server | Social spending | 0 | item price | n/a | 2 |
| **Second Chance** (revive) | Come back once after elimination | 3 | 25–49 | Passes if: once per game, not in Round 5 or Final Lie, feed line shows it, one free token every 3 games | 2 |
| **Second Opinion** (hint) | In Round 2 only, removes 5 wrong buttons for you | 3 | 25 | Passes if: once per game, free token every 3 games, others can see you used it. Never reveals the answer. | 2 |
| Seasonal cosmetic set with an announced end date | Honest scarcity | 0 | 399–799 | n/a | 3 |
| Subscription test (weekly exclusive cosmetic + 2x Tickets) | Recurring | 0–2 | test price | Tickets buy cosmetics only | 3 |

Never sell: showing the correct button, the next safe zone, immunity, skipping a round, or
anything in Final Lie. One paid edge per player per game, total.

## 5. Skills note

The five `wao-*` skills are installed in `.claude/skills/`. When this game goes active, update
`wao-luau-conventions` to allow purchase prompts (native prompts, server grants) and the edge
limits above, and add Final Lie and the fixes from section 2 to `wao-playtest`.
