> **Parked reference, not active instructions.** Kept so the idea is not lost. Do not build from this until the owner makes this game active in `CLAUDE.md`. Studio-wide rules in `/CLAUDE.md`, `docs/DESIGN_RULES.md` and `docs/MONETIZATION.md` win over anything here.

# Game design — CART PANIC

## Pitch
A 1–4-player team raids a surreal supermarket for six minutes. Grab absurd merchandise, load a shared cart, and get back to checkout before closing. A shopping trip becomes a slapstick disaster. Working title, not cleared for publication.

Audience: broad Roblox audience; readable on phones, friendly non-graphic danger, no voice-chat requirement. Use original characters and fictional brands. No gambling or paid random rewards.

## Core loop
Lobby -> instant solo start or invite friends -> short shop run -> extract a loaded cart -> funny results and collection progress -> replay or return to lobby. No long mandatory tutorial. In the first 30 seconds teach grab, deposit and exit by three contextual prompts. Players can extract early. A round ends on timer or successful extraction. First-run targets: actionable input <10s, first pickup <30s, first deposit <45s; test instead of assuming.

## MVP rules (hypotheses)
- One party per active server for prototype; 1–4 players, 360-second round. Do not implement cross-server matchmaking until the loop is proven. Late arrivals spectate or wait in lobby; reconnect policy must be explicit.
- One item carried per player; 8 cart slots, ordinary item = 1, bulky item = 2. Players deposit through an interaction near cart. Server owns inventory and cart state. A simple cart controller with a driver and deterministic constraints is preferred over uncontrolled physics. Other players can take over when driver leaves.
- MVP loot examples: cereal 10, giant cheese 20, disco toaster 45, giant TV 65, suspicious golden duck 120. These values are tuning examples; score is not Robux. Slots and values are server-defined. All item art is original.
- One early hazard: a cleaning robot follows a short telegraphed patrol and knocks unsecured carried items loose, with recovery. Later: freezer vents, conveyors, vault gate. No graphic damage. Do not make long forced stuns.
- Closing: at 60s clear warning; 20s stronger audiovisual cue, readable with sound off. Players and cart must reach exit; allow extraction if at least one active player and cart are inside. Remaining teammates do not prevent team payout. No player can sabotage a teammate's rewards. End-on-extract is clearly communicated. Timeout loses unbanked cart loot; grant a modest participation reward with anti-AFK checks. Test whether early extraction and timeout incentives feel fair.
- Everyone in an active team shares earned base currency. Avoid last-hit or cart-driver ownership. Track optional personal highlights, not reward competition. Solo can carry/deposit and move cart without help; scale encounter pressure or goals by team size, not by monetization.
- Initial results show team score, recovered items, a funny highlight based on actual events, collection progress, rematch and invite options. A team highlight is optional and never fabricates what a player did.

## Why friends matter
Specialize without rigid classes: cart driver, scout, collector, hazard spotter; swap roles freely. Bulky-item co-op assistance is optional and later. Shared cart is a visible team goal. Contextual pings replace mandatory chat; invite via supported Roblox social prompt where available, with clear fallback. No fake notifications or contact harvesting. Joining with a friend reduces coordination cost rather than gating rewards. Solo players receive a complete game.

## Reward design
Immediate: satisfying pickup sound, deposit feedback, value/slots UI, predictable visual progress.
Round: earned currency, team recap, clear next unlock. Example unlock targets 1/3/6 completed runs; tune by observed time.
Long-term: item collection book, cart cosmetics, emotes, badges, store variants; cosmetics never improve earnings or movement. Permanent collection unlocks, no streak reset or expiry pressure. Free players can earn appealing cosmetics. Direct-priced optional cosmetic shop only after fun is verified; no paid loot boxes, pay-to-win, artificial scarcity, purchase pressure or loss of earned progress.

Psychology: competence (learn routes), autonomy (choose risk/exit), relatedness (help friends), curiosity (unusual loot), surprise (fair telegraphed hazards), visible achievable progress. These are design hypotheses, not guaranteed retention. Optimize for enjoyable voluntary returns, not compulsion.

## MVP exclusions
Trading, pets, prestige, daily streaks, battle pass, paid boosts, endless currencies, procedural entire map, mandatory social play, cross-server matchmaking, full physics destruction and elaborate AI. One map, one cart, five items, one hazard, one loop.

## Metrics and playtest
Measure join->first pickup/deposit, first-round completion/extraction, voluntary replay, D1/D7 retention, invite prompt eligibility/use and attributable joins if legitimately exposed by platform, party vs solo completion, frame rate and cart bugs. Do not collect private chat or external identifiers. Define event denominators and compare cohorts by device and session type. Early goals are hypotheses, not platform benchmarks. Interview testers: what confused you, what was funniest, would you bring a friend and why? Seek honest feedback, never compensate for positive ratings.
