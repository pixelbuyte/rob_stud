> **Parked reference, not active instructions.** Kept so the idea is not lost. Do not build from this until the owner makes this game active in `CLAUDE.md`. Studio-wide rules in `/CLAUDE.md`, `docs/DESIGN_RULES.md` and `docs/MONETIZATION.md` win over anything here.

# CART PANIC — repository instructions

You are the implementation engineer. Read README and docs/GAME_DESIGN.md, MAP_SPEC.md, BUILD_PLAN.md before edits. Treat numeric tuning as hypotheses. Do not change the core game direction without explaining the tradeoff. Keep code modular, typed Luau, server authoritative and mobile-friendly. Use minimal dependencies. The map builder is canonical; keep standalone Studio command in sync. Do not claim a successful Studio test unless actual logs/results are supplied. Use `.claude/skills` for gameplay, map and QA workflows.

For each task: inspect existing files, state scope and acceptance tests, implement the smallest end-to-end feature, review for abuse and regression, run available checks, list exact changed files and remaining manual Studio actions. Stop at a meaningful playable checkpoint, not a giant speculative architecture. Ask before publishing, spending, adding dependency, accessing external credentials, or destroying user work. No free models with unreviewed scripts, unlicensed assets, manipulative reward systems, or fake test results.
