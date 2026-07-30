# Scenario 4 — EUR Receivable FX Hedge Model · Technical Specification

| Field | Value |
|---|---|
| **Created / updated by** | Kiley Gennerman |
| **Date** | 2026-07-30 |
| **Version** | 1.0 |
| **Role / audience** | Treasury Analyst / CFO and Director of Treasury |
| **Status** | Stage 2 design; indicative market inputs only |

## 1. Problem statement

A U.S. aerospace manufacturer expects a **€20,000,000 receivable** from a European customer in one year (365 days). The company’s functional currency is USD; therefore, a decline in the USD-per-EUR exchange rate before collection reduces the USD value of the completed sale and can compress contract margin and impair cash-flow planning. The workbook will compare an unhedged position with a EUR forward sale, a money-market hedge, and a EUR put option. Its purpose is to quantify a protected USD outcome, the option’s retained EUR-upside, and the cost of that protection for a treasury decision.

All values marked **indicative** are placeholders from the Scenario 4 design exercise and must be replaced with quoted market data in Stage 4. No trade is authorized by this specification.

## 2. Inputs — named-range contract

All editable inputs reside on the **Inputs** tab and are workbook-level named ranges. Rates use the quote convention **USD per EUR**: a higher rate means EUR appreciation. Stage 4 records the provider, quote timestamp, tenor, and access date in Notes & Assumptions.

| Named range | Description | Indicative placeholder | Unit | Stage-4 source / replacement |
|---|---|---:|---|---|
| `FC_AMT` | Contractual EUR receivable | 20,000,000 | EUR | Executed customer contract / Treasury |
| `S0_in` | EUR/USD spot at model inception | 1.1000 | USD per EUR | Live EUR/USD mid-rate, timestamped market-data source |
| `F0_in` | EUR/USD forward rate matching settlement | 1.0750 | USD per EUR | Live 1-year bank or market-data forward quote |
| `R_USD` | USD simple annual borrowing/investment rate | 4.50% | Annual % | Matching-tenor USD funding/deposit curve |
| `R_FC` | EUR simple annual borrowing/investment rate | 2.00% | Annual % | Matching-tenor EUR funding/deposit curve |
| `K_PUT` | EUR put strike | 1.1000 | USD per EUR | 1-year EUR put dealer/option-data quote; strike selected by Treasury |
| `K_CALL` | EUR call strike (participation reference only) | 1.1000 | USD per EUR | Matching 1-year call quote if the call view is presented |
| `PREM_PUT` | EUR put premium per EUR | 0.0300 | USD per EUR | Executable dealer premium, net of stated convention |
| `PREM_CALL` | EUR call premium per EUR | 0.0300 | USD per EUR | Executable dealer premium if call view is presented |
| `T_DAYS` | Days from inception to collection | 365 | Days | Contract settlement date less valuation date |

The final workbook also defines constants `BASIS_USD = 360` and `BASIS_FC = 360`; they are non-editable assumptions under the stated ACT/360 simple-interest convention. `S_T` is the row-level ending spot in the Sensitivity table, not a global input.

## 3. Workbook architecture

| Tab | Purpose |
|---|---|
| **Cover** | Scenario identity, author, version, purpose, and Stage-4 market-data/source documentation. |
| **Legend-Key** | Color convention: blue/white editable inputs, gray formula outputs, green check/pass cells, and yellow review items. |
| **Inputs** | Sole editable input panel, named ranges, units, source, timestamp, and indicative/live-data status. |
| **Forward** | EUR forward-sale calculation and locked USD-proceeds result. |
| **Money Market** | EUR borrowing, USD conversion/investment, and parity check. |
| **Options** | Put-premium calculation and EUR put payoff/proceeds logic; call formulas are documented as a reference, not a recommended hedge for this receivable. |
| **Sensitivity** | Eleven-scenario comparison table, winner label, and one USD-proceeds line chart. |
| **Notes & Assumptions** | Quote conventions, excluded costs, calculation conventions, data sources, and audit notes. |

## 4. Assumptions and constraints

- The exposure is a EUR **receivable**. All strategy outputs are settlement-date USD proceeds; higher proceeds are better.
- Interest is simple interest on ACT/360 for both modeled legs: `DF_USD = 1 + R_USD × T_DAYS / BASIS_USD` and `DF_FC = 1 + R_FC × T_DAYS / BASIS_FC`. Stage 4 must replace this convention if the chosen instruments use another day count.
- The forward and money-market results should agree within rounding under covered interest-rate parity. A material difference is an input-quality or convention investigation, not silently rounded away.
- The put premium is paid in USD at inception, quoted per EUR, and carried to settlement at the USD rate. No option multiplier is assumed.
- Counterparty risk, credit valuation adjustment, collateral, taxes, accounting designation/effectiveness, bid-ask spreads, commissions, and transaction costs are excluded from the base case. Stage 4 may add them as explicit sensitivities.
- The model is deterministic: it applies no probabilities, volatility forecast, or exercise/assignment frictions. It compares settlement outcomes for specified `S_T` values only.

