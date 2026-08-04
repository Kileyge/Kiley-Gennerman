# Stage 4 review — Scenario 4 EUR receivable market data & population · Treasury sign-off

Kiley — I re-derived every number in this memo from your stated inputs before writing a word of it, and they all tie: the CIP forward to ten decimals, the forward and money-market proceeds to the cent, the no-hedge value, and the put net of a future-valued premium. That's the standard. What follows is almost entirely about the difference between *arithmetic that checks* and *market data that would survive a desk review* — which is the actual lesson of this stage.

| Criterion | Score |
|---|---|
| Provenance & sourcing discipline | 35 / 35 |
| Population accuracy & model integrity | 35 / 35 |
| Validation & cross-check | 20 / 20 |
| Documentation quality | 10 / 10 |
| **Total** | **100 / 100** |

**What you did well — and why it matters**

- **The provenance table is the real thing.** Value, unit, market-data date, *separate* retrieval date with timezone, source link, and a rationale sentence for every one of the ten ranges. Separating "the date the market printed this" from "the date I fetched it" is a distinction most professionals skip, and it's the one that lets someone reconstruct your work six months later.
- **You labelled your proxies as proxies, in writing.** Calling `R_USD` and `R_FC` "reference-rate proxies, not dealer funding quotes," and stating outright that the ECB deposit facility stands in because a matching-tenor EUR government yield wasn't reliably available — that is exactly the disclosure that keeps a model honest. The failure mode this prevents is a reader treating a policy rate as a funding rate.
- **You flagged the forward as CIP-implied rather than passing it off as a quote.** `F0_in = 1.1593342407` is computed, and you say so, and you explain why (no free executable 1-year forward). You also quantified the move from the Stage 3 indicative rate — +0.0320064432, about 2.84% — and attributed it to the higher live spot and a different rate differential. I verified that delta; it's right.
- **The failed cross-check is reported as a failure.** The lab was unreachable, you said so, you substituted an independent hand recomputation, and you left a written instruction for what to do if the lab comes back. Reporting a control you *couldn't* run, rather than quietly dropping it, is the habit that matters most on this stage.
- **Every figure reconciles.** Forward and money-market both $23,186,684.81; no-hedge $22,778,000.00; put $22,175,301.67 after a premium future-valued at `DF_USD`. Future-valuing the premium so it sits on the same footing as settlement-date proceeds is the correct treatment and one many students get wrong by netting it at spot.

**To push it further (real-desk nuance)**

- **Your inputs are observed as much as seven weeks apart.** Spot is dated 2026-07-27, `R_USD` 2026-07-08, `R_FC` effective 2026-06-17. Covered interest parity is a *point-in-time* arbitrage relation — it holds between rates observable simultaneously. Building a CIP forward from legs sampled across seven weeks means the forward isn't the no-arbitrage rate for any single moment. You note the staggering in your Stage 5 retrospective; the place it belongs is here, quantified. A rough sensitivity makes the point: a **50bp** error in the EUR leg alone moves the locked proceeds by roughly **$115,000**.
- **A Treasury CMT yield is not a rate you can borrow or lend at.** The money-market hedge requires you to actually *borrow EUR* and *invest USD*. Your model uses one rate per currency, which is why forward and money-market land on the identical $23,186,684.81. In practice you borrow above the benchmark and deposit below it, and that bid/offer wedge makes the money-market hedge strictly worse than the forward almost every time. The exact tie is an artifact of single-rate modelling, not a finding — worth saying so explicitly.
- **There's a day-count basis mismatch hiding in `R_USD`.** The 1-year CMT is quoted on a bond-equivalent/investment basis; you're feeding it into an ACT/360 simple-interest formula. It's a small effect at these levels, but converting to a money-market basis before use is the technically correct step, and naming the choice is better than letting it pass silently.
- **The flat 0.0300 premium on both options violates put-call parity — by a lot.** With `F0_in = 1.1593` and both strikes at 1.1400, parity requires `C − P = (F − K)/DF_USD ≈ 0.0186` USD per EUR: the call should cost about 1.86 cents *more* than the put. Pricing both at 0.0300 embeds a **$371,000** inconsistency on a €20M notional. You inherited these as scenario assumptions and flagged them as non-executable, which is the right disclosure — but the consequence is worth stating: the call schedule can't be read as a market-consistent price, only as a teaching illustration.
- **`K_PUT = K_CALL = 1.1400` is a modelling convenience worth naming.** Rounding both strikes off the 1.1389 spot is reasonable, but a put and a call at the same strike on the same underlying are two legs of a parity relation, not two independent quotes — which is precisely why the premium inconsistency above shows up so sharply.

**Next — Stage 5**

Hand the spec and this memo to an independent LLM, have it reproduce the analysis cold, and reconcile it line by line against the populated workbook. One thing to watch, given how clean this stage is: if you supply the same CIP convention to the independent run, it will agree with you by construction — and a reconciliation that agrees everywhere hasn't tested anything. Design at least one comparison that could actually come out different.

— Treasury

---

### How to work this review — professional workflow

Treat this PR the way an analyst treats feedback from Treasury — a review is a proposal to engage with, not a checklist to rubber-stamp:

1. **Read it yourself first.** Understand each point and form your own view before changing anything. Disagreeing *with a documented reason* is a legitimate, senior response.
2. **Stress-test it with an LLM (pushback pass).** Paste this review and your market-data memo into your AI assistant and ask it to (a) explain anything you're unsure of more deeply, and (b) argue the *other side* — where might the reviewer be wrong, and what would you give up by making each change. You're building judgment, not just executing edits.
3. **Decide, then draft the changes with the LLM.** For the points you accept, have the AI help implement them — you specify exactly what and why.
4. **Verify — non-negotiable.** Re-run your own checks (the parity tie-out, the formula-error scan, no error cells) and confirm the numbers before you commit. An AI will hand you a confident wrong edit; verification is what makes the result *yours*.
5. **Close the loop on the PR.** Reply in the thread with what you changed, what you pushed back on and why, then commit and push. Writing down the reasoning is exactly how this works on a real team.

*This is the same human-in-the-loop discipline the whole project is built on: the LLM drafts, you edit and verify, and you own the result.*
