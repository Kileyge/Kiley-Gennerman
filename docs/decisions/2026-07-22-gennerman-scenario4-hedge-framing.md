# EUR Receivable Hedge Framing — U.S. Aerospace Manufacturer

**Created by:** Kiley Gennerman  
**Updated by:** Kiley Gennerman  
**Date Created:** July 22, 2026  
**Date Updated:** July 22, 2026  
**Version:** 0.1  
**LLM Used:** GPT-5

---

## Executive Summary (≤150 words)

Our aerospace business expects to receive €20,000,000 from a European customer in one year. Because the company reports in U.S. dollars, the payment’s value will depend on EUR/USD at collection. A weaker euro would reduce dollar proceeds and could compress contract margin. Hedging converts that uncertain dollar value into a managed financial decision. This memo frames three practical hedge families—forward contracts, money-market hedges, and EUR put options—and outlines the work to compare them. No instrument is recommended for execution yet; later stages will build, price, validate, and recommend the appropriate hedge.

## Background & Objectives

The company has a €20,000,000 receivable due from a European customer in one year. Although the sale is complete, its USD value remains exposed until settlement. If EUR/USD falls, the company receives fewer dollars than planned despite collecting the full euro amount. That can reduce translated revenue, weaken deal economics, and impair cash-flow planning.

The objective is to protect a minimum USD value while assessing each strategy’s cost, flexibility, and retained upside if the euro strengthens.

## Methods

Three hedge families will be evaluated. A forward contract locks in a EUR/USD conversion rate today for the €20 million receivable. It provides certainty with no upfront premium, but is binding and eliminates the benefit of a stronger euro. A money-market hedge borrows euros today, converts and invests the dollars, then uses the receivable to repay the euro borrowing at maturity. It can replicate a forward using cash markets, but requires euro borrowing capacity and adds balance-sheet complexity. An EUR put option establishes a minimum conversion outcome while retaining upside if the euro appreciates. That flexibility is valuable, but the premium is paid regardless of the settlement rate.

## Limitations & Next Steps

This Stage 1 framing uses Scenario 4 facts only; it does not use live market data or establish a final hedge choice. Stage 2 will specify the workbook’s inputs, named ranges, and calculation flow. Stage 3 will generate the workbook and audit it. Stage 4 will add current EUR/USD, forward, interest-rate, and option data. Stage 5 will validate results against an independent LLM run and deliver a hedge recommendation for CFO approval.

## References

1. *Example Scenarios – EUR Receivables (USD Base, 1-Year Horizon)*, Scenario 4 – U.S. Aerospace Manufacturer.
2. *Stage 1 – Executive Memo* assignment instructions.
