> **Parked reference, not active instructions.** Kept so the idea is not lost. Do not build from this until the owner makes this game active in `CLAUDE.md`. Studio-wide rules in `/CLAUDE.md`, `docs/DESIGN_RULES.md` and `docs/MONETIZATION.md` win over anything here.

# TIDELIFT — Claude Code project instructions

## Mission

Build an original, touch-first Roblox co-op game for 1–4 players. A crew uses a shared dock crane to rescue stranded bots and deliver their pods to a ferry as the tide rises. The signature moment is one friend calling the landing while the other drops the pod. Solo players can complete the same objectives using assisted controls.

Start by reading this file, the repo tree, and any existing project and design files. Preserve working code and identify the source of truth for the map before editing. If a repo clearly belongs to a different game, surface the mismatch before overwriting anything. Use the current milestone as scope. In a blank repo, start with Milestone 1.

## Which Claude model to use

Choose in Claude Code with `/model` or at launch using `claude --model <alias>`. This file provides routing guidance. Model selection is controlled by the active session and available account models.

| Model | Use it for |
| --- | --- |
| **Sonnet** (`sonnet`) | Default builder. Rojo setup, graybox geometry, Luau features, input bindings, HUD, tuning, and ordinary bug fixes. Use it for the complete first playable slice. |
| **Opus** (`opus`) | Architecture that crosses several services, round cleanup and disconnect races, difficult replication bugs, saving and migration decisions, and reviewing a complex diff before merging. Give it the repro, affected files, and invariants. |
| **Haiku** (`haiku`) | Scoped supporting work with obvious expected output, such as updating asset tables, drafting QA scripts or labels, or summarizing logs. Review the result before merging. |

