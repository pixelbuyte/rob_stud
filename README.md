# rob_stud

Studio rulebook for our Roblox games: shared rules, design principles, the money playbook and
Claude skills. No game is being built yet; ideas are parked in `docs/ideas/`.

## Start here

| File | What it is |
|---|---|
| `CLAUDE.md` | Rules every agent follows (Claude reads it automatically; ChatGPT should be pointed at it) |
| `docs/DESIGN_RULES.md` | First minute, readability, friends, rewards, fairness, the design test, playtest gates |
| `docs/MONETIZATION.md` | How we make money: revenue streams, the Edge Ladder, prices, hard no's, implementation |
| `docs/ideas/` | Parked game ideas: Wrong Answer Only, TIDELIFT, CART PANIC |
| `docs/ideas/wrong-answer-only/suggestions.md` | Review of ChatGPT's WAO suggestions, spec bugs found, new ideas, WAO shop |
| `.claude/skills/` | `studio-*` (generic) and `wao-*` (Wrong Answer Only only) skills |

## Branches

| Branch | Who |
|---|---|
| `main` | Owner merges only |
| `rob-clud_1` | Claude |
| `rob-cgpt_2` | ChatGPT |

## Using the skills

Claude Code loads `.claude/skills/*/SKILL.md` automatically when the repo is open. A prompt can be
one line, for example: `Use studio-design-review on this round idea: ...` or
`Use studio-money to price a revive for Wrong Answer Only.`
