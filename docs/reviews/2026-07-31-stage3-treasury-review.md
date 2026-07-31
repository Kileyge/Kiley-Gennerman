# Stage 3 review — Scenario 4 EUR receivable build & audit · Treasury sign-off

Kiley — Stage 3 is where the spec meets reality, and this is the strongest part of your project so far. You didn't just build the workbook; you audited it like someone who expected to find a defect — and found the ones that matter.

| Criterion | Result |
|---|---|
| Contract compliance (named ranges, tabs, formula-only) | ✓ 50 / 50 checks |
| Model resolves (0 errors / 216 formulas) | ✓ |
| Audit quality (root-caused and verified with numbers) | ✓ |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **Your audit note is the standout.** You didn't report "65 errors" and move on — you traced them to unanchored defined names, explained the offset-drift mechanism, and caught the genuinely dangerous case: `=S0_in` resolving to the *text* `"USD per EUR"` rather than `1.10`. A wrong number that looks like a number is worse than a `#VALUE!`, because it survives a glance. Finding that is what separates auditing from spot-checking — and it's the habit that makes people trust your models.
- **You fixed the root cause, then re-verified.** Rewriting all 32 defined names to absolute references and re-running `recalc.py` to `0 errors across 216 formulas` is the right sequence: fix the class of bug, then prove it's gone with the tool, not by eyeballing. That's a control, not a hope.
- **Finding 2 shows why a mechanical contract check earns its keep.** The missing `HEDGE_VALUE_PUT` column didn't throw an error — `OVERALL_WINNER` still computed — so a "does it calculate?" glance would have passed it. Your column-by-column check against spec §6 caught a silent structural gap. That's exactly the failure mode audits exist for.
- **You quantified your passes.** `PARITY_GAP = 8.6e-08`, the `$220,000` no-hedge step, the put floor reproducing `K_PUT × FC_AMT − FV_PREMIUM_PUT` — check figures with numbers behind them are what let a reviewer trust the model in minutes.

**To push it further (real-desk nuance)**

- **Your honesty about scope is exactly right — now widen it deliberately.** You noted you're "not claiming the workbook is otherwise flawless, only that these were the areas the spec's validation rules exercise." That's the correct posture. For Stage 4, add a couple of checks *beyond* the spec's own list — e.g. behavior under a zero or negative rate, or a stale-timestamp guard on the live feed — because live data breaks models in ways indicative placeholders never do.
- **`audit_check.py` is a real asset — treat it like one.** A reusable, re-runnable contract check is worth more than any single fix. Carry it into Stage 4 and run it every time you refresh the data, so a bad quote or a shifted column trips the alarm immediately rather than at review.

**Next — Stage 4**

Replace each indicative input with same-date, matching-tenor live quotes and keep the source evidence (provider, timestamp, tenor, access date) in Notes & Assumptions. Then re-run `audit_check.py` and your §7 rules against the live values — the parity gap you proved to `8.6e-08` on back-solved inputs is exactly the figure that will move once real forwards arrive, and that movement is the analysis. Excellent work through the build.

— Treasury

---

### How to work this review — professional workflow

Treat this PR the way an analyst treats feedback from Treasury — a review is a proposal to engage with, not a checklist to rubber-stamp:

1. **Read it yourself first.** Understand each point and form your own view before changing anything. Disagreeing *with a documented reason* is a legitimate, senior response.
2. **Stress-test it with an LLM (pushback pass).** Paste this review and your audit note into your AI assistant and ask it to (a) explain anything you're unsure of more deeply, and (b) argue the *other side* — where might the reviewer be wrong, and what would you give up by making each change. You're building judgment, not just executing edits.
3. **Decide, then draft the changes with the LLM.** For the points you accept, have the AI help implement them — you specify exactly what and why.
4. **Verify — non-negotiable.** Re-run your own checks (`scripts/recalc.py`, `audit_check.py`, the parity tie-out, no error cells) and confirm the numbers before you commit. An AI will hand you a confident wrong edit; verification is what makes the result *yours*.
5. **Close the loop on the PR.** Reply in the thread with what you changed, what you pushed back on and why, then commit and push. Writing down the reasoning is exactly how this works on a real team.

*This is the same human-in-the-loop discipline the whole project is built on: the LLM drafts, you edit and verify, and you own the result.*
