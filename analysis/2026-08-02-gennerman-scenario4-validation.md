# Stage 5 LLM Analysis & Validation — Scenario 4 EUR Receivable

| Item | Detail |
|---|---|
| **Exposure** | €20,000,000 receivable due in 365 days |
| **Functional currency** | USD |
| **Documents supplied to independent analysis** | Stage 2 technical specification and Stage 4 market-data memo only |
| **Comparison basis** | Populated Stage 4 workbook |
| **Validation date** | 2026-08-02 |

## Part 1 — Independent LLM execution record

### Exact prompt

> Using only the attached FX hedge model technical specification and market-data memo, independently calculate the EUR receivable hedge outcomes. Evaluate no hedge, forward, money-market hedge, EUR put option, and the EUR call payable-reference outcome at the low, base, and high settlement-spot scenarios. Explain assumptions, identify ambiguities, and recommend a hedge for the CFO. Do not rely on a workbook or any prior result.

### Raw independent output

The analysis identifies a €20.0 million EUR receivable and a USD-per-EUR quote convention. With `S0_in = 1.1389`, `R_USD = 4.06%`, `R_FC = 2.25%`, and `T_DAYS = 365`, the CIP forward is 1.1593342407 USD/EUR. A EUR forward sale therefore locks USD proceeds of $23,186,684.81. The money-market hedge produces the same amount: borrow the present value of the EUR receivable, convert at spot, and invest USD to maturity.

The EUR put has a 1.1400 strike and a $0.0300-per-EUR premium. Its future-valued premium is $624,698.33. At settlement rates below 1.1400, it produces $22,175,301.67; above strike it participates in EUR appreciation but remains reduced by the premium. The forward is therefore superior to the put until EUR/USD exceeds approximately 1.19057. Given the stated objective of protecting a minimum USD value and the equivalent proceeds of the money-market hedge, the preferred strategy is the EUR forward sale because it provides the highest certain proceeds without the borrowing and investment mechanics of the money-market alternative.

The documents leave three points for Treasury judgment: the EUR deposit facility is an overnight reference-rate proxy rather than a one-year funding quote; the input observations are not all from the identical market date; and option premiums are scenario assumptions rather than executable dealer quotes.

## Part 2 — Comparison with populated workbook

All amounts are settlement-date USD. The call is shown as a **payable-reference outlay**, so lower is better; it is not included in the receivable winner decision.

| Settlement scenario | Strategy | Independent LLM | Workbook | Difference | Diagnosis |
|---|---|---:|---:|---:|---|
| `S_T = 1.081955` (−5%) | No hedge | $21,639,100.00 | $21,639,100.00 | $0.00 | Reconciled |
|  | Forward | $23,186,684.81 | $23,186,684.81 | $0.00 | Reconciled |
|  | Money market | $23,186,684.81 | $23,186,684.81 | $0.00 | Reconciled |
|  | Put | $22,175,301.67 | $22,175,301.67 | $0.00 | Reconciled |
|  | Call payable-reference outlay | $22,263,798.33 | $22,263,798.33 | $0.00 | Reconciled |
| `S_T = 1.138900` (base) | No hedge | $22,778,000.00 | $22,778,000.00 | $0.00 | Reconciled |
|  | Forward | $23,186,684.81 | $23,186,684.81 | $0.00 | Reconciled |
|  | Money market | $23,186,684.81 | $23,186,684.81 | $0.00 | Reconciled |
|  | Put | $22,175,301.67 | $22,175,301.67 | $0.00 | Reconciled |
|  | Call payable-reference outlay | $23,402,698.33 | $23,402,698.33 | $0.00 | Reconciled |
| `S_T = 1.195845` (+5%) | No hedge | $23,916,900.00 | $23,916,900.00 | $0.00 | Reconciled |
|  | Forward | $23,186,684.81 | $23,186,684.81 | $0.00 | Reconciled |
|  | Money market | $23,186,684.81 | $23,186,684.81 | $0.00 | Reconciled |
|  | Put | $23,292,201.67 | $23,292,201.67 | $0.00 | Reconciled |
|  | Call payable-reference outlay | $23,424,698.33 | $23,424,698.33 | $0.00 | Reconciled |

### Reconciliation conclusion

There are no numerical discrepancies. The independent output and workbook use the same ACT/360 simple-interest convention, CIP-implied `F0_in`, and future-valued USD premium. The independent output correctly treats the call as a payable-reference calculation, not as a hedge candidate for this EUR receivable.

## Part 2 — Hand verification

All calculations below use the populated named inputs and are performed outside Excel.

### 1. Forward proceeds

`USD_FWD = FC_AMT × F0_in`  
`= €20,000,000 × 1.159334240689819`  
`= $23,186,684.81`

### 2. Money-market hedge — all three steps

`DF_FC = 1 + R_FC × T_DAYS/360`  
`= 1 + 0.0225 × 365/360 = 1.0228125`

`EUR_BORROW = FC_AMT / DF_FC`  
`= €20,000,000 / 1.0228125 = €19,553,926.06`

`USD_TODAY = EUR_BORROW × S0_in`  
`= €19,553,926.06 × 1.1389 = $22,269,966.39`

`DF_USD = 1 + R_USD × T_DAYS/360`  
`= 1 + 0.0406 × 365/360 = 1.0411638889`

`USD_MM = USD_TODAY × DF_USD`  
`= $22,269,966.39 × 1.0411638889 = $23,186,684.81`

The forward and money-market outcomes match exactly because the forward is CIP-implied from the same inputs.

### 3. Put outcome at the −5% settlement spot

`S_T = 0.95 × S0_in = 0.95 × 1.1389 = 1.081955`

`FV_PREM_PUT = PREM_PUT × FC_AMT × DF_USD`  
`= $0.0300 × €20,000,000 × 1.0411638889 = $624,698.33`

`USD_PUT(S_T) = MAX(S_T, K_PUT) × FC_AMT − FV_PREM_PUT`  
`= MAX(1.081955, 1.1400) × €20,000,000 − $624,698.33`  
`= $22,800,000.00 − $624,698.33 = $22,175,301.67`

This agrees with both the independent result and the workbook’s put floor.

## Part 4 — Specification retrospective

The specification was strong enough to reproduce every workbook result, but it still required analytical judgment in several places. First, it calls `R_USD` and `R_FC` interest rates without distinguishing a Treasury yield, an overnight policy rate, a deposit quote, and an actual corporate funding rate. The Stage 4 memo made defensible proxy choices, but a v2 specification should state the preferred instrument hierarchy, whether yields should be converted from an investment basis, and what to do when the two legs have different data dates.

Second, the specification treats the forward as either a live quote or a parity result but does not require a clear **live-forward-versus-CIP** decision tree. That distinction matters: this model uses a CIP-implied forward so parity is mechanically expected. A v2 should require a separate “market forward variance” output when a dealer forward becomes available.

Third, the call is pedagogically useful but economically a payable-reference strategy for this receivable. The original specification describes it, yet a fresh analyst could mistakenly include it in the receivable winner calculation. A v2 should state prominently that the receivable recommendation compares no hedge, forward, money market, and put only; the call is presented in its own payable-reference schedule.

Finally, the Stage 4 memo documents that the course lab was unavailable in this environment. The documents should include a formal lab-input/output template so a student can insert the lab’s result later rather than leaving the final external cross-check narrative-only.
