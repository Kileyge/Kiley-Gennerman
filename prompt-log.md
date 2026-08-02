# AI Prompt Log — FX Transaction Hedging Project

**Student:** Kiley Gennerman  
**Course:** FIN 321 International Business Finance  
**Scenario:** U.S. aerospace manufacturer — €20,000,000 EUR receivable due in one year

This log records how AI assistance was used as a drafting, model-building, and validation aid. Each output was reviewed, corrected where necessary, and retained only after it was reconciled to the project requirements and workbook controls.

## Stage 2 — Technical specification

**Purpose:** Produce a buildable design document before creating the workbook.

**Prompt:**

> Draft a 2–3 page technical specification for Scenario 4: a U.S. aerospace manufacturer with a €20,000,000 EUR receivable due in one year. Follow the Stage 2 requirements: standardized named ranges, tab architecture, forward/money-market/put-option calculation flow, sensitivity plan, exact gray outputs, and validation checks. Mark all market values as indicative pending Stage 4 live data.

**Output used:** `docs/specs/2026-07-30-gennerman-scenario4-eur-receivable-spec.md`

**Human review and revision:** The initial money-market description was too general to be audited. It said, “Compare the money-market result with the forward for parity.” It was revised to state the exact three-step sequence—borrow `FC_AMT / DF_FC`, convert at `S0_in`, invest at `DF_USD`—and the test `ABS(PARITY_GAP) <= 1.00`. This made the calculation flow and acceptance threshold reproducible for the Stage 3 builder.

## Stage 3 — Workbook build and audit

**Purpose:** Create the formula-driven workbook from the specification and audit the result.

**Prompt:**

> Build an Excel workbook exactly from `2026-07-30-gennerman-scenario4-eur-receivable-spec.md`. Include every required named range, all specified tabs, formula-driven forward, three-step money-market, put and call calculations, an eleven-row sensitivity table and line chart, and visible validation checks. Apply the yellow/blue/green/gray convention.

**Output used:** `models/builds/2026-07-30-gennerman-scenario4-eur-receivable-model.xlsx`

**Human review and audit fixes:**

1. The indicative 1.0750 forward was inconsistent with the stated ACT/360 inputs and failed the parity control. It was replaced with the full CIP value in the Stage 2 design, then later replaced with dated Stage 4 data.
2. The put-floor output initially pointed to the wrong sensitivity range. It was relinked to the `USD_PUT` column.
3. The Cover status cell pointed to the wrong checks cell. It was relinked to the live overall-model-status output.
4. Formula descriptions were evaluating instead of displaying as documentation. They were converted to literal text while the actual calculation cells stayed formula-driven.
5. The call was initially visible only at the base spot. An eleven-row call reference schedule was added so its payoff and payable outlay recalculate at each `S_T` scenario.

**Audit evidence:** `analysis/2026-07-30-gennerman-build-audit.md` and the workbook’s **Audit Summary** tab.

## Stage 4 — Market-data population

**Purpose:** Replace placeholders with dated market inputs and test the model with live-data proxies.

**Prompt:**

> Populate the Scenario 4 EUR receivable workbook with dated, reputable EUR/USD spot and reference-rate data; compute a CIP-implied one-year forward if an executable free forward quote is unavailable; retain the assignment option premia; document sources, dates, proxy logic, and validation results.

**Human review and sourcing decisions:**

- Used the ECB EUR/USD reference rate of 1.1389 USD/EUR.
- Used the FRED 1-year U.S. Treasury CMT of 4.06% as the USD reference-rate proxy.
- Used the 2.25% ECB deposit facility as the EUR reference-rate proxy, explicitly noting that it is an overnight policy rate rather than a one-year funding quote.
- Computed `F0_in = 1.1593342407` through the specified ACT/360 CIP formula because an accessible executable one-year EUR/USD forward was unavailable.
- Retained the $0.0300-per-EUR put and call premiums as scenario assumptions, as directed.

**Output used:** `data/2026-08-02-gennerman-market-data.md`

**Validation result:** Forward and money-market proceeds reconciled at $23,186,684.81; the parity gap was $0.00 within the workbook’s $1 tolerance; all workbook checks passed. The course lab was not available in the working environment, so the memo records an independent arithmetic reconciliation and a clear follow-up instruction.

## Stage 5 — Independent analysis and executive recommendation

**Purpose:** Recalculate the outcomes from the specification and market-data memo, compare them with the workbook, and make a CFO recommendation.

**Prompt:**

> Using only the attached FX hedge model technical specification and market-data memo, independently calculate the EUR receivable hedge outcomes. Evaluate no hedge, forward, money-market hedge, EUR put option, and the EUR call payable-reference outcome at the low, base, and high settlement-spot scenarios. Explain assumptions, identify ambiguities, and recommend a hedge for the CFO. Do not rely on a workbook or any prior result.

**Human review and conclusion:** The independent analysis reconciled all tested forward, money-market, put, call-reference, and unhedged outcomes to the populated workbook with zero differences. The review also identified specification improvements: define a hierarchy for rate proxies, distinguish a dealer forward from a CIP-implied forward, and label the call more prominently as payable-reference only.

**Outputs used:**

- `analysis/2026-08-02-gennerman-scenario4-eur-receivable-validation.md`
- `docs/decisions/2026-08-02-gennerman-scenario4-eur-receivable-hedge-recommendation.md`

**Recommendation:** Sell the €20,000,000 receivable forward. It locks approximately $23.187 million of USD proceeds, matches the money-market hedge economically without its borrowing/investment mechanics, and is preferable to paying the put premium when budget certainty is the primary objective.

## Responsible-use statement

AI accelerated drafting, calculation review, and documentation. The student remained responsible for verifying market-data choices, testing formulas, documenting limitations, interpreting trade-offs, and approving the final recommendation. This project is an educational analysis and not authorization to execute a financial transaction.
