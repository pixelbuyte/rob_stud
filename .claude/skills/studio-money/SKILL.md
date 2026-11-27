---
name: studio-money
description: Use when designing, pricing, reviewing or implementing anything players pay for in a Roblox game in this repo - game passes, developer products, private servers, subscriptions, edge items, shops or offers.
---

# Money design and implementation

Read `docs/MONETIZATION.md` first. It is the source of truth; this skill is the checklist.

## Designing a product

1. Name it, describe the effect in one line, pick a price band from the price ladder.
2. Assign an Edge Ladder tier (0 cosmetic → 4 hard edge).
3. Say which modes it is sold in (competitive / co-op / PvE).
4. For Tier 3 and 4: answer the six edge-test questions (visible, limited, free path, skill still
   matters, off in finals, fair loss). One "no" = redesign or drop it.
5. Check the hard no's list. Any hit = drop it.
6. Name the phase it ships in and the metric that would make us roll it back.

Output as a table row:

| Product | Effect | Tier | Price | Modes | Edge test | Phase | Rollback signal |
|---|---|---|---|---|---|---|---|

## Implementing a product

- Add the entry to the shared `ProductCatalog` module; never hard-code IDs elsewhere.
- Client only opens the native prompt. Server grants.
- `ProcessReceipt`: look up player, check `PurchaseId` not already processed, apply grant and
  record `PurchaseId` in one `UpdateAsync`, return `PurchaseGranted` only after success;
  otherwise `NotProcessedYet`. Never yield forever; never error out of the callback.
- Game passes: `UserOwnsGamePassAsync` on join (pcall, cached) plus
  `PromptGamePassPurchaseFinished` for live grants.
- Edge limits (per match, per round, finals off) enforced on the server.
- Tier 3+ use fires a visible feed line / icon to everyone.
- Paid random items: blocked unless the owner approved, odds are shown, and the
  `PolicyService` check passes for that player.
- Test with Studio test purchases on a separate data store. Report it as "pending real purchase
  test" until the owner confirms one on a private published build.

## Review checklist

- [ ] Every product is in `ProductCatalog` with a tier
- [ ] No grant path from a client RemoteEvent
- [ ] Receipts idempotent; duplicate `PurchaseId` grants nothing
- [ ] Edge limits enforced server-side; finals disable Tier 3+
- [ ] No prompt on join, mid-round or right after death
- [ ] Free path exists for every Tier 3 item
- [ ] Price shown in UI comes from Roblox product info, not hard-coded