## 5. Calculation flow

Every calculated cell must be a formula using named ranges or a row’s explicit `S_T`; no calculation may depend on an unexplained cell address.

### 5.1 Forward hedge

The company sells the expected EUR receipt forward at inception. The locked settlement proceeds are:

`USD_FWD = FC_AMT × F0_in`

`USD_FWD` is constant in every sensitivity row.

### 5.2 Money-market hedge

1. Borrow the present value of the receivable in EUR: `EUR_BORROW = FC_AMT / DF_FC`.
2. Convert the borrowed EUR to USD today: `USD_TODAY = EUR_BORROW × S0_in`.
3. Invest the USD to settlement: `USD_MM = USD_TODAY × DF_USD`.

The maturity receivable repays the EUR borrowing. The workbook calculates `PARITY_GAP = USD_MM − USD_FWD` and `PARITY_CHECK = ABS(PARITY_GAP) <= 1.00`; $1 is the base-case rounding tolerance on a $20 million exposure.

### 5.3 EUR put option

`PREMIUM_TOTAL = PREM_PUT × FC_AMT`

`FV_PREMIUM_PUT = PREMIUM_TOTAL × DF_USD`

For each sensitivity-row settlement rate `S_T`, the put pays `MAX(0, K_PUT − S_T) × FC_AMT`. Net settlement proceeds are:

`USD_PUT(S_T) = S_T × FC_AMT + MAX(0, K_PUT − S_T) × FC_AMT − FV_PREMIUM_PUT`

Equivalently, `USD_PUT(S_T) = MAX(S_T, K_PUT) × FC_AMT − FV_PREMIUM_PUT`. This creates a post-premium floor while retaining higher USD proceeds if EUR appreciates. For completeness only, a EUR call on a payable would cap cost at `MIN(S_T, K_CALL) × FC_AMT + FV_PREMIUM_CALL`; it is not included in the receivable winner decision.

### 5.4 Unhedged baseline and summary outputs

For each row, `USD_NO_HEDGE(S_T) = S_T × FC_AMT`. The gray summary cells must be named exactly: `USD_NO_HEDGE_BASE`, `USD_FWD`, `USD_MM`, `USD_PUT_BASE`, `USD_PUT_FLOOR`, `PARITY_GAP`, and `PARITY_CHECK`. Base values are evaluated where `S_T = S0_in`; `USD_PUT_FLOOR` is the minimum option result across the sensitivity grid.

## 6. Sensitivity plan and outputs

Create eleven rates from `0.95 × S0_in` through `1.05 × S0_in` in 1% increments: `S_T(n) = S0_in × (1 + n × 1%)`, for integer `n = −5…+5`. The row with `n = 0` is explicitly labeled **Baseline**.

The gray **Sensitivity Results** table has columns `S_T`, `USD_NO_HEDGE`, `USD_FWD`, `USD_MM`, `USD_PUT`, `HEDGE_VALUE_FWD`, `HEDGE_VALUE_MM`, `HEDGE_VALUE_PUT`, and `OVERALL_WINNER`. Hedge value equals strategy proceeds less `USD_NO_HEDGE`; `OVERALL_WINNER` returns the highest USD-proceeds label among No Hedge, Forward, Money Market, and Put.

Include one line chart titled **USD Proceeds by EUR/USD Settlement Rate** with `S_T` on the x-axis and USD proceeds on the y-axis, one series per strategy. It must let the CFO see the unhedged exposure slope, the certainty of forward/MM proceeds, the put’s net floor, and where retained option upside outweighs its premium.

## 7. Validation rules — Stage 3 audit checklist

- `PARITY_CHECK` passes; `ABS(PARITY_GAP) <= 1.00` using unrounded formulas.
- `USD_FWD` and `USD_MM` are constant throughout the sensitivity grid.
- `USD_NO_HEDGE` increases by `FC_AMT ×` the row-to-row change in `S_T`.
- `USD_PUT` is continuous at `S_T = K_PUT`; below strike it equals `K_PUT × FC_AMT − FV_PREMIUM_PUT`, and above strike it rises one-for-one with `S_T`.
- No cells display `#REF!`, `#DIV/0!`, `#VALUE!`, `#N/A`, or circular-reference warnings; no output is hard-coded.
- All editable inputs are named exactly as §2; every gray output and every sensitivity result is formula-driven and uses the stated USD-per-EUR convention.

## 8. Stage-4 data and decision handoff

Stage 4 replaces each indicative market input with same-date, matching-tenor live data and retains the source evidence. Treasury then reviews the gray Strategy Summary, Sensitivity Results, parity check, and chart alongside liquidity, credit, accounting, and policy constraints before recommending a hedge.
