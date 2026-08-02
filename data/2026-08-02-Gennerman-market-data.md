# Stage 4 Market-Data Memo — Scenario 4 EUR Receivable

| Field | Value loaded | Unit | Market-data date / retrieval | Source and rationale |
|---|---:|---|---|---|
| `FC_AMT` | 20,000,000 | EUR | Scenario fact | Contractual EUR receivable from Scenario 4; unchanged. |
| `T_DAYS` | 365 | Days | Scenario fact | One-year settlement horizon from Scenario 4; unchanged. |
| `S0_in` | 1.1389 | USD per EUR | ECB reference rate dated 2026-07-27; retrieved 2026-08-02 (America/Denver) | [ECB EUR/USD reference rate](https://www.ecb.europa.eu/stats/policy_and_exchange_rates/euro_reference_exchange_rates/html/index.en.html). ECB quotes USD per EUR, matching the model convention. |
| `R_USD` | 4.06% | Annual % | U.S. 1-year CMT dated 2026-07-08; retrieved 2026-08-02 (America/Denver) | [FRED DGS1](https://fred.stlouisfed.org/series/DGS1/), sourced from the Federal Reserve H.15 release. Chosen as the available 1-year U.S. government benchmark. |
| `R_FC` | 2.25% | Annual % | ECB policy rate effective 2026-06-17 and confirmed unchanged 2026-07-23; retrieved 2026-08-02 (America/Denver) | [ECB monetary-policy decision](https://www.ecb.europa.eu/press/pr/date/2026/html/ecb.mp260723~29f24d99bc.en.html). The ECB deposit facility is used as an EUR reference-rate proxy because a free, matching-tenor EUR government yield was not reliably available. |
| `F0_in` | 1.1593342407 | USD per EUR | Computed 2026-08-02 | **CIP-implied forward**: `S0_in × (1 + R_USD × T_DAYS/360) / (1 + R_FC × T_DAYS/360)`. A free executable 1-year EUR/USD forward was not available; this preserves the model’s stated parity convention. |
| `K_PUT` | 1.1400 | USD per EUR | Set 2026-08-02 | Near-ATM strike, rounded from the 1.1389 live spot in line with Scenario 4 convention. |
| `K_CALL` | 1.1400 | USD per EUR | Set 2026-08-02 | Near-ATM strike, rounded from the 1.1389 live spot; retained for the payable-reference call schedule. |
| `PREM_PUT` | 0.0300 | USD per EUR | Scenario assumption | Retained as directed: retail-accessible EUR option quotes are unreliable for this exercise. |
| `PREM_CALL` | 0.0300 | USD per EUR | Scenario assumption | Retained as directed: retail-accessible EUR option quotes are unreliable for this exercise. |

## Rate and forward methodology

`R_USD` and `R_FC` are intentionally documented as **reference-rate proxies**, not dealer funding quotes. The U.S. leg uses the 1-year Treasury constant-maturity yield; the EUR leg uses the ECB deposit facility rate. The quoted data dates are the latest reliably accessible observations as of this Stage 4 retrieval. The model’s ACT/360 simple-interest convention is unchanged.

Using these inputs, the CIP-implied `F0_in` is 1.1593342407. This is 0.0320064432 USD/EUR (about 2.84%) above the Stage 3 indicative forward of 1.1273277974, principally because the live spot is higher and the selected USD/EUR rate differential differs from the Stage 3 placeholder assumptions.

## Model population and validation

The workbook’s yellow input cells were populated with the values above; formulas, named ranges, and day-count assumptions were not altered. The live Controls / Checks results are:

| Test | Result |
|---|---:|
| CIP forward and money-market proceeds | $23,186,684.81 each |
| Parity gap | $0.00 (within $1 tolerance) |
| No-hedge USD value at live spot | $22,778,000.00 |
| Put net proceeds at live spot | $22,175,301.67 |
| Formula-error scan | No errors found |

## FX Hedging Lab cross-check

The course FX Hedging Lab could not be reached from the current environment. As an independent arithmetic check, I recomputed the forward, money-market, and put formulas from the live named inputs above: forward and money-market proceeds both equal $23,186,684.81, while the at/near-spot put outcome equals $22,175,301.67 after the future-valued premium. These match the populated workbook. Before submission, enter the same values into the course lab if it becomes available and append any variance here; the expected variance is $0.00 under its stated ACT/360/CIP logic.
