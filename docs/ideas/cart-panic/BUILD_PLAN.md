> **Parked reference, not active instructions.** Kept so the idea is not lost. Do not build from this until the owner makes this game active in `CLAUDE.md`. Studio-wide rules in `/CLAUDE.md`, `docs/DESIGN_RULES.md` and `docs/MONETIZATION.md` win over anything here.

# Technical build plan

## Architecture (target, not implemented)
Rojo source tree. Shared typed configuration, item definitions and network contract in ReplicatedStorage.Shared. Server-only RoundService, LootService, CartService, HazardService, RewardService, ProfileService, TelemetryService under ServerScriptService.CartPanic. Client controllers under StarterPlayerScripts for input, UI, highlights and effects. Use one composition root and explicit service dependencies; don't add frameworks without need. Server owns round phase, deadline, item IDs, cart capacity, rewards and persistence. Client requests intent only, never a score, price, item position or player identity to trust.

Round state: Lobby -> Countdown -> Active -> Resolving -> Results -> Lobby. Use unique round IDs, server deadline and idempotent resolution. Disconnect cleanup and rematch reset are required. At most one active round in MVP server. Do not publish until server map deployment is intentional.

## Milestones / acceptance
1. Graybox: run Studio command in Edit mode; inspect dimensions, walk all lanes, spawn safely; confirm marker counts/unique IDs and accessible vault. Correct layout defects first. Source includes generated graybox only.
2. Vertical slice: basic round state, 5 item definitions, server pickup/drop/deposit, shared 8-slot cart, exit check, end-of-round score and rematch. Four-player and solo runs finish without UI dead ends. Use temporary simple geometry. No persistence, monetization or NPC until loop works.
3. Chaos: one patrol hazard, telegraphs, cart recovery, readable timer, contextual pings, accessible keyboard/touch/gamepad controls. Verify race conditions and failure recovery. Test with real friends.
4. Progress: idempotent per-round reward grant, reliable profile persistence, collection, 3 cosmetics, basic results and opt-in social prompt. Use separate development data stores. No purchase integration unless explicitly approved.
5. Art/launch readiness: original/cleared assets, device profiling, streaming evaluation, Studio multiplayer test, moderation/compliance review, accurate thumbnail and store copy, rollback plan. Limited public test before wider launch.

## Remote security and reliability
- Validate player membership, active round ID, alive/eligible state, item identity, item ownership, range and plausible motion, cooldown, finite numeric inputs, and capacity on server. Rate-limit per player/action. Server derives reward and quantity. Do not yield between check and state transition; make duplicate requests harmless. Reject stale requests after round resolution.
- Cart: one authorized driver at a time, server checks control and bounds; test server-owned movement vs prediction. If using network ownership, validate speed/teleports and do not let client physics decide item ownership/extraction. Ensure cart can recover without granting duplicate loot.
- Persistent rewards: use UpdateAsync with idempotent grant IDs and bounded history/reconciliation; handle failures, retries, shutdown and reconnects, avoid overwriting with defaults when loads fail. No data store calls in per-frame logic. Design before adopting third-party profile packages. Use platform policy and documentation for purchases if ever added; never grant via client events.
- No obfuscated assets, unknown scripts, hardcoded secrets, unsafe HTTP, arbitrary model insertion or destructive repo commands. Review dependencies and licenses. No automatic publish or spending without explicit approval.

## Test checklist
- Solo complete round; 2 and 4 clients complete; simultaneous pickup only grants one item; simultaneous deposit cannot exceed capacity; dropping, disconnecting and rejoining do not duplicate items; cart driver disconnect frees control; exit without cart rejects; timeout/exit race resolves once; late join waits; rematch cleans up old state; forged IDs/out-of-range/spam rejected; mobile action prompts not hidden; cart fits aisles; no UI dependence on sound/color alone; persistence load/write failures do not erase progress. Verify actual Studio logs, don't claim tests ran when they did not.
