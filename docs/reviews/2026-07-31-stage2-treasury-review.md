# Stage 2 review — Scenario 4 EUR receivable · Treasury sign-off

Kiley — I read your specification the way Treasury actually will: the spec is the contract the Stage 3 build has to honor. On the test that matters — could an analyst who has never seen your file rebuild the model from this document alone — it holds up completely.

| Criterion | Score |
|---|---|
| Named-range contract & tab architecture | 30 / 30 |
| Calculation flow | 30 / 30 |
| Validation & sensitivity plan | 20 / 20 |
| Reproducibility & prompt log | 20 / 20 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **You pinned the quote convention in the spec** (USD per EUR, higher = EUR appreciation). Most analysts leave that implicit, and it is the single most common source of a sign-flipped hedge model. Stating it up front is precisely why your build came back correct instead of inverted — the AI had nothing to guess.
- **Your parity check is a live tolerance, not a hope.** `ABS(PARITY_GAP) <= $1` on a $20M notional is an actual pass/fail a reviewer can trust. "Forward and MM should be close" is not auditable; yours is — and defining `BASIS_USD`/`BASIS_FC` as non-editable constants (not magic numbers buried in formulas) is the kind of discipline that keeps a model maintainable.
- **The named-range contract is complete and honest about provenance.** Every input carries a unit, a placeholder, and a Stage-4 source; keeping `S_T` as the row-level ending spot rather than a global input shows you understand what varies and what's fixed.

**To push it further (real-desk nuance)**

- **When the live forward breaks parity, that gap is information — exploit it, don't hide it.** A quoted 1-year forward carries a bid/ask and a cross-currency basis, so `F0_in` won't exactly equal the MM-implied rate. A small residual is basis; a *large* one means the forward and money-market hedges lock materially different USD, so flag the advantaged leg (or a genuine arbitrage from a mispriced forward) and pick it. Set your live-data tolerance in bps now so Stage 4 doesn't absorb a real signal into "rounding."
- **Your put is struck at-the-money** (`K_PUT = S0_in = 1.10`) — the most expensive floor. On a live desk you'd usually show the CFO one or two out-of-the-money strikes too, so the premium/protection trade-off is visible rather than a single point. One extra column at Stage 4 does it.
- **"OVERALL_WINNER" is hindsight.** It's the best outcome *given* a realized `S_T`, but the decision is made under uncertainty. Frame the Stage 5 recommendation on risk tolerance (the forward wins only if EUR falls; the put only pays for its premium on a large move), not on which column happened to win.

**Next — Stage 3**

Hand this spec to an AI, let it build the workbook, then audit it hard against your own §7: every calculated cell a formula referencing your named ranges, all three families live, the sensitivity table and chart, and a build note. Every weakness in a spec becomes a defect in the build — yours is tight, so hold the build to it.

— Treasury

---

### How to work this review — professional workflow

Treat this PR the way an analyst treats feedback from Treasury — a review is a proposal to engage with, not a checklist to rubber-stamp:

1. **Read it yourself first.** Understand each point and form your own view before changing anything. Disagreeing *with a documented reason* is a legitimate, senior response.
2. **Stress-test it with an LLM (pushback pass).** Paste this review and your spec into your AI assistant and ask it to (a) explain anything you're unsure of more deeply, and (b) argue the *other side* — where might the reviewer be wrong, and what would you give up by making each change. You're building judgment, not just executing edits.
3. **Decide, then draft the changes with the LLM.** For the points you accept, have the AI help implement them — you specify exactly what and why. Your spec is the prompt; precise in, correct out.
4. **Verify — non-negotiable.** Re-run your own checks (`scripts/recalc.py`, the parity tie-out, sensitivity continuity, no error cells) and confirm the numbers before you commit. An AI will hand you a confident wrong edit; verification is what makes the result *yours*.
5. **Close the loop on the PR.** Reply in the thread with what you changed, what you pushed back on and why, then commit and push. Writing down the reasoning is exactly how this works on a real team.

*This is the same human-in-the-loop discipline the whole project is built on: the LLM drafts, you edit and verify, and you own the result.*
