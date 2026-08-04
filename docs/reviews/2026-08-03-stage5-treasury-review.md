# Stage 5 review — Scenario 4 EUR receivable LLM analysis & validation · Treasury sign-off

Kiley — this is the strongest finished project in the cohort, and the hand-verification section is the reason. You recomputed the money-market hedge in three visible steps outside Excel and landed on $23,186,684.81 from `€19,553,926.06 → $22,269,966.39 → $23,186,684.81`; I re-derived all of it independently and every figure ties, including the breakeven and all three call outlays. The retrospective is also unusually honest about what the spec left to judgment. So this review spends its time on the one structural weakness in the validation *design* — which is the most interesting thing left to teach you.

| Criterion | Score |
|---|---|
| Independent LLM execution & documentation | 25 / 25 |
| Reconciliation & diagnosis | 25 / 25 |
| Hand verification | 25 / 25 |
| Specification retrospective | 24 / 25 |
| **Total** | **99 / 100** |

**What you did well — and why it matters**

- **The hand verification is the real control.** Reproducing the three money-market steps by hand, with the discount factors written out, is the check that doesn't depend on the workbook or the LLM being right. It's the only step in the entire project where an independent brain touches the arithmetic — and it's why I trust the rest.
- **You future-valued the premium consistently, including on the call.** `FV_PREM = PREM × FC_AMT × DF_USD = $624,698.33`, applied identically to the put and to the payable-reference call. I checked all three call outlays ($22,263,798.33 / $23,402,698.33 / $23,424,698.33) and they're right at every node. Getting the premium onto the same time footing as settlement proceeds is the detail that separates a correct comparison from a plausible one.
- **You refused to let the call contaminate the receivable decision.** Labelling it a payable-reference outlay, stating that lower is better, and excluding it from the winner logic is exactly right — a EUR call does not hedge a EUR receivable. You then carried that into the retrospective as a v2 spec requirement, which is the correct place for it.
- **The breakeven is the sentence a CFO actually needs.** "The forward is superior to the put until EUR/USD exceeds approximately 1.19057" — I get 1.190569, which is spot **+4.54%**. Converting a strategy comparison into a single threshold the CFO can hold against their own view is what turns analysis into advice.
- **The retrospective identifies real spec defects, not cosmetic ones.** The `R_USD`/`R_FC` instrument hierarchy, the missing live-forward-versus-CIP decision tree, the call's ambiguous status, and the absent lab-input template are all genuine gaps that would bite a v2 build. That's a v2 backlog, not a reflection exercise.

**To push it further (real-desk nuance)**

- **Your reconciliation reads $0.00 everywhere because it was guaranteed to.** You gave the independent analysis the same spec and the same market-data memo, both of which specify a CIP-implied forward — so both sides computed the identical forward from identical inputs by identical formula. Fifteen rows of "Reconciled / $0.00" is therefore not evidence that the model is right; it's evidence that two runs of the same recipe agree. In a real model-validation review, a zero-variance reconciliation across every single cell is a **red flag**, not a green one — it usually means the challenger model inherited the champion's assumptions. You come close to seeing this ("this model uses a CIP-implied forward so parity is mechanically expected"), but you frame it as a footnote about parity rather than as a limitation of your own validation design. That reframing is the last step.
- **What an independent check should have varied.** Give the challenger something the champion didn't have — a dealer forward quote instead of the CIP rate, an ACT/365 basis instead of ACT/360, or a 1-year Euribor instead of the deposit facility — and then see whether the *recommendation* survives, not whether the arithmetic matches. A validation earns its name when it can fail.
- **The recommendation rests on a forward that isn't executable.** "The forward locks $23,186,684.81" is a modelled number, not a tradeable one — `F0_in` is CIP-implied, and a dealer quote would embed a cross-currency basis and a bid/offer spread. The honest formulation is that the forward locks approximately that amount, subject to the quoted spread, and that the forward-versus-put decision could flip if the quoted forward comes in materially below parity. Your breakeven at 1.19057 is the tool for that: state how far the quoted forward would have to fall before the put becomes the better structure.
- **Quantify the proxy risk you already identified.** You correctly note the ECB deposit facility is an *overnight* policy rate doing duty as a 1-year discount rate. Put a number on it: a **50bp** shift in the EUR leg moves `F0_in` from 1.159334 to roughly 1.153616 or 1.165109 — about **$115,000** of locked proceeds either way. That's larger than most of the differences you reconciled to $0.00, which is precisely the point: the risk lives in the inputs, not in the arithmetic.
- **One point held back on the retrospective.** Everything you list is a defect in the *specification*. Nothing in it is a defect in your *validation method* — and after this exercise you have a clear one to name (the point above). A retrospective that examines the reviewer's own process, not only the artifact under review, is what a senior analyst writes.

**Closing**

You finished the whole arc — framing, spec, build, audit, live data, independent validation — with the numbers tying at every handoff and the open items carried forward honestly rather than quietly dropped. The habit worth keeping is the one you showed in the hand verification: when it matters, compute it yourself. The habit worth adding is asking, before you run a check, *"what result would tell me I'm wrong?"* — and making sure the check is capable of producing it.

— Treasury

---

### How to work this review — professional workflow

Treat this PR the way an analyst treats feedback from Treasury — a review is a proposal to engage with, not a checklist to rubber-stamp:

1. **Read it yourself first.** Understand each point and form your own view before changing anything. Disagreeing *with a documented reason* is a legitimate, senior response.
2. **Stress-test it with an LLM (pushback pass).** Paste this review and your validation document into your AI assistant and ask it to (a) explain anything you're unsure of more deeply, and (b) argue the *other side* — where might the reviewer be wrong, and what would you give up by making each change. You're building judgment, not just executing edits.
3. **Decide, then draft the changes with the LLM.** For the points you accept, have the AI help implement them — you specify exactly what and why.
4. **Verify — non-negotiable.** Re-run your own checks and confirm the numbers before you commit. An AI will hand you a confident wrong edit; verification is what makes the result *yours*.
5. **Close the loop on the PR.** Reply in the thread with what you changed, what you pushed back on and why, then commit and push. Writing down the reasoning is exactly how this works on a real team.

*This is the same human-in-the-loop discipline the whole project is built on: the LLM drafts, you edit and verify, and you own the result.*
