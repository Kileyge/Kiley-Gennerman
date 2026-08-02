# CFO Recommendation — Hedge the €20.0M EUR Receivable with a Forward

**To:** Chief Financial Officer  
**From:** Kiley Gennerman, Treasury Analyst  
**Date:** 2026-08-02  
**Subject:** One-year EUR/USD hedge recommendation for the European aerospace receivable

## Recommendation

Sell the expected **€20,000,000** receivable forward for settlement in one year. Using the populated Stage 4 inputs, the forward locks **$23.187 million** of USD proceeds. The money-market hedge produces the same economic result but requires borrowing EUR, converting to USD, and investing the proceeds; it therefore adds operational and balance-sheet complexity without improving the locked USD outcome. A put option preserves upside, but its $0.0300-per-EUR premium produces a lower protected floor of **$22.175 million** and does not outperform the forward unless EUR/USD settles above approximately **1.1906**.

## Exposure and market context

The company will collect €20.0 million from a European customer in 365 days while reporting in USD. A fall in EUR/USD lowers translated cash proceeds and can reduce contract margin after the operating sale is complete. The Stage 4 model uses a EUR/USD reference spot of **1.1389 USD/EUR**, a 4.06% U.S. one-year Treasury benchmark, and a 2.25% ECB deposit-facility EUR proxy. With these inputs, the covered-interest-parity forward is **1.1593342 USD/EUR**. The data memo documents the exact sources, dates, and proxy choices.

## Hedge outcomes

| Strategy | Base-case settlement-date USD outcome | Key feature |
|---|---:|---|
| No hedge | $22.778 million | Full EUR/USD exposure remains. |
| Forward | $23.187 million | Highest certain USD proceeds; no upfront option premium. |
| Money market | $23.187 million | Economic forward replication; requires borrowing and investing. |
| EUR put | $22.175 million floor | Protects against EUR depreciation while retaining upside, net of premium. |

The forward/money-market parity check passes within the model’s $1 tolerance. The equality is expected because the model calculates the forward through CIP using the same rates and ACT/360 convention.

## Sensitivity interpretation

At a 5% EUR depreciation (`S_T = 1.081955`), no hedge produces **$21.639 million**. The forward and money-market hedges remain at **$23.187 million**, protecting $1.548 million of incremental USD proceeds versus no hedge. The put protects its net floor at **$22.175 million**, but its premium causes it to trail the forward by about $1.011 million.

At the current base spot, the unhedged value is **$22.778 million**, still below the forward by about $409 thousand. At a 5% EUR appreciation (`S_T = 1.195845`), no hedge reaches **$23.917 million** and the put reaches **$23.292 million**, exceeding the forward by about $105 thousand. That upside is real but requires EUR/USD to exceed the put’s approximately **1.1906** forward break-even rate; the forward is the superior choice across the model’s lower and central scenarios.

## Executive justification

The forward is the cleanest match for the business objective: protect a minimum USD value and support dependable margin and cash-flow planning. It locks a known USD amount without an upfront premium, avoids the liquidity and balance-sheet mechanics of a money-market hedge, and is easier to communicate to operating leadership.

The put is appropriate only if Treasury is willing to pay roughly $625 thousand in future-valued premium to retain EUR upside. Given the stated emphasis on protection and budget certainty, that optionality is not compelling at the current premium assumption. Treasury should obtain an executable bank forward quote before trade execution and confirm counterparty, credit, collateral, and hedge-accounting treatment under company policy. The Stage 4 rate inputs are documented reference-rate proxies rather than executable funding quotes; the recommended instrument should be priced by Treasury’s approved counterparty at execution.

## Decision requested

Authorize Treasury to solicit an executable one-year EUR/USD forward sale for €20.0 million, validate the quoted forward against the model’s 1.1593342 CIP reference, and execute subject to the company’s counterparty and hedge-accounting policy.
