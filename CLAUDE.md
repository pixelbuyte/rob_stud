# Studio rules — read this first

This repo is a small Roblox studio workspace. It holds the **rules, design principles, money
playbook and Claude skills** that every game we make follows, plus a parking lot of game ideas.
Right now there is **no active game**: do not scaffold or build a game unless the owner names one
as active in the table below.

| Game idea | Status | Reference |
|---|---|---|
| Wrong Answer Only (2–12 player elimination) | Parked, keep in mind | `docs/ideas/wrong-answer-only/` and `.claude/skills/wao-*` |
| TIDELIFT (1–4 player co-op crane rescue) | Parked, keep in mind | `docs/ideas/tidelift/` |
| CART PANIC (1–4 player co-op supermarket raid) | Parked, keep in mind | `docs/ideas/cart-panic/` |

When a game becomes active, it gets a folder `games/<name>/` with its own `GAME.md` that declares
the build pipeline (Rojo or Studio MCP), the map source of truth, the current milestone and the
palette. Game-specific rules and skills (like `wao-*`) override the generic `studio-*` ones for
that game only. Never edit another game's folder while working on one.

## Branches

| Branch | Owner | Rule |
|---|---|---|
| `main` | Project owner | Only the owner merges into it. |
| `rob-clud_1` | Claude (Claude Code) | Claude's work. |
| `rob-cgpt_2` | ChatGPT | ChatGPT's work. Claude never pushes here after creating it. |

Both agents follow the same rules in this file so their output can be compared side by side.

## Working rules

1. Read this file, `docs/DESIGN_RULES.md`, `docs/MONETIZATION.md` and the active game's `GAME.md`
   before changing anything. Inspect the repo tree; preserve working code.
2. One milestone at a time. Build the smallest playable change, then stop for a playtest.
3. Treat every tuning number (timers, prices, speeds) as a starting hypothesis kept in one config
   module. Record playtest-driven changes in the game's docs.
4. Report what you actually ran separately from what still needs Studio, a second client or a
   real phone. Never claim a Studio or device test ran unless the owner supplied the result.
5. Ask before: publishing, spending Robux or money, adding a dependency, using credentials,
   deleting the owner's work, or changing a game's core direction.
6. No free models with unreviewed scripts, no unlicensed assets, no AI-generated thumbnails or
   fake footage. Record asset IDs and provenance.

## Code rules (short form; full version in `studio-luau` skill)

- Luau, `--!strict` where practical. Services fetched once at the top.
- **Server owns all game state, deaths, rewards, purchases and saves.** Clients send intent only
  (e.g. `PrimaryAction(targetId)`) and draw replicated state.
- Validate every remote: type, sender, round ID, phase, alive state, range, cooldown, legality.
  Rate-limit per player. Duplicate requests must be harmless.
- `task.wait/spawn/delay` only. Every loop has an exit tied to round state. Track and disconnect
  every connection when a round ends.
- DataStores: `pcall`, bounded retries with backoff, `UpdateAsync` for anything that grants,
  save on leave and in `BindToClose`, separate Studio data. A save failure never stops the loop.
- Purchases: `ProcessReceipt` is idempotent (record `PurchaseId`), grants happen on the server,
  and `PurchaseGranted` is returned only after the grant is saved.

## Art rules (short form; full version in `studio-art` skill)

- Colours from a per-game `Theme` module (`Color3.fromRGB`), never `BrickColor` names.
- Matte materials (Concrete, Slate, Metal, SmoothPlastic). Neon only for one meaning, never floors.
- One accent colour for UI; signal colours only where they mean something.
- No emoji in signs or UI. Builder Sans (Bold for headings, Medium for body).
- No gradients, drop shadows, rainbow strokes, screen shake. Short Quad Out tweens.
- Every state is readable without colour and without sound (shape, text, icon).

## Money rules (short form; full version in `docs/MONETIZATION.md`)

- Every product is classified on the **Edge Ladder** (Tier 0 cosmetic → Tier 4 hard edge) and
  must pass the edge test for its game mode before it ships.
- All products live in one shared `ProductCatalog` module: id, tier, price, effect, limits.
- Paid random items only with shown odds and a `PolicyService` check. No fake scarcity, no
  purchase prompts on join or right after a death, no paywalled core loop, no paid invites.
- Monetization ships in phases gated by retention, not on day one.

## Skills

| Skill | Use when |
|---|---|
| `studio-design-review` | Designing or judging any new mechanic, round, mode or feature |
| `studio-build` | Creating map geometry or project structure (Rojo or Studio MCP) |
| `studio-luau` | Writing or reviewing any Luau |
| `studio-art` | Building parts, lighting, signs or UI |
| `studio-money` | Designing, pricing or implementing anything players pay for |
| `studio-playtest` | Testing, fixing Output errors, writing test checklists |
| `wao-*` | Only when Wrong Answer Only is the active game |

## Which model to use

Sonnet for ordinary building, UI, tuning and bug fixes. Opus for work that crosses several
services (round cleanup, disconnect races, replication bugs, save/migration and purchase
handling) and for reviewing a complex diff. Haiku for scoped chores with obvious output (asset
tables, QA scripts, log summaries), reviewed before merge.

## Done report (always this shape)

```
Done: <one line>
Changed: <files>
Checks run: <exact commands and results>
Still needs Studio / device: <numbered manual steps>
Risks: <anything uncertain, or "none">
```