Keep Sonnet when it can finish the task. Use whichever available equivalent is closest if an alias is unavailable. [Claude Code model configuration](https://code.claude.com/docs/en/model-config)

## Build scope

| Milestone | Deliverable |
| --- | --- |
| **1 — build now** | Dock, first pickup platform, one rescue pod, functional crane, ferry, one partner ping, solo assist, simple HUD, result, replay. One delivery completes the round. |
| 2 | Full map, tide, two authored mission layouts, extra pods, role handoffs, timeout and flood handling. Two of three delivered pods win. |
| 3 | Persistent cosmetic progress, optional native friend invites, events, device testing, then visual polish. |

Build Milestone 1 without live tide, shop, persistence, or reserved servers. Keep a 360-second server deadline to exercise timeout and reset. A player should understand the task from spawn and land a first pod within 60 seconds in a fresh playtest. Treat all tuning values below as starting values. Keep them in one config and document any playtest-driven changes.

## Player movement and input

- Use the native Roblox R15 controller, third-person camera, and normal touch thumbstick. Begin with standard walk speed (16 studs/sec) and default jump. Give all required paths walkable ramps and rails. Save sprint, stamina, ragdolls, and precision jumping for later exploration.
- Spawn near `(-12, 12, -6)` at safe-dock floor height, facing the pickup platform. Place the crane console within a few steps, with the pickup and ferry visible. Use Roblox spawn placement to position the avatar above the floor.
- Claiming the console shows crane controls and defaults to an unobstructed view of the active target. Keep a clearly labeled **Exit** button. Release controls upon exit, death, leaving station range, round end, or disconnect. Keep the native camera in the first slice. Preserve walking so players can step away at any time.
- Use native prompts for nearby world interactions. Route buttons and bindings through one action handler. While operating, keyboard Q/E cycles targets, F activates the primary action, and Esc retains Roblox menu behavior. Use Backspace for **Exit** only in crane mode. On controller, shoulder buttons cycle targets and X activates, while B exits crane mode. On touch, show explicit **Target**, **Hook/Drop**, and **Exit** controls clear of native controls and screen insets. Highlight the chosen pad in-world and label it on every device.
- The spotter uses a prompt at an authored pad marker to place a ping. Show the marker and label for 5 seconds. Limit to one active ping per player and allow immediate replacement after a short cooldown. The carrier should be able to use the solo cycle at any crew size, including after a teammate disconnects. No voice or simultaneous button press required.
- Below the water hazard plane, reset a fallen player to safe spawn after 3 seconds. A respawning operator releases the console. Late arrivals spawn at the safe dock and wait for replay. Keep the objective and spectator message visible.

## Crane and pod movement

Milestone 1 uses a gantry and motorized trolley. The server owns all logical transforms, states, and deliveries. Move along known paths. Use consistent easing and fixed vertical clearance. Disable free-swing simulation.

| Setting | Start value |
| --- | --- |
| Working footprint | X −20 to 72, Z 0 to 55. Extend from authored bounds in Milestone 2. |
| Hook carry level | World Y=25. Maintain clearance for the suspended pod across the route. |
| Horizontal speed | 24 studs/second, with a short eased start and stop. |
| Hoist speed | 13 studs/second. |
| Console range | 10 studs from the interaction anchor. |
| Pickup/drop pause | 0.25 sec latch cue, 0.35 sec dock cue. |
| Primary action | Hook when empty at a valid pickup target. Drop only when loaded and aimed at an empty ferry pad. |

1. **Select.** Empty hook targets the training pad. Loaded hook targets the ferry. In Milestone 2, cycle only valid pads. Target preview uses a ring, name, and shape. Previewing never moves or delivers a pod.
2. **Hook.** On action, validate operator, current round, legal target, and idle crane. Raise to the carry level if needed. Move horizontally above the target. Lower vertically to its socket. Latch once, play a click and brief light pulse, then raise vertically to carry height.
3. **Carry and dock.** On **Drop**, travel horizontally at carry height, lower until the pod base aligns to the ferry pad, pause, release, then retract. Count delivery only after the pod is seated and the server commits its transition. Mark the pad and objective visibly. For the three-pod milestone, use three ferry sockets or clear each delivered model into a visible staging slot.
4. **Recover.** If the operator leaves, the round ends, or a path becomes invalid, cancel the active move. Return any undelivered pod to its last valid pickup socket and release control. Committed deliveries remain committed until replay. A dock reset prompt can clear a stalled crane when there is no active operator. Two clients racing for control must see one winner.

Represent poses as phase, start pose, end pose, server start time, and duration. Let clients interpolate visual poses from that state, with a current snapshot for late viewers. Use anchored server-owned markers and local visual copies, with one visible rendering path per player. Use model pivots to preserve geometry offsets. Keep the suspended pod aligned beneath the hook socket, and derive cable length and orientation from trolley and hook positions. A small optional visual sway can be added after playtesting, with a reduced-motion option. Crane pieces and moving pods should be non-colliding for the graybox. [Roblox model pivots](https://create.roblox.com/docs/reference/engine/classes/PVInstance)

## Map and asset models

Generate the initial map with a shared data-only `MapSpec` and a server `MapBuilder` inside a dedicated Workspace folder. Build exact coordinates from named landmarks. Floors below give top-surface Y. All other dimensions are tunable in studs.

| Model | Placement or structure | Purpose |
| --- | --- | --- |
| Safe dock | X/Z (0, 0), 64 × 32, floor Y=12 | Spawn, crane console, replay. Put railings on exposed edges and leave clear ramp openings. |
| Crane gantry | Yellow anchored beams, one trolley, cable, and hook. Use `ConsolePoint` and `HookSocket`. | Visible navigation landmark and the only moving machinery in the first slice. Keep columns outside player and pod paths. |
| Training platform | X/Z (0, 48), 40 × 30, floor Y=6. Connect to dock with a 12+ stud wide walkable ramp. | First pod, pickup ring, partner ping prompt. |
| Rescue pod | About 5 wide × 5 deep × 4 high. Original primitive shell and recognizable bot face. Pivot at bottom center, `CarrySocket` at top center. | Main objective. Use one shared pod model with cosmetic variations later. |
| Ferry and pad | X/Z (64, 0), 40 × 28, floor Y=12. Join to dock with a high catwalk. Use `PadCenter`. | Fixed delivery destination in the first slice. Seated pods remain visible for the result. |
| Warehouse | X/Z (−58, 76), 56 × 46, floor Y=8. | Milestone 2 shortcut with gate accessible from both sides. |
| Outer pier | X/Z (60, 96), 28 × 54, floor Y=4. | Milestone 2 exposed optional rescue. Give it a walkable escape route. |
| High loop | 12+ studs wide, Y=14. | Milestone 2 all-tide connection between dock and pickup areas. |

Use anchored primitive models first. Blue rails indicate high paths, yellow identifies machinery, and stripes plus text indicate danger. Distinguish state with symbols and sound as well as color. Define interaction anchors, collision geometry, and visual geometry separately where needed. Import only approved original or licensed assets, record asset IDs and provenance, and inspect any imported model for embedded scripts. Graybox one readable silhouette per landmark before adding clutter.

In Milestone 2, use a server clock with flood stages at 90, 180, and 270 seconds and 15-second warnings. Animate the non-colliding visible water up to the stage height. Resolve walkable zones and hazards from server state. Use authored high routes and crane access to keep at least two pods recoverable at every stage in each layout. Gates slide vertically and change traversal collision only at validated open or closed states. Warn players and keep a usable exit when a lower path closes.

## Round, HUD, and rewards

- **Dock → Running → Debrief → Dock.** Use a unique round ID and server deadline. Rebuild round-owned objects or reset them deterministically. Every async completion checks its round and current phase. Replay starts with a fresh pod, clock, operator, and cleared pings.
- Show one next action at a time: **Start Rescue → Hook Pod → Drop on Ferry → Rescue Complete**. Keep target selection, pod status, and timer visible where relevant. Use short contextual cues. Show result, crew contributions, and **Run Again** on the debrief.
- In Milestone 2, show delivered count and tide stage. After two pods, allow an early finish via the ferry lever or let players try the optional third pod. At timeout, resolve from delivered count.
- In Milestone 3, award 20 bolts per delivered pod to participating players, plus 10 when all three are delivered. Keep completed delivery rewards on failed runs. Display 40/120/240 lifetime-bolt cosmetic unlocks and next progress. Make earning predictable. No purchase or invite bonus in the beta. Use native friend invites from a deliberate button and honor platform eligibility.

## Repo and implementation contracts

- Use Luau with `--!strict` where practical and Rojo v7. Map `src/shared` to a named ReplicatedStorage folder, `src/server` to ServerScriptService, and `src/client` to StarterPlayerScripts. Pin chosen tool versions. Use `rojo serve` for editing and `rojo build -o Tidelift.rbxlx` for a portable build.
- Start with `GameConfig`, `MapSpec`, a server bootstrap, `MapBuilder`, `RoundService`, `CraneService`, and client input/HUD/rendering. Split out pod, tide, or reward modules when their responsibilities justify it. Document setup and manual checks in README and `docs/playtest.md`.
- Inputs are requests like `ClaimConsole`, `ReleaseConsole`, `PrimaryAction(targetId)`, and `Ping(targetId)`. Resolve targets from authored IDs. Validate request type, sender participation, character status, phase, proximity where relevant, server cooldown, and command legality. Client messages never supply a pod pose or grant progress.
- Make the pod lifecycle `Ground → Hooked → Delivered`. Clear pending transitions on reset and reject duplicate delivery. Make acquisition and delivery updates atomic within the server state machine. Clean up runtime objects and event connections across repeated rounds.
- Use StyLua and lint/build checks where configured. Use small project skills under `.claude/skills/` if the same build or QA checklist becomes repetitive. Keep the map spec in one place and link to it.

## Verification before handing off

| Run in Studio | Expect |
| --- | --- |
| One client | Spawn sees the goal. Start, claim, hook, carry, drop, win, and replay. Exactly one delivery. |
| Two clients | Both see matching crane and pod states. One owns controls. Other can ping. One player's exit or disconnect allows the remaining player to finish. |
| Edge cases | Double activation never duplicates a pod. Timeout during travel cancels cleanly. Disconnect during latch or drop causes one consistent outcome. Late join or replay keeps the HUD in sync. |
| Mobile emulation, then a real phone | All actions are reachable, camera can see hook and ferry, labels fit, and core movement feels responsive. |
| Gamepad | Can start, operate, exit, and replay using native navigation and bindings. |

Use Studio multi-client test mode and network emulation if available. Document what actually ran, any failures, and the exact manual steps still pending. Obtain project-owner approval before publishing.

**First task:** Build Milestone 1 end to end. Show the changed files, available checks, Rojo/Studio run instructions, and anything that still requires a real device or a second Studio client.
