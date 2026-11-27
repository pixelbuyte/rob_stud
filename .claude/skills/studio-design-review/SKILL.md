---
name: studio-design-review
description: Use when designing, pitching or judging any new mechanic, round, mode, feature or game idea in this repo - runs the design test, fairness and friends checks, and returns a go / change / reject verdict.
---

# Design review

Read `docs/DESIGN_RULES.md` first. If the idea touches anything players pay for, also run the
`studio-money` skill.

## Steps

1. Restate the idea in one sentence a first-timer would understand. If you cannot, say so.
2. Answer the 8 questions in section 7 of `DESIGN_RULES.md` (one sentence, moment, lesson,
   reveal, friends, solo, fairness, cost).
3. Check it against the parked ideas in `docs/ideas/` so we reuse what already works and do not
   design two games that clash.
4. List the riskiest assumption and the cheapest playtest that would prove or kill it.

## Output (grid, always this shape)

| Check | Answer | Pass? |
|---|---|---|
| One sentence | ... | Yes/No |
| Moment | ... | |
| Lesson | ... | |
| Reveal | ... | |
| Friends | ... | |
| Solo | ... | |
| Fairness | ... | |
| Cost | ... | |

Then: **Verdict:** Go / Change (say what) / Reject (say why). **Riskiest assumption:** ...
**Cheapest test:** ...

Do not start building during a design review.
