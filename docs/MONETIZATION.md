# Money playbook

Goal: earn real money from cool things players want to buy, including items that give an edge,
without breaking the game that makes people come back. A game that loses its players earns
nothing, so every rule here protects retention first.

Roblox programs and policies change. Check the current pages on create.roblox.com
(Monetization, Creator Rewards, Policy, Paid Random Items) before shipping anything.

## 1. Ways a Roblox game earns

| Stream | What it is | Best for |
|---|---|---|
| Game passes | One-time purchase, permanent (VIP, trail packs, perks) | Status, permanent perks, cosmetic bundles |
| Developer products | Repeatable purchase (revive, hint, boost, currency) | Consumables, soft edges |
| Private servers | Monthly price for a server the buyer controls | Friend groups, content creators; easy early money |
| Experience subscriptions | Monthly recurring perks | Only after strong retention |
| In-experience avatar items | Selling UGC/avatar items inside the game | Games with a strong look or mascot |
| Ads (rewarded/immersive) | Paid by Roblox for ad views where eligible | Extra income from non-spenders; check age and region rules |
| Creator Rewards | Roblox pays for engaged and returning users (check current terms) | Every game. Retention earns money even from players who never buy |

The last row matters most early: a fun, fair game with good retention earns before anyone spends.

## 2. The Edge Ladder

Every product gets a tier. The tier decides where it can be sold.

| Tier | Type | Examples | Competitive modes | Co-op modes |
|---|---|---|---|---|
| 0 | Cosmetic | Trails, skins, death effects, titles, emotes, horns | Yes | Yes |
| 1 | Social / expression | Reactions, victory poses, announcer packs, gifting, lobby props | Yes | Yes |
| 2 | Convenience | Private server, extra loadout slots, party rejoin, faster cosmetic unlocks | Yes | Yes |
| 3 | Soft edge | Revive, one extra clue, shield, small team boost | Limited (edge test) | Yes (edge test) |
| 4 | Hard edge | Permanent stat boost, the answer, skip a round, immunity, 2x score in ranked | **Never** | Only for PvE, and only if a free player can still finish |

### The edge test (Tier 3 and 4 must pass all six)

1. **Visible:** other players can see it was used (icon, effect, feed line).
2. **Limited:** at most one paid edge per player per match, with a cap per round.
3. **Free path:** the same item can be earned by playing at a pace a regular player reaches
   (target: about one per 3 games).
4. **Skill still matters:** it softens a mistake, it does not make the decision for you.
5. **Off in finals:** disabled in the last round, sudden death and any ranked/leaderboard mode.
6. **Fair loss:** a free player who loses to it still feels they could have won.

If an edge item passes the test but non-payer retention drops after launch, roll it back.

### Why co-op is the best place for edges

In co-op games (TIDELIFT, CART PANIC) an edge helps the whole team, so the buyer is the hero, not
the villain. Sell **crew-wide** boosts: one person buys, everyone benefits, friends see who did
it. This turns spending into a social moment instead of a fairness complaint.

In competitive games (Wrong Answer Only) spending should mostly be about **looking cool, showing
off and expressing yourself**: death effects, victory poses, reactions, titles. Soft edges are
allowed but capped by the edge test.

## 3. Price ladder (starting values in Robux)

| Band | Price | Use |
|---|---|---|
| Impulse | 25–49 | Revive, hint, single reaction, one-run boost |
| Small | 75–99 | One trail, one death effect, small pack |
| Mid | 149–249 | VIP pass, announcer pack, cosmetic bundle |
| Premium | 399–799 | Big cosmetic bundles, "everything this season" |
| Collector | 999+ | Cosmetic-only showcase sets; never power |

- One clear **starter pack** (good value, shown once in the shop, never a pop-up timer).
- Bundles must be cheaper than buying the parts separately, and say so honestly.
- Use the Creator Dashboard pricing tools (price testing, regional pricing) where available to the
  experience, instead of guessing.

## 4. What usually sells on Roblox

| Product | Why it works | Tier |
|---|---|---|
| VIP pass (tag, chat colour, lobby perk, extra reactions) | Status that everyone sees | 0–1 |
| Death / elimination effects | Losing becomes a show; clips get shared | 0 |
| Trails and auras | Always visible while playing | 0 |
| Private servers | Friend groups and YouTubers | 2 |
| Revive / second chance | Strong emotion at the exact moment of loss | 3 |
| Crew boost in co-op | Buyer helps friends | 3 |
| Gifting | Buy for a friend; social and generous | 0–1 |
| Limited-run cosmetics with a real end date | Honest scarcity, announced ahead | 0 |

## 5. Hard no's

- Paid random items (loot boxes, gacha) without shown odds and a
  `PolicyService:GetPolicyInfoForPlayerAsync` check (`ArePaidRandomItemsRestricted`). House rule:
  none in the prototype at all.
- Paid trading where the policy check says it is not allowed (`IsPaidItemTradingAllowed`).
- Fake scarcity, fake countdowns, "only 2 left" lies, pressure aimed at kids.
- Purchase prompts on join, during a round, or right after a death. Show the offer on the result
  or spectator screen, and only once per session for the same item.
- Paywalled core loop: a free player always gets the full game.
- Paying or rewarding players for invites, likes, favourites or follows.
- Off-platform payments or anything outside Roblox's terms.
- Selling information that solves a competitive challenge (the correct button, the safe zone).

## 6. Implementation contract

- **One catalog:** `ProductCatalog` shared module. Each entry: `id`, `kind` (pass/product),
  `tier`, `price` (display only; Roblox is the source), `effect`, `limitPerMatch`,
  `allowedInFinals` (false for Tier 3+), `freePath` description.
- **Server grants only.** Client calls the native prompt (`PromptProductPurchase`,
  `PromptGamePassPurchase`); the server grants via `ProcessReceipt` and
  `PromptGamePassPurchaseFinished` plus `UserOwnsGamePassAsync` (cached, re-checked on join).
- **Idempotent receipts:** store processed `PurchaseId`s in the player's record with
  `UpdateAsync`; return `PurchaseGranted` only after the save succeeds, otherwise
  `NotProcessedYet` so Roblox retries.
- **Edge limits are server checks**, not UI hiding: per match, per round, finals off.
- **Every use is announced** to the lobby (feed line or icon) for Tier 3+.
- **Studio:** test purchases only, on a separate data store; never real Robux in tests.

## 7. Phases (ship money after fun)

| Phase | Gate | Add |
|---|---|---|
| 0 Prototype | Loop works in playtests | Nothing paid. Free cosmetic unlocks to test progression. |
| 1 Launch | Live and stable | Private servers, 3–5 cosmetics, starter pack |
| 2 Early | ~100 daily players returning on their own | VIP pass, Tier 3 items that passed the edge test, gifting |
| 3 Growth | ~1,000 daily players | Rotating shop, seasonal cosmetics, subscription test |

## 8. Numbers to watch

| Metric | Why |
|---|---|
| Payer conversion (% of players who buy anything) | Is the shop appealing at all |
| Revenue per daily player | Overall money health |
| Day 1 / Day 7 return, **payers vs non-payers** | If non-payers return less after an edge item ships, it is hurting the game |
| Edge item use rate and win rate with/without | Is an edge too strong |
| Refund / complaint mentions | Early warning of a bad product |
